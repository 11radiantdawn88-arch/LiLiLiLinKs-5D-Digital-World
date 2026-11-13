#!/usr/bin/env bash
set -euo pipefail

BASE="/opt/lililinks-5d"
echo "=== Installing LiLiLinKs 5D Ecosystem into $BASE ==="

mkdir -p "$BASE"/{keys,auth,store/{catalog,login,privacy,terms},mail/app,meet/{lobby,room}}

# 1. Generate RS256 & DKIM Keys
openssl genpkey -algorithm RSA -out "$BASE/keys/rs256_private.pem" -pkeyopt rsa_keygen_bits:2048 2>/dev/null
openssl rsa -pubout -in "$BASE/keys/rs256_private.pem" -out "$BASE/keys/rs256_public.pem" 2>/dev/null
openssl genpkey -algorithm RSA -out "$BASE/keys/dkim_lili2026.pem" -pkeyopt rsa_keygen_bits:2048 2>/dev/null
openssl rsa -pubout -in "$BASE/keys/dkim_lili2026.pem" -out "$BASE/keys/dkim_lili2026_pub.pem" 2>/dev/null
chmod 600 "$BASE"/keys/*.pem

# 2. Write Environment & Manifest Configs
cat <<'EOF' > "$BASE/.env.production"
APP_ENV=production
ECOSYSTEM_DOMAIN=lililinks.net
JWT_PRIVATE_KEY_PATH=/etc/lililinks/keys/rs256_private.pem
JWT_PUBLIC_KEY_PATH=/etc/lililinks/keys/rs256_public.pem
AUTH_PORT=8082
MAIL_HOST=mail.lililinks.net
DKIM_PRIVATE_KEY_PATH=/etc/lililinks/keys/dkim_lili2026.pem
MAIL_STORAGE_ENCRYPTION_KEY=$(openssl rand -hex 32)
MEET_WS_PORT=8443
TURN_SHARED_SECRET=$(openssl rand -hex 64)
EOF

cat <<'EOF' > "$BASE/lililinks-manifest.json"
{
  "ecosystem": "LiLiLinKs 5D",
  "version": "1.0.0-release",
  "distribution": "independent-web-store",
  "modules": [
    { "id": "lililinks-store", "name": "LiLiLinKs 5D App Store", "entry": "/store/index.html", "auth_scope": "core.identity.read", "status": "production-ready" },
    { "id": "lilimail", "name": "LILIMAIL", "entry": "/mail/app/index.html", "protocol": "SMTP/IMAP-Encrypted-Relay", "auth_scope": "mail.send, mail.sync", "status": "production-ready" },
    { "id": "lilimeet", "name": "LILIMEET", "entry": "/meet/lobby/index.html", "protocol": "WebRTC-Mesh-SFU", "auth_scope": "meet.room.create, meet.stream", "status": "production-ready" }
  ],
  "security": { "ssl_enforced": true, "zero_tracking_telemetry": true, "session_token": "JWT-RS256" }
}
EOF

cat <<'EOF' > "$BASE/lilimail-transport.yaml"
service: lilimail-core-relay
version: 1.0.0
network:
  bind_host: "0.0.0.0"
  submission_port: 587
  implicit_tls_port: 465
  imap_tls_port: 993
transport_security:
  min_tls_version: "1.3"
  require_starttls: true
authentication:
  dkim_signing: true
  dkim_selector: "lili2026"
  spf_enforcement: "strict"
  dmarc_policy: "reject"
privacy_filters:
  strip_client_ip_headers: true
  strip_user_agent: true
EOF

cat <<'EOF' > "$BASE/lilimeet-rtc.json"
{
  "service": "lilimeet-rtc-gateway",
  "signaling": { "protocol": "wss", "endpoint": "/meet/ws/signal", "heartbeat_interval_ms": 15000, "room_ttl_minutes": 240 },
  "ice_servers": [{ "urls": ["stun:stun.lililinks.net:3478"] }],
  "media_constraints": { "audio_codec": "opus", "video_codecs": ["VP9", "H264"], "e2ee_insertable_streams": true }
}
EOF

# 3. Write Caddyfile & Docker Compose
cat <<'EOF' > "$BASE/Caddyfile"
lililinks.net, www.lililinks.net {
    encode zstd gzip
    header {
        Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "no-referrer"
        Permissions-Policy "camera=(self), microphone=(self), geolocation=()"
    }
    handle /auth/* { reverse_proxy lililinks-auth:8082 }
    handle /store/* { reverse_proxy lililinks-store:80 }
    handle /mail/* { reverse_proxy lililinks-store:80 }
    handle /meet/ws/* { reverse_proxy lilimeet-signal:8443 }
    handle /meet/* { reverse_proxy lililinks-store:80 }
    handle { redir /store/index.html 302 }
}
EOF

cat <<'EOF' > "$BASE/docker-compose.yml"
services:
  caddy-gateway:
    image: caddy:2-alpine
    restart: unless-stopped
    ports: ["80:80", "443:443", "443:443/udp"]
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    depends_on: [lililinks-auth, lililinks-store, lilimeet-signal]
    networks: [lililinks_net]

  lililinks-auth:
    image: node:22-alpine
    restart: unless-stopped
    working_dir: /app
    command: node server.js
    env_file: .env.production
    volumes:
      - ./auth/server.js:/app/server.js:ro
      - ./keys:/etc/lililinks/keys:ro
      - auth_data:/var/lib/lililinks
    networks: [lililinks_net]

  lililinks-store:
    image: nginx:alpine
    restart: unless-stopped
    volumes:
      - ./store:/usr/share/nginx/html/store:ro
      - ./mail:/usr/share/nginx/html/mail:ro
      - ./meet/lobby:/usr/share/nginx/html/meet/lobby:ro
      - ./meet/room:/usr/share/nginx/html/meet/room:ro
      - ./lililinks-manifest.json:/usr/share/nginx/html/store/lililinks-manifest.json:ro
    networks: [lililinks_net]

  lilimeet-signal:
    image: node:22-alpine
    restart: unless-stopped
    working_dir: /app
    command: sh -c "npm install --no-save ws && node server.js"
    env_file: .env.production
    volumes:
      - ./meet/server.js:/app/server.js:ro
      - ./lilimeet-rtc.json:/etc/lilimeet/rtc.json:ro
    networks: [lililinks_net]

networks:
  lililinks_net:
    driver: bridge
volumes:
  caddy_data:
  caddy_config:
  auth_data:
EOF

# 4. Write Backend Node.js Servers (Auth & Meet Signaling)
cat <<'EOF' > "$BASE/auth/server.js"
import http from 'node:http';
import fs from 'node:fs';
import crypto from 'node:crypto';

const PORT = process.env.AUTH_PORT || 8082;
const PRIVATE_KEY = fs.readFileSync(process.env.JWT_PRIVATE_KEY_PATH || '/etc/lililinks/keys/rs256_private.pem', 'utf8');
const USERS_FILE = '/var/lib/lililinks/users.json';

if (!fs.existsSync('/var/lib/lililinks')) fs.mkdirSync('/var/lib/lililinks', { recursive: true });
if (!fs.existsSync(USERS_FILE)) fs.writeFileSync(USERS_FILE, '{}');

const b64u = (i) => Buffer.from(i).toString('base64url');
function signJWT(payload) {
  const h = b64u(JSON.stringify({ alg: 'RS256', typ: 'JWT' }));
  const b = b64u(JSON.stringify({ ...payload, iat: Math.floor(Date.now() / 1000), exp: Math.floor(Date.now() / 1000) + 86400 }));
  const s = crypto.createSign('RSA-SHA256').update(`${h}.${b}`).sign(PRIVATE_KEY, 'base64url');
  return `${h}.${b}.${s}`;
}

function hashPw(pw, salt = crypto.randomBytes(16).toString('hex')) {
  return `${salt}:${crypto.scryptSync(pw, salt, 64).toString('hex')}`;
}

function checkPw(pw, stored) {
  const [salt, key] = stored.split(':');
  return crypto.timingSafeEqual(Buffer.from(key, 'hex'), crypto.scryptSync(pw, salt, 64));
}

http.createServer((req, res) => {
  if (req.method !== 'POST') return res.writeHead(405).end();
  let raw = '';
  req.on('data', (c) => { raw += c; });
  req.on('end', () => {
    let data;
    try { data = JSON.parse(raw); } catch { return res.writeHead(400).end(); }
    const handle = String(data.handle || '').toLowerCase().trim();
    const password = String(data.password || '');
    if (!handle || password.length < 8) {
      res.writeHead(400, { 'Content-Type': 'application/json' });
      return res.end(JSON.stringify({ error: 'Handle required and password must be 8+ characters.' }));
    }
    const users = JSON.parse(fs.readFileSync(USERS_FILE, 'utf8'));
    if (req.url === '/auth/register') {
      if (users[handle]) {
        res.writeHead(409, { 'Content-Type': 'application/json' });
        return res.end(JSON.stringify({ error: 'Handle already claimed.' }));
      }
      users[handle] = { password: hashPw(password), created: Date.now() };
      fs.writeFileSync(USERS_FILE, JSON.stringify(users, null, 2));
    } else if (req.url === '/auth/login') {
      if (!users[handle] || !checkPw(password, users[handle].password)) {
        res.writeHead(401, { 'Content-Type': 'application/json' });
        return res.end(JSON.stringify({ error: 'Invalid credentials.' }));
      }
    } else return res.writeHead(404).end();

    const token = signJWT({ sub: `${handle}@lililinks.net` });
    res.writeHead(200, {
      'Content-Type': 'application/json',
      'Set-Cookie': `lililinks_session=${token}; HttpOnly; Secure; SameSite=Strict; Path=/; Max-Age=86400`
    });
    res.end(JSON.stringify({ ok: true }));
  });
}).listen(PORT);
EOF

cat <<'EOF' > "$BASE/meet/server.js"
import { WebSocketServer } from 'ws';
const wss = new WebSocketServer({ port: process.env.MEET_WS_PORT || 8443, path: '/meet/ws/signal' });
const rooms = new Map();

wss.on('connection', (ws) => {
  let room = null;
  ws.on('message', (raw) => {
    let msg; try { msg = JSON.parse(raw); } catch { return; }
    if (msg.type === 'join' && typeof msg.room === 'string') {
      room = msg.room.slice(0, 64);
      if (!rooms.has(room)) rooms.set(room, new Set());
      rooms.get(room).add(ws);
      ws.send(JSON.stringify({ type: 'joined', peers: rooms.get(room).size }));
    } else if (room && rooms.has(room)) {
      for (const peer of rooms.get(room)) if (peer !== ws && peer.readyState === 1) peer.send(JSON.stringify(msg));
    }
  });
  ws.on('close', () => {
    if (room && rooms.has(room)) {
      rooms.get(room).delete(ws);
      if (rooms.get(room).size === 0) rooms.delete(room);
    }
  });
});
EOF

# 5. Print Deployment Summary & DKIM Key
PUB_KEY=$(grep -v "PUBLIC KEY" "$BASE/keys/dkim_lili2026_pub.pem" | tr -d '\n')
echo ""
echo "=== Core files and keys created in $BASE ==="
echo "1. Add this TXT record at lili2026._domainkey.lililinks.net:"
echo "   v=DKIM1; k=rsa; p=$PUB_KEY"
echo "2. Copy your HTML pages into store/, mail/app/, meet/lobby/, and meet/room/"
echo "3. Start the live ecosystem: cd $BASE && docker compose up -d"
chmod +x install-all-lililinks.sh && sudo ./install-all-lililinks.sh
# LiLiLiLinKs-5D-Digital-World
- name: chat-gpt-code-review
  uses: ARezaK/chat-gpt-code-review@v2
delete