# Chat Heads Rust — a Horizon native mod

A player's head right before their name on every chat line they wrote, for
[Horizon](https://playhorizon.dev/) — Minecraft: Java Edition 26.3 rewritten in Rust.
Built from the behaviour of dzwdz's Chat Heads (MPL-2.0, 52.5M downloads on Modrinth) and
Domanffe's MIT Horizon port; **no Java code and no original assets**.

**Works for Microsoft accounts and cracked (offline-mode) players alike, with no option:**
the sender is read from the message's own `<Name>` prefix, which offline-mode servers write
identically to online ones — no account, no signed chat, no UUID. The skin is whatever the
game itself resolved for that player from its tab list (Microsoft or cracked), the **hat
layer is drawn over the face** so hair and headwear show, and a player the game has no skin
for keeps a steady default Steve/Alex chosen from their name. The line stays bare only if
the atlas has nothing at all — never a blank box.

## Install (players)

1. Put `dist/chat_heads_rust-1.0.0.hnmod` in the game folder's `horizon-native-mods/`.
2. Options > Mods… → turn **Chat Heads Rust** on.
3. Settings > Native mods (experimental) → **Build** → confirm "I trust these mods: build".
4. Play the **Native mods** instance.

## Settings (Options > Mods > Chat Heads Rust)

- **Chat heads**: ON / OFF
- **Show your own head**: ON / OFF — your name comes from the game itself; `/chatheads me <name>` only overrides it
- 
## Commands

    /chatheads me <name>   tell the mod your name (hides your own head when the setting is off)

## Build from source

    git clone <this repository>
    cd <repo>
    cargo test                                  # sender/geometry/settings logic, on no game
    git clone https://github.com/horizon-rust/horizon-mods
    python3 horizon-mods/native/tools/native.py check /path/to/repo
    python3 horizon-mods/native/tools/native.py package /path/to/repo

Needs Rust stable; for the game check also Python 3.11+, `rsync`, and on Linux
`libasound2-dev`, `libudev-dev`, `pkg-config`. Loader: horizon-mixins bundle — game `26.3`,
hook points API 2 (named hooks), Horizon `4aa6854a886183440256f4f4a4a9d1ed853227a1`,
horizon-mixins `a2af5c0dd4117d51ecd70066ba7a00b4a2c10dab`.

## How sender detection works (and why cracked works)

1. **The line itself.** Vanilla chat renders a player message as `<Name> text` — on cracked
   servers exactly as on premium ones, because offline-mode servers skip only the Mojang
   *authentication*, not the chat format. The mod reads that `<Name>` and accepts it when it
   is a profile name (1–16 of `A-Z a-z 0-9 _`). System lines, `/me`, `Name: text` rewrites and
   wrapped continuations never match, so they get no head.
2. **The skin, both layers.** The game keys resolved skins by name (`name:<lowercase>` in
   its HUD atlas) from the tab list's player info — which offline-mode servers send just
   like online ones. The head draws the face (8,8) **and the hat overlay (40,8) over it**,
   so hair, hats and glasses show. No skin resolved → the steady default; no atlas entry →
   no head.

## Known limits (honest ones)

- Sender detection is **heuristic only** — Horizon's hook points give mods no chat-sender
  UUID. This is the same "Heuristic only" mode dzwdz's Modrinth page recommends for servers
  that hide senders.
- A server that rewrites chat into a non-`<Name>` format (some minigame/plugins) gets no heads.
- Clickable links in a shifted line land 10 px left of their pixels — the chat's hit-test
  cannot be patched by horizon-mixins (`E_UNSUPPORTED_CONTROL_FLOW`), the same limit
  Domanffe's port documents.
- Pinned to Horizon `4aa6854` / mixins `a2af5c0`; the three list/settings/command hooks are
  named (`hook =`), so launcher updates that only move the game's internals still build.

## Credits & license

MIT for everything in this repository. dzwdz's Chat Heads (MPL-2.0) is the original mod;
Domanffe's chatheads port showed the Horizon hook pattern. Neither endorses this port.
