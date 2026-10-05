# StudyingChat

A free-to-host group chat: Google sign-in, real-time rooms, no message filter. It is a static site (`index.html`) that talks to several free Firebase projects at once and fails over between them automatically.

## Features
- Google sign-in, one tap on return visits
- Rooms (`general`, `random`, `memes` plus your own; friends type the same room name to join)
- Replies, emoji reactions, edit and delete your own messages
- Image links embed inline, links are clickable, `**bold**`, `@mentions` highlight
- Sound and desktop alerts, three themes, works on phones
- Live server status (live, standby, down) in the sidebar

## Setup (about 15 minutes, all free)
1. Create 2 or 3 projects at https://console.firebase.google.com (Spark plan, no card needed).
2. In each project: **Authentication > Sign-in method > Google > Enable**.
3. In each project: **Firestore Database > Create database**, then **Rules** and paste `firestore.rules`.
4. In each project: **Project settings > Your apps > Web app** and copy the config.
5. Paste each config into the `BACKENDS` list at the top of the script in `index.html`.
6. Deploy the folder free on GitHub Pages or Cloudflare Pages.
7. In every project add your site's domain under **Authentication > Settings > Authorized domains** (for example `yourname.github.io`).

## How failover works
- Messages are written to the first healthy server. If a write is rejected (quota hit, outage), the same message is retried on the next server immediately. Nobody sees a reload or a gap.
- Every client listens to all servers and merges messages by time, so people on different servers still see one conversation, and old history stays readable after a switch.
- A server that errors is skipped for 15 minutes, then tried again. Free quotas reset daily.
- Reads run out before writes, because every message is read by every user. More projects means more headroom.

## Notes
- Anyone with a Google account can sign in. To limit it to friends, add an allowlist check in `firestore.rules`.
- The Firebase config values are not secrets; the rules are what protect your data.
