# LiLiLinKs 5D — backend foundation plan

## Identity and data architecture

Use Supabase Auth for LiLiID sign-in (email verification, password reset, optional MFA), PostgreSQL for application records, and row-level security (RLS) for data boundaries. The static website must never contain a service-role key, payment secret, email-provider secret, or other privileged credential.

The accompanying `supabase/schema.sql` is a starter schema for:

- **LiLiID:** user profiles linked to Auth UUIDs; passwords remain with the identity provider.
- **LiLiStore:** public active catalog items, seller-owned drafts, private buyer orders, and immutable order-item title/price snapshots.
- **LiLiMail:** mailbox-owned message metadata only. Actual email delivery and message-body storage require a mail provider and server-side integration.
- **LiLiMeet 5D:** host-owned rooms and participant membership records. Actual calls require WebRTC infrastructure/signaling and authorization.

## Safe setup sequence

1. Create a dedicated Supabase project for development; do not reuse production data while testing.
2. Review `supabase/schema.sql`, then apply it in the Supabase SQL editor.
3. Configure Auth redirect URLs for the eventual Netlify domain and enable email verification.
4. Add only the Supabase project URL and public anon key to the frontend build environment. Never expose the service-role key.
5. Test RLS with two separate test users: each must be unable to read another user's mail metadata or orders, and a seller must not edit another seller's products.
6. Add server-side functions for payment checkout and verified payment webhooks. Do not let browser code mark an order paid.
7. Add a mail provider behind a server-side function; verify sender domains and implement abuse/rate limits.
8. Add a WebRTC provider/signaling service with short-lived room tokens and server-side membership checks.
9. Complete privacy policy, terms, data retention/deletion, logging, backup, accessibility, and security review before public launch.

## Current implementation boundary

This repository package remains a static front-end prototype. The SQL is not applied to a live database, and no Supabase project, identity provider, email provider, payment processor, or meeting provider has been connected. No real user accounts, email delivery, paid orders, or multiplayer calls are active.
