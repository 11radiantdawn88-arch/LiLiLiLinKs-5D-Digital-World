https://lovable.dev/invite/H67TEY5# LiLiLinKs 5D Digital World — working prototype

Open `index.html` locally or deploy the contents of this folder using Netlify's manual/drag-and-drop deploy. No GitHub workflow is required.

## Included interactive pages
- `index.html` — 5D hub, module launchpad, LiLiTune synthesized soundscapes, and platform previews.
- `mail.html` — local email composition preview; does not send mail.
- `account.html` — LiLiID profile preview; does not create/authenticate accounts.
- `store.html` — sample marketplace listings; no checkout.
- `games.html` — local single-player Nebula Runner mini-game.
- `holomeet.html` — LiLiMeet 5D room concept preview; no live calls or hardware holograms.
- `glow.html` — LiLiGlow search launcher using an external search engine.
- `watch.html` — LiLiWatch local video playback preview; no upload or streaming.

## Honest production status
This is a static front-end prototype. It does not include a production backend, user authentication, mailbox/domain hosting, email delivery, real-time meetings, volumetric capture, multiplayer, payment processing, affiliate attribution, video uploads/streaming, or production browser privacy/security. These require configured providers, server-side code, credentials, policies, and testing. Do not place secrets in these static files.

## Netlify manual deploy (no GitHub)
1. Sign in to Netlify and open the manual deploy / deploy manually area.
2. Upload the contents of this folder (or the ZIP if the upload interface accepts ZIP archives).
3. Wait for Netlify to finish publishing and test the resulting site URL.
4. Connect a custom domain only after it is purchased and its DNS is configured.

No deployment has been performed from this workspace.<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>5D App Store &amp; Marketplace | LiLiLinKs 5D Digital World</title>
  <style>
    :root {
      --bg-dark: #070913;
      --bg-panel: rgba(20, 27, 51, 0.65);
      --cyan: #00f0ff;
      --purple: #b86bff;
      --green: #34d399;
      --text-main: #ffffff;
      --text-muted: #cbd5e1;
      --text-dim: #94a3b8;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      background-color: var(--bg-dark);
      background-image:
        radial-gradient(circle at top left, rgba(0, 240, 255, 0.12), transparent 45%),
        radial-gradient(circle at bottom right, rgba(184, 107, 255, 0.14), transparent 45%);
      color: var(--text-main);
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      line-height: 1.6;
      min-height: 100vh;
    }

    /* Navigation */
    .nexus-nav {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 1rem 2rem;
      background: rgba(7, 9, 19, 0.85);
      backdrop-filter: blur(12px);
      border-bottom: 1px solid rgba(0, 240, 255, 0.15);
    }

    .nav-brand {
      font-weight: 800;
      font-size: 1.15rem;
      letter-spacing: 0.05em;
      background: linear-gradient(90deg, #ffffff, var(--cyan));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-decoration: none;
    }

    .nav-controls {
      display: flex;
      align-items: center;
      gap: 1.25rem;
    }

    .nav-controls a {
      color: var(--text-muted);
      text-decoration: none;
      font-size: 0.9rem;
      font-weight: 600;
      transition: color 0.2s;
    }

    .nav-controls a:hover {
      color: var(--cyan);
    }

    .user-chip {
      font-size: 0.8rem;
      padding: 0.3rem 0.75rem;
      border-radius: 999px;
      background: rgba(184, 107, 255, 0.15);
      border: 1px solid rgba(184, 107, 255, 0.4);
      color: #e9d5ff;
      font-weight: 600;
    }

    .audio-toggle {
      background: rgba(0, 240, 255, 0.1);
      border: 1px solid rgba(0, 240, 255, 0.35);
      color: var(--cyan);
      padding: 0.35rem 0.75rem;
      border-radius: 999px;
      font-size: 0.78rem;
      font-weight: 600;
      cursor: pointer;
    }

    /* Main Container */
    .store-container {
      max-width: 1140px;
      margin: 0 auto;
      padding: 3rem 1.5rem 5rem;
    }

    .store-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
      flex-wrap: wrap;
      gap: 1.5rem;
      margin-bottom: 2.5rem;
    }

    .badge {
      display: inline-block;
      padding: 0.3rem 0.85rem;
      margin-bottom: 0.75rem;
      font-size: 0.72rem;
      font-weight: 700;
      letter-spacing: 0.14em;
      color: var(--cyan);
      border: 1px solid rgba(0, 240, 255, 0.4);
      border-radius: 999px;
      background: rgba(0, 240, 255, 0.08);
    }

    .store-header h1 {
      font-size: clamp(2rem, 4vw, 2.8rem);
      background: linear-gradient(90deg, #ffffff, var(--cyan), var(--purple));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .store-header p {
      color: var(--text-dim);
    }

    /* Filter Tabs */
    .filter-bar {
      display: flex;
      gap: 0.65rem;
      flex-wrap: wrap;
    }

    .filter-btn {
      padding: 0.5rem 1rem;
      border-radius: 8px;
      border: 1px solid rgba(255, 255, 255, 0.15);
      background: rgba(20, 27, 51, 0.6);
      color: var(--text-muted);
      font-size: 0.85rem;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.2s;
    }

    .filter-btn.active,
    .filter-btn:hover {
      border-color: var(--cyan);
      color: var(--cyan);
      background: rgba(0, 240, 255, 0.1);
    }

    /* Product Grid */
    .store-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
      gap: 1.5rem;
    }

    .item-card {
      background: var(--bg-panel);
      border: 1px solid rgba(0, 240, 255, 0.18);
      border-radius: 16px;
      padding: 1.75rem;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      transition: transform 0.25s ease, border-color 0.25s ease, box-shadow 0.25s ease;
    }

    .item-card:hover {
      transform: translateY(-5px);
      border-color: var(--purple);
      box-shadow: 0 12px 28px rgba(184, 107, 255, 0.18);
    }

    .item-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
    }

    .item-icon {
      font-size: 2.2rem;
    }

    .item-cat {
      font-size: 0.7rem;
      font-weight: 700;
      letter-spacing: 0.12em;
      color: #a5b4fc;
      text-transform: uppercase;
    }

    .item-card h3 {
      font-size: 1.25rem;
      margin-bottom: 0.5rem;
    }

    .item-card p {
      font-size: 0.92rem;
      color: var(--text-muted);
      margin-bottom: 1.5rem;
    }

    .item-footer {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding-top: 1rem;
      border-top: 1px solid rgba(255, 255, 255, 0.08);
    }

    .item-price {
      font-weight: 700;
      color: var(--cyan);
      font-size: 0.95rem;
    }

    .btn-claim {
      padding: 0.55rem 1.15rem;
      border-radius: 8px;
      border: none;
      background: linear-gradient(135deg, var(--cyan), #7c3aed);
      color: #050811;
      font-weight: 700;
      font-size: 0.85rem;
      cursor: pointer;
      transition: transform 0.2s, opacity 0.2s;
    }

    .btn-claim:hover {
      transform: translateY(-1px);
    }

    .btn-claim.owned {
      background: rgba(52, 211, 153, 0.15);
      color: var(--green);
      border: 1px solid rgba(52, 211, 153, 0.4);
      cursor: default;
    }

    @media (max-width: 650px) {
      .nav-controls a {
        display: none;
      }
    }
  </style>
</head>
<body>

  <!-- Navigation -->
  <nav class="nexus-nav">
    <a href="index.html" class="nav-brand">← Back to 5D Nexus</a>
    <div class="nav-controls">
      <a href="account.html">LiLiID &amp; Mail</a>
      <span id="navUserChip" class="user-chip">🌐 Explorer_01</span>
      <button id="audioToggle" class="audio-toggle">🔊 LiLiTune: ON</button>
    </div>
  </nav>

  <!-- Main Content -->
  <main class="store-container">
    <div class="store-header">
      <div>
        <span class="badge">5D APP STORE • BETA PREVIEW</span>
        <h1>5D Store &amp; Digital Assets</h1>
        <p>Unlock spatial apps, interface themes, and avatar modules for your LiLiID.</p>
      </div>

      <div class="filter-bar">
        <button class="filter-btn active sonic-trigger" data-filter="all">All Assets</button>
        <button class="filter-btn sonic-trigger" data-filter="apps">5D Apps</button>
        <button class="filter-btn sonic-trigger" data-filter="themes">Themes</button>
        <button class="filter-btn sonic-trigger" data-filter="wearables">Wearables</button>
      </div>
    </div>

    <div class="store-grid" id="storeGrid">
      <!-- Item 1 -->
      <article class="item-card" data-category="apps">
        <div>
          <div class="item-top">
            <span class="item-icon">🛸</span>
            <span class="item-cat">5D App</span>
          </div>
          <h3>LiLiMeet Hologram Pack</h3>
          <p>Adds 3D spatial grid environments and low-latency room presets to your LiLiMeet module.</p>
        </div>
        <div class="item-footer">
          <span class="item-price">FREE BETA</span>
          <button class="btn-claim sonic-trigger" data-id="holomeet-pack">Add to LiLiID</button>
        </div>
      </article>

      <!-- Item 2 -->
      <article class="item-card" data-category="themes">
        <div>
          <div class="item-top">
            <span class="item-icon">🌌</span>
            <span class="item-cat">UI Theme</span>
          </div>
          <h3>Quantum Neon Overdrive</h3>
          <p>High-contrast cybernetic glow borders and deep-space parallax grid shaders across all pages.</p>
        </div>
        <div class="item-footer">
          <span class="item-price">FREE BETA</span>
          <button class="btn-claim sonic-trigger" data-id="quantum-theme">Add to LiLiID</button>
        </div>
      </article>

      <!-- Item 3 -->
      <article class="item-card" data-category="wearables">
        <div>
          <div class="item-top">
            <span class="item-icon">👑</span>
            <span class="item-cat">Wearable</span>
          </div>
          <h3>Founder's Astral Aura</h3>
          <p>Exclusive V2.0-Beta holographic badge displayed alongside your LiLiID handle in public rooms.</p>
        </div>
        <div class="item-footer">
          <span class="item-price">0 5D CREDITS</span>
          <button class="btn-claim sonic-trigger" data-id="founder-aura">Add to LiLiID</button>
        </div>
      </article>

      <!-- Item 4 -->
      <article class="item-card" data-category="apps">
        <div>
          <div class="item-top">
            <span class="item-icon">🎧</span>
            <span class="item-cat">5D App</span>
          </div>
          <h3>LiLiTune Synth Studio</h3>
          <p>Expanded Web Audio frequency synthesizer with customizable harmonic hover chords.</p>
        </div>
        <div class="item-footer">
          <span class="item-price">FREE BETA</span>
          <button class="btn-claim sonic-trigger" data-id="lilitune-studio">Add to LiLiID</button>
        </div>
      </article>

      <!-- Item 5 -->
      <article class="item-card" data-category="wearables">
        <div>
          <div class="item-top">
            <span class="item-icon">🛡️</span>
            <span class="item-cat">Wearable</span>
          </div>
          <h3>Zero-Tracker Sentinel Shield</h3>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>LiLiID &amp; Mail | LiLiLinKs 5D Digital World</title>
  <style>
    :root {
      --bg-dark: #070913;
      --bg-panel: rgba(20, 27, 51, 0.65);
      --cyan: #00f0ff;
      --purple: #b86bff;
      --green: #34d399;
      --text-main: #ffffff;
      --text-muted: #cbd5e1;
      --text-dim: #94a3b8;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      background-color: var(--bg-dark);
      background-image:
        radial-gradient(circle at top right, rgba(184, 107, 255, 0.12), transparent 45%),
        radial-gradient(circle at bottom left, rgba(0, 240, 255, 0.1), transparent 45%);
      color: var(--text-main);
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      line-height: 1.6;
      min-height: 100vh;
    }

    /* Navigation Bar */
    .nexus-nav {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 1rem 2rem;
      background: rgba(7, 9, 19, 0.85);
      backdrop-filter: blur(12px);
      border-bottom: 1px solid rgba(0, 240, 255, 0.15);
    }

    .nav-brand {
      font-weight: 800;
      font-size: 1.15rem;
      letter-spacing: 0.05em;
      background: linear-gradient(90deg, #ffffff, var(--cyan));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-decoration: none;
    }

    .nav-controls {
      display: flex;
      align-items: center;
      gap: 1.25rem;
    }

    .nav-controls a {
      color: var(--text-muted);
      text-decoration: none;
      font-size: 0.9rem;
      font-weight: 600;
      transition: color 0.2s;
    }

    .nav-controls a:hover {
      color: var(--cyan);
    }

    .audio-toggle {
      background: rgba(0, 240, 255, 0.1);
      border: 1px solid rgba(0, 240, 255, 0.35);
      color: var(--cyan);
      padding: 0.35rem 0.75rem;
      border-radius: 999px;
      font-size: 0.78rem;
      font-weight: 600;
      cursor: pointer;
    }

    /* Main Layout */
    .portal-container {
      max-width: 1120px;
      margin: 0 auto;
      padding: 3rem 1.5rem 5rem;
    }

    .portal-header {
      margin-bottom: 2.5rem;
    }

    .badge {
      display: inline-block;
      padding: 0.3rem 0.85rem;
      margin-bottom: 0.75rem;
      font-size: 0.72rem;
      font-weight: 700;
      letter-spacing: 0.14em;
      color: var(--cyan);
      border: 1px solid rgba(0, 240, 255, 0.4);
      border-radius: 999px;
      background: rgba(0, 240, 255, 0.08);
    }

    .portal-header h1 {
      font-size: clamp(2rem, 4vw, 2.8rem);
      background: linear-gradient(90deg, #ffffff, var(--cyan), var(--purple));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .portal-header p {
      color: var(--text-dim);
      font-size: 1rem;
    }

    .portal-grid {
      display: grid;
      grid-template-columns: 1fr 1.35fr;
      gap: 2rem;
    }

    @media (max-width: 840px) {
      .portal-grid {
        grid-template-columns: 1fr;
      }
    }

    /* Panels */
    .panel {
      background: var(--bg-panel);
      border: 1px solid rgba(0, 240, 255, 0.2);
      border-radius: 16px;
      padding: 2rem;
      backdrop-filter: blur(10px);
    }

    .panel h2 {
      font-size: 1.3rem;
      margin-bottom: 1.25rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    /* Identity Card Preview */
    .id-card {
      background: linear-gradient(135deg, rgba(0, 240, 255, 0.14), rgba(184, 107, 255, 0.18));
      border: 1px solid rgba(0, 240, 255, 0.45);
      border-radius: 14px;
      padding: 1.5rem;
      margin-bottom: 1.75rem;
      display: flex;
      align-items: center;
      gap: 1.25rem;
    }

    .avatar-orb {
      width: 64px;
      height: 64px;
      border-radius: 50%;
      background: rgba(7, 9, 19, 0.85);
      border: 2px solid var(--cyan);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2rem;
      flex-shrink: 0;
      box-shadow: 0 0 18px rgba(0, 240, 255, 0.3);
    }

    .id-details h3 {
      font-size: 1.2rem;
      color: #ffffff;
    }

    .id-address {
      font-size: 0.85rem;
      color: var(--cyan);
      font-family: monospace;
    }

    .id-status {
      display: inline-block;
      margin-top: 0.35rem;
      font-size: 0.75rem;
      color: var(--green);
      font-weight: 600;
    }

    /* Form Controls */
    .form-group {
      margin-bottom: 1.25rem;
    }

    .form-group label {
      display: block;
      font-size: 0.85rem;
      color: var(--text-muted);
      margin-bottom: 0.4rem;
      font-weight: 600;
    }

    .form-group input,
    .form-group select {
      width: 100%;
      padding: 0.75rem 1rem;
      border-radius: 8px;
      border: 1px solid rgba(255, 255, 255, 0.18);
      background: rgba(7, 9, 19, 0.75);
      color: #ffffff;
      font-size: 0.95rem;
      outline: none;
    }

    .form-group input:focus,
    .form-group select:focus {
      border-color: var(--cyan);
    }

    .btn-save {
      width: 100%;
      padding: 0.85rem;
      border: none;
      border-radius: 8px;
      background: linear-gradient(135deg, var(--cyan), #7c3aed);
      color: #050811;
      font-weight: 700;
      font-size: 0.95rem;
      cursor: pointer;
      transition: transform 0.2s, box-shadow 0.2s;
    }

    .btn-save:hover {
      transform: translateY(-2px);
      box-shadow: 0 8px 20px rgba(0, 240, 255, 0.3);
    }

    .save-feedback {
      margin-top: 0.75rem;
      font-size: 0.85rem;
      color: var(--green);
      text-align: center;
      min-height: 1.25rem;
    }

    /* Inbox Section */
    .mail-list {
      display: flex;
      flex-direction: column;
      gap: 0.85rem;
    }

    .mail-item {
      padding: 1rem 1.25rem;
      border-radius: 10px;
      background: rgba(7, 9, 19, 0.65);
      border: 1px solid rgba(255, 255, 255, 0.1);
      cursor: pointer;
      transition: border-color 0.2s, transform 0.2s;
    }

    .mail-item:hover {
      border-color: var(--purple);
      transform: translateX(4px);
    }

    .mail-meta {
      display: flex;
      justify-content: space-between;
      font-size: 0.78rem;
      color: var(--cyan);
      margin-bottom: 0.3rem;
    }

    .mail-subject {
      font-weight: 700;
      font-size: 0.98rem;
      color: #ffffff;
      margin-bottom: 0.25rem;
    }

    .mail-snippet {
      font-size: 0.88rem;
      color: var(--text-dim);
    }

    .pill-encrypted {
      font-size: 0.68rem;
      padding: 0.2rem 0.55rem;
      border-radius: 999px;
      background: rgba(52, 211, 153, 0.15);
      color: var(--green);
      border: 1px solid rgba(52, 211, 153, 0.35);
    }
  </style>
</head>
<body>

  <!-- Navigation -->
  <nav class="nexus-nav">
    <a href="index.html" class="nav-brand">← Back to 5D Nexus</a>
    <div class="nav-controls">
      <a href="store.html">5D Store</a>
      <a href="holomeet.html">LiLiMeet</a>
      <button id="audioToggle" class="audio-toggle">🔊 LiLiTune: ON</button>
    </div>
  </nav>

  <!-- Main Content -->
  <main class="portal-container">
    <div class="portal-header">
      <span class="badge">UNIFIED IDENTITY &amp; COMMS</span>
      <h1>LiLiID &amp; 5D Mail</h1>
      <p>Configure your persistent dimensional profile stored locally on your device.</p>
    </div>

    <div class="portal-grid">
      <!-- Left Column: LiLiID Profile -->
      <section class="panel">
        <h2>Your LiLiID Card</h2>

        <div class="id-card">
          <div id="avatarPreview" class="avatar-orb">🌐</div>
          <div class="id-details">
            <h3 id="handlePreview">Explorer_01</h3>
            <div id="emailPreview" class="id-address">explorer_01@lilimail.com</div>
            <div id="statusPreview" class="id-status">● Online in 5D Nexus</div>
          </div>
        </div>

        <form id="profileForm">
          <div class="form-group">
            <label for="handleInput">Nexus Handle</label>
            <input type="text" id="handleInput" placeholder="Enter your handle" maxlength="24" required />
          </div>

          <div class="form-group">
            <label for="avatarSelect">Avatar Badge</label>
            <select id="avatarSelect">
              <option value="🌐">🌐 Dimensional Explorer</option>
              <option value="⚡">⚡ Quantum Architect</option>
              <option value="🔮">🔮 Neural Navigator</option>
              <option value="🚀">🚀 Nebula Runner</option>
              <option value="👑">👑 Nexus Founder</option>
            </select>
          </div>

          <div class="form-group">
            <label for="statusSelect">Presence Status</label>
            <select id="statusSelect">
              <option value="● Online in 5D Nexus">● Online in 5D Nexus</option>
              <option value="🟣 In LiLiMeet Spatial Room">🟣 In LiLiMeet Spatial Room</option>
              <option value="🎮 Playing Nebula Runner">🎮 Playing Nebula Runner</option>
              <option value="🔒 Stealth Mode">🔒 Stealth Mode</option>
            </select>
          </div>

          <button type="submit" class="btn-save sonic-trigger">Save LiLiID Profile</button>
          <div id="saveFeedback" class="save-feedback"></div>
        </form>
      </section>

      <!-- Right Column: 5D Mail Inbox -->
      <section class="panel">
        <h2>
          <span>LiLiMail Inbox</span>
          <span class="pill-encrypted">LOCAL-FIRST • E2E READY</span>
        </h2>

        <div class="mail-list">
          <article class="mail-item sonic-trigger">
            <div class="mail-meta">
              <span>nexus-core@lilimail.com</span>
              <span>V2.0-BETA</span>
            </div>
.dev-hub-section {
  padding: 5rem 1.5rem;
  background: linear-gradient(180deg, #070913 0%, #0d1326 100%);
  color: #ffffff;
  border-top: 1px solid rgba(0, 240, 255, 0.12);
  font-family: system-ui, -apple-system, sans-serif;
}

.dev-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 2rem;
  max-width: 1140px;
  margin: 0 auto;
}

.dev-panel {
  padding: 2rem;
  border-radius: 14px;
  background: rgba(15, 23, 42, 0.75);
  border: 1px solid rgba(184, 107, 255, 0.25);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.dev-panel h3 {
  font-size: 1.35rem;
  margin: 0 0 0.85rem;
  color: #ffffff;
}

.dev-panel p {
  color: #cbd5e1;
  line-height: 1.6;
  margin-bottom: 1.25rem;
}

.spec-list {
  list-style: none;
  padding: 0;
  margin: 0 0 1.75rem;
  color: #94a3b8;
  font-size: 0.92rem;
}

.spec-list li {
  margin-bottom: 0.5rem;
}

.spec-list code {
  color: #00f0ff;
  background: rgba(0, 240, 255, 0.1);
  padding: 0.15rem 0.4rem;
  border-radius: 4px;
}

.pipeline-status {
  display: flex;
  flex-direction: column;
  gap: 0.85rem;
  margin-top: 0.5rem;
}

.status-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.75rem 1rem;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.04);
  font-size: 0.92rem;
  color: #e2e8f0;
}

.status-pill {
  font-size: 0.7rem;
  font-weight: 700;
  padding: 0.25rem 0.6rem;
  border-radius: 999px;
  letter-spacing: 0.08em;
}

.status-pill.active {
  background: rgba(16, 185, 129, 0.15);
  color: #34d399;
  border: 1px solid rgba(52, 211, 153, 0.35);
}

.status-pill.beta {
  background: rgba(184, 107, 255, 0.15);
  color: #d8b4fe;
  border: 1px solid rgba(216, 180, 254, 0.35);
}

.nexus-footer {
  padding: 2.5rem 1.5rem;
  background-color: #05070f;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  text-align: center;
  color: #64748b;
  font-size: 0.9rem;
  font-family: system-ui, -apple-system, sans-serif;
}

.footer-links {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  margin-top: 0.75rem;
}

.footer-links a {
  color: #94a3b8;
  text-decoration: none;
}

.footer-links a:hover {
  color: #00f0ff;
}
<section id="developer-hub" class="dev-hub-section">
  <div class="section-header">
    <span class="badge">OPEN ECOSYSTEM • MPL-2.0</span>
    <h2>Developer Hub &amp; Trust Log</h2>
    <p>Inspect the architecture, verify automated code reviews, and contribute to the 5D codebase.</p>
  </div>

  <div class="dev-grid">
    <!-- Codebase & Licensing -->
    <div class="dev-panel">
      <h3>📦 Open-Source Repository</h3>
      <p>
        LiLiLinKs 5D Digital World is licensed under the <strong>Mozilla Public License 2.0 (MPL-2.0)</strong>. 
        Fork the repository, test new spatial modules locally, or submit pull requests.
      </p>
      <ul class="spec-list">
        <li><strong>Branch Strategy:</strong> <code>main</code> (Production) / <code>dev</code> (Beta)</li>
        <li><strong>Stack:</strong> Modular HTML5, CSS3 Grid, Vanilla ES6+</li>
        <li><strong>Storage:</strong> Local-First State (`nexus-core.js`)</li>
      </ul>
      <a href="https://github.com" 
         target="_blank" rel="noopener" class="btn-secondary">
        Explore Source Code →
      </a>
    </div>

    <!-- CI/CD & Security -->
    <div class="dev-panel">
      <h3>🔒 CI/CD &amp; Data Integrity</h3>
      <p>
        Every commit passes through automated syntax checks and AI-assisted code verification 
        to keep the multi-dimensional launchpad fast and secure.
      </p>
      <div class="pipeline-status">
        <div class="status-row">
          <span>Automated Code Review</span>
          <span class="status-pill active">ACTIVE</span>
        </div>
        <div class="status-row">
          <span>Zero-Tracker Privacy Policy</span>
          <span class="status-pill active">VERIFIED</span>
        </div>
        <div class="status-row">
          <span>Public Beta Release</span>
          <span class="status-pill beta">V2.0-BETA</span>
        </div>
      </div>
    </div>
  </div>
</section>

<footer class="nexus-footer">
  <p>© 2026 LiLiLinKs 5D Digital World • Connected Dimensions</p>
  <div class="footer-links">
    <a href="#dimensions">Dimensions</a>
    <a href="#developer-hub">Developer Hub</a>
    <a href="https://github.com/blob/main/LICENSE" target="_blank" rel="noopener">MPL-2.0 License</a>
  </div>
</footer>
Here is the complete breakdown for Phase 3: Metaverse Integration, along with the ready-to-use HTML/CSS code for your interactive module cards so users can launch directly into each dimension of your platform.

🌌 Phase 3: Metaverse Integration

• Spatial Module Portals: Connect your core views (holomeet.html, glow.html, watch.html, games.html, store.html, and account.html) through a unified interactive card grid on the main landing page.
• LiLiID Profile & Avatar Layer: Build a lightweight front-end identity card in account.html where users can customize their handle, avatar badge, and active dimension status stored locally via nexus-core.js.
• Interactive Environment Previews: Add live CSS hover depth, glowing border telemetry, and sound feedback triggers (LiLiTune) when users preview or enter each module.
• Digital Marketplace & Asset Readiness: Structure store.html with modular item cards for future digital wearables, custom themes, and spatial room upgrades.

🎛️ Interactive Dimension Module Cards (index.html & styles.css)


HTML (index.html — place directly below .nexus-hero)

html
<section id="dimensions" class="dimensions-section">
  <div class="section-header">
    <h2>Connected Dimensions</h2>
    <p>Select a portal below to enter a specialized module of the 5D ecosystem.</p>
  </div>

  <div class="dimensions-grid">
    <a href="holomeet.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">SPATIAL COMMS</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiMeet</h3>
      <p>Next-generation holographic meeting rooms and spatial audio lounges.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="glow.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">DISCOVERY</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiGlow</h3>
      <p>Streamlined neural search launcher engineered for rapid web exploration.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="watch.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">MEDIA STREAM</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiWatch</h3>
      <p>Immersive local media theater with ambient 5D audio-visualizers.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="games.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">ARCADE</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>Nebula Runner</h3>
      <p>Interactive browser arcade challenges and high-score telemetry.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="store.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">MARKETPLACE</span>
        <span class="status-dot">PREVIEW</span>
      </div>
      <h3>5D Store</h3>
      <p>Acquire digital assets, interface themes, and spatial upgrades.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="account.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">IDENTITY</span>
        <span class="status-dot">ACTIVE</span>
      </div>
      <h3>LiLiID & Mail</h3>
      <p>Manage your unified 5D profile, preferences, and encrypted inbox.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>
  </div>
</section>
Use code with caution.

CSS (styles.css)

css
.dimensions-section {
  padding: 5rem 1.5rem;
  background-color: #070913;
  color: #ffffff;
  font-family: system-ui, -apple-system, sans-serif;
}

.section-header {
  text-align: center;
  max-width: 640px;
  margin: 0 auto 3.5rem;
}

.section-header h2 {
  font-size: 2.25rem;
  margin-bottom: 0.75rem;
  background: linear-gradient(90deg, #00f0ff, #b86bff);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.section-header p {
  color: #94a3b8;
  font-size: 1.05rem;
}

.dimensions-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
  gap: 1.5rem;
  max-width: 1140px;
  margin: 0 auto;
}

.dimension-card {
  display: flex;
  flex-direction: column;
  padding: 1.75rem;
  border-radius: 14px;
  background: rgba(20, 27, 51, 0.55);
  border: 1px solid rgba(0, 240, 255, 0.18);
  text-decoration: none;
  color: inherit;
  transition: transform 0.25s ease, border-color 0.25s ease, box-shadow 0.25s ease;
}

.dimension-card:hover {
  transform: translateY(-5px);
  border-color: #00f0ff;
  box-shadow: 0 12px 30px rgba(0, 240, 255, 0.16);
}

.card-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.card-tag {
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  color: #a5b4fc;
}

.status-dot {
  font-size: 0.68rem;
  font-weight: 700;
  padding: 0.2rem 0.55rem;
  border-radius: 999px;
  background: rgba(0, 240, 255, 0.12);
  color: #00f0ff;
}

.dimension-card h3 {
  font-size: 1.35rem;
  margin: 0 0 0.6rem;
  color: #ffffff;
}

.dimension-card p {
  font-size: 0.95rem;
  line-height: 1.55;
  color: #cbd5e1;
  margin: 0 0 1.5rem;
  flex-grow: 1;
}

.portal-link {
  font-size: 0.9rem;
  font-weight: 600;
  color: #00f0ff;
}
Use code with caution.
Would you like to continue with:
Writing the nexus-core.js script to connect state and soundscapes across these cardsMoving forward to Phase 4: Public Beta Launch & Developer Hub
.dimensions-section {
  padding: 5rem 1.5rem;
  background-color: #070913;
  color: #ffffff;
  font-family: system-ui, -apple-system, sans-serif;
}

.section-header {
  text-align: center;
  max-width: 640px;
  margin: 0 auto 3.5rem;
}

.section-header h2 {
  font-size: 2.25rem;
  margin-bottom: 0.75rem;
  background: linear-gradient(90deg, #00f0ff, #b86bff);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.section-header p {
  color: #94a3b8;
  font-size: 1.05rem;
}

.dimensions-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
  gap: 1.5rem;
  max-width: 1140px;
  margin: 0 auto;
}

.dimension-card {
  display: flex;
  flex-direction: column;
  padding: 1.75rem;
  border-radius: 14px;
  background: rgba(20, 27, 51, 0.55);
  border: 1px solid rgba(0, 240, 255, 0.18);
  text-decoration: none;
  color: inherit;
  transition: transform 0.25s ease, border-color 0.25s ease, box-shadow 0.25s ease;
}

.dimension-card:hover {
  transform: translateY(-5px);
  border-color: #00f0ff;
  box-shadow: 0 12px 30px rgba(0, 240, 255, 0.16);
}

.card-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.card-tag {
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  color: #a5b4fc;
}

.status-dot {
  font-size: 0.68rem;
  font-weight: 700;
  padding: 0.2rem 0.55rem;
  border-radius: 999px;
  background: rgba(0, 240, 255, 0.12);
  color: #00f0ff;
}

.dimension-card h3 {
  font-size: 1.35rem;
  margin: 0 0 0.6rem;
  color: #ffffff;
}

.dimension-card p {
  font-size: 0.95rem;
  line-height: 1.55;
  color: #cbd5e1;
  margin: 0 0 1.5rem;
  flex-grow: 1;
}

.portal-link {
  font-size: 0.9rem;
  font-weight: 600;
  color: #00f0ff;
}
<section id="dimensions" class="dimensions-section">
  <div class="section-header">
    <h2>Connected Dimensions</h2>
    <p>Select a portal below to enter a specialized module of the 5D ecosystem.</p>
  </div>

  <div class="dimensions-grid">
    <a href="holomeet.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">SPATIAL COMMS</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiMeet</h3>
      <p>Next-generation holographic meeting rooms and spatial audio lounges.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="glow.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">DISCOVERY</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiGlow</h3>
      <p>Streamlined neural search launcher engineered for rapid web exploration.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="watch.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">MEDIA STREAM</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>LiLiWatch</h3>
      <p>Immersive local media theater with ambient 5D audio-visualizers.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="games.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">ARCADE</span>
        <span class="status-dot">LIVE</span>
      </div>
      <h3>Nebula Runner</h3>
      <p>Interactive browser arcade challenges and high-score telemetry.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="store.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">MARKETPLACE</span>
        <span class="status-dot">PREVIEW</span>
      </div>
      <h3>5D Store</h3>
      <p>Acquire digital assets, interface themes, and spatial upgrades.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>

    <a href="account.html" class="dimension-card">
      <div class="card-top">
        <span class="card-tag">IDENTITY</span>
        <span class="status-dot">ACTIVE</span>
      </div>
      <h3>LiLiID & Mail</h3>
      <p>Manage your unified 5D profile, preferences, and encrypted inbox.</p>
      <span class="portal-link">Enter Portal →</span>
    </a>
  </div>
</section>

