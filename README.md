# T20 Spin Wheel 🏏

A multiplayer (2–6 players) online cricket game in one static `index.html`. Each game draws a fresh wheel from a pool of ~150 international T20 stars. Players take turns spinning, then have 30 seconds to lock the landed player into one of 8 stat slots (or use one of 3 Skips). If time runs out, the player is locked into a random empty slot automatically. The best stat line wins. The UI text is in Hinglish.

## Tech
- Plain HTML/CSS/JS in a single file. No build step, backend, database or login.
- [PeerJS](https://peerjs.com) (jsDelivr CDN, with unpkg as a fallback) for peer-to-peer WebRTC multiplayer. Uses the free PeerJS cloud signalling server.
- `localStorage` for the host's saved room (used by "Room resume karo") and for each player's private picks and 2x choice.

## Run locally
Serve the folder over HTTP, for example `python3 -m http.server`, then open it on several devices. Each player needs their own browser profile, since a browser's saved token identifies its seat. Incognito windows work for local testing.

## Deploy
1. Open https://app.netlify.com/drop
2. Drag this folder (or a zip of it) onto the page.
3. Open the site, click **Naya room banao**, share the `?room=CODE` link, and press **Game shuru karo** once everyone has joined.
