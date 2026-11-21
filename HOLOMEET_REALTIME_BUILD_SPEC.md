# LiLiMeet 5D — Real-Time Meeting Build Specification

## Product direction

LiLiMeet is the spatial collaboration layer of **LiLiLinKs 5D Digital World**. The 5D identity is the experience direction: a layered, responsive room with spatial presence, expressive avatars, shared content, and immersive visual depth. A browser-based visual stage is not a physical hologram; true free-standing projection requires compatible display hardware and a separate hardware integration.

## Current verified baseline

- `holomeet.html` provides the holographic-style room visual and responsive participant cards.
- Camera preview can be enabled locally on a supported HTTPS browser; it is not transmitted.
- Microphone permission can be checked; audio is not transmitted or recorded.
- No signaling service, WebRTC peer connection, live participant join, room authorization API, recording, or physical hologram integration is implemented.
- Supabase schema already defines `meeting_rooms` and `meeting_members`, with RLS enabled and additional policy hardening in `supabase/SECURITY_HARDENING.sql`. The SQL has not been applied to a live project as part of this package verification.

## Target architecture

1. **Identity and authorization — Supabase Auth**
   - Require an authenticated host to create a room.
   - Require a server-verified invite or membership before issuing any media-room credential.
   - Keep service-role keys and provider secrets only in server-side Netlify environment variables.

2. **Room metadata — Supabase Postgres**
   - Use `meeting_rooms` for host, title, scheduled time, and lifecycle state.
   - Use `meeting_members` for explicit membership and host/participant roles.
   - Enforce RLS and server-side checks; never trust a room ID or role supplied by browser code alone.
   - Add auditable invite lifecycle fields and rate limits before public launch.

3. **Media plane — WebRTC through a managed SFU**
   - Select a managed WebRTC SFU/provider after a cost, region, privacy, retention, and SDK review.
   - Use the provider's short-lived room token minted by a Netlify server function after Supabase authorization.
   - Do not expose provider API secrets or mint tokens in the browser.
   - Use an SFU for multi-person rooms rather than a browser mesh, to avoid quadratic upload bandwidth as participants grow.

4. **Signaling and presence**
   - Let the chosen SFU handle media signaling where supported.
   - Use Supabase Realtime or a server endpoint for room presence and non-media events only; presence must not grant access by itself.
   - Reconcile disconnects and stale participants; treat the server as the authority for room state.

5. **5D experience layer**
   - Preserve the current spatial stage, luminous participant identity, and immersive layout.
   - Map real participant states to the stage: connecting, live, muted, camera-off, reconnecting, and disconnected.
   - Provide accessible list/grid alternatives, keyboard-operable controls, reduced-motion support, and mobile layouts.
   - Treat avatar effects as presentation, not as proof of a physical holographic projection.

## Required server endpoints before live meetings

- `create-meeting-room`: authenticated host creates a room; validates title and schedule; records host membership.
- `create-meeting-invite`: host creates a single-use or bounded-use invite with expiry and role.
- `join-meeting`: validates authenticated user and invite/membership, then issues a short-lived SFU token scoped to that room and role.
- `end-meeting`: host ends the room and invalidates future join credentials.
- `meeting-webhook`: verifies provider webhook signatures and reconciles room/participant lifecycle events.

All endpoints must validate input, enforce ownership/membership, rate-limit abuse, avoid leaking whether private room IDs exist, and log security-relevant events without logging media or access tokens.

## Privacy and safety requirements

- Camera and microphone remain off until the user explicitly enables each device.
- Show a persistent live/muted/camera state and a clear leave control.
- Explain whether the selected provider processes media, where it is processed, and its retention policy.
- Recording and transcription remain disabled unless separately designed with explicit consent, participant notice, and retention/deletion controls.
- Never store raw media in the application database.
- Provide host controls to remove participants and close a room.
- Use short-lived credentials and rotate/revoke them on room end or membership removal.

## Release acceptance gates

### Gate A — secure room lifecycle
- [ ] Authenticated host can create and end a room.
- [ ] Non-members cannot read private room metadata or obtain media tokens.
- [ ] Expired, revoked, and reused invites are rejected according to policy.
- [ ] RLS tests cover host, invited member, unrelated authenticated user, and anonymous user.

### Gate B — real-time media
- [ ] Two separate devices can join the same room and exchange audio/video.
- [ ] Three or more participants work through the SFU without mesh fan-out.
- [ ] Mute, camera toggle, device selection, leave, and host removal work.
- [ ] Reconnect and network interruption behavior is tested on mobile and desktop.
- [ ] Provider webhooks are signature-verified and idempotent.

### Gate C — 5D experience quality
- [ ] Live states appear accurately on the spatial stage.
- [ ] Layout remains usable on narrow mobile screens and desktop.
- [ ] Keyboard navigation, screen-reader labels, contrast, and reduced-motion behavior pass review.
- [ ] No visual state implies a participant is connected when they are not.

### Gate D — production readiness
- [ ] Privacy notice, terms, provider DPA, data-region, and retention review are complete.
- [ ] Load, latency, abuse, and failure-mode tests pass.
- [ ] Monitoring and incident response are configured.
- [ ] End-to-end tests pass in a staging environment before production credentials are enabled.

## Immediate next implementation sequence

1. Choose the SFU provider based on the decision criteria above; do not hard-code a provider before the account, pricing, and privacy review.
2. Apply and test the existing Supabase schema and security hardening in a development project.
3. Implement room lifecycle and invite authorization endpoints.
4. Implement server-side short-lived media-token issuance.
5. Integrate the provider SDK into `holomeet.html`, retaining the current 5D stage as the presentation layer.
6. Run two-device and multi-participant staging tests before describing LiLiMeet as live.

**Release status:** specification only. No live meeting service is claimed or enabled by this document.
