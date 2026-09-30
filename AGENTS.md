# AGENTS.md

## Architecture
- Everything lives in `index.html`: CSS in `<style>` and the whole app in a single `<script>`. The user wants a drag-and-drop Netlify Drop deploy, so keep it as one file with no build step and no dependencies other than the PeerJS CDN script.
- Script sections: DATA (the `RAW` player stats, `STATS`, `LABELS`), HOST, GUEST, SHARED, WHEEL (canvas), RESULTS and RENDER (string templates plus `bind()`).

## Multiplayer model
- Host peer ID is `t20w-<CODE>`. The host is seat 0 and the only source of truth. Guests are seats 1–5 (max 6 players). The host keeps one connection per seat in `conns`, applies actions in `applyAction(seat, a)`, saves to `localStorage["t20w-host"]` (state `v:2`), and sends each guest `{t:"state", s, seat, now}`. `now` lets guests compute clock `skew`.
- A guest sends `{t:"hello", token, name}`. The token maps to a seat through the host-only `state.tokens`, which is stripped before broadcast. New tokens are accepted only in `lobby`. The host can kick players in the lobby and starts the game with `startGame()`, which builds the per-game wheel pool. Actions are `{t:"act", a:"ready"|"spin"|"lock"|"skip"|"name"|"pass"}`. `pass` is host-only and skips an offline player's turn.
- The shared state never contains which stat a player locked or their 2x choice. These stay in `localStorage["t20w-priv-<CODE>-<H|G>"]`. When the phase is `over`, guests send `{t:"final", picks, dbl}`, the host collects them in `priv.all`, and it relays `{t:"finals", all}` to everyone.
- Spins: `state.spin = {seq, idx, jit, segs, item, at}`. Every client animates any new `seq` for 4 s with an ease-out curve. A `setTimeout` makes sure the spin finishes even in background tabs.
- Decision timer: `state.pending.deadline` is host time (spin end + 30 s). At 0 the deciding client auto-locks a random empty slot. After a 4 s grace period, the host force-locks. `state.last` then tells that player's client to fill a random empty slot locally (`reconcileAuto`).
- The DOUBLE SPIN segment (`DS = -1`) stays on the wheel. Landed players are removed. The wheel pool size, `max(60, n*11+8)`, guarantees the wheel never runs out.
- New players go at the end of `RAW` so saved ids stay valid.

## Conventions
- UI text is in Hinglish. The theme uses CSS variables with `prefers-color-scheme`. The layout is mobile-first, with two columns at ≥820px.
- Stats are approximate career T20I figures. `null` means no data, shows as "—", and counts as the worst value in scoring.
