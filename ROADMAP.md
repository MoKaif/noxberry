# NoxBerry Roadmap

NoxBerry is a fork of [Strawberry](https://github.com/strawberrymusicplayer/strawberry)
(itself a fork of Clementine). This file tracks the redesign phases, the
decisions that are locked in, and what is planned next.

## Ground rules

These apply to every UI phase unless a phase explicitly says otherwise:

- Do **not** change playback, database, playlist or application behaviour.
- Keep all signals, slots, actions, shortcuts and menus working.
- Do not remove existing functionality; preserve the architecture.
- Separate presentation from business logic. Prefer, in order:
  1. the QSS theme (`data/style/strawberry.css`),
  2. light `.ui` spacing tweaks,
  3. presentation-only paint code.
- Avoid touching playback code.

## Locked decisions

| Decision | Value | Notes |
| --- | --- | --- |
| Rename depth | **User-facing only** | Display strings + icon only. |
| Accent colour | **Berry Violet `#7c3aed`** (hover `#8b5cf6`) | Fixed brand accent; neutral surfaces stay palette-adaptive. |
| Theme | Adaptive, tuned dark-first | Palette-aware via `%palette-*` tokens. |
| Build type | `Release` for daily use | Sets `-DNDEBUG` and disables debug-output spam. |

### Identifiers that must NOT be renamed

Renaming these breaks config continuity, IPC and desktop integration, and buys
nothing visually. They stay `strawberry` / `Strawberry` on purpose:

- `applicationName`, `organizationName`, `organizationDomain` (`src/main.cpp`)
- the config directory and the collection database location
- the MPRIS D-Bus service name and the single-instance key
- the `.desktop` file id, `Exec`, `TryExec`, `Icon`, `StartupWMClass`
- all C++ class names, namespaces and resource filenames

## Status

### Done — Phase 1: theme (`redesign/mainwindow-phase1`)

- Rewrote `data/style/strawberry.css` as a palette-aware, dark-first theme:
  rounded menus/inputs/cards, slim scrollbars, consistent padding.
- Now Playing: larger artwork (300px), rounded cover corners, typography
  hierarchy via `QTextDocument` span classes.
- Queue and player-control spacing tidied in the `.ui` files.

### Done — Phase 2: branding + sidebar (`redesign/branding-and-sidebar`)

- User-facing rename to **NoxBerry**: window title, application display name,
  About dialog (upstream Strawberry/Clementine credit and sponsor links kept).
- New app icon: violet berry with a night ("nox") crescent and leafy crown.
  Master SVGs in `data/icons/svg/`, rasterized into the existing
  `strawberry.png` / `-grey` / `-faded` assets at every size.
- Berry Violet accent applied to selection, focus, pressed and progress
  surfaces; default buttons are accent-filled.
- Sidebar: glossy white active gradient replaced with a rounded Berry Violet
  pill and bright label; hover is a subtle rounded overlay; sidebar background
  is now a neutral offset window colour instead of the system highlight.
- `.desktop` / AppStream display names rebranded (ids left untouched).

## Next — Phase 3: layout

The biggest visual wins. Each is a separate, reviewable slice:

- **Album browser** — card-based grid, large artwork, hover affordances.
- **Now Playing** — richer layout, bigger art, clearer metadata hierarchy.
- **Queue** — better rows, drag affordance, clearer now-playing marker.
- **Search** — modern field, better result grouping and empty states.
- **Statistics** — a real view rather than plain text.

Risk note: these go beyond pure QSS. Several will need presentation-only
delegates (`QStyledItemDelegate`) or custom paint code. Behaviour, models and
queries stay untouched.

## Future — data and integrations

Genuinely new features, not presentation. These will relax the ground rules and
need their own design pass:

- **PostgreSQL seeding** — a side database of enriched track metadata, seeded
  from a spreadsheet, with extra columns such as YouTube URL and Spotify URL
  for the same song. Open question: how it joins to the existing SQLite
  collection without disturbing it.
- **Mood / genre playlists** — auto playlists driven by the enriched data.
- **Last.fm and similar plugins** — beyond the existing scrobbler.

## Regenerating the icon

```bash
for s in 22 32 48 64 128; do
  rsvg-convert -w $s -h $s data/icons/svg/noxberry.svg -o data/icons/${s}x${s}/strawberry.png
done
rsvg-convert -w 1000 -h 1000 data/icons/svg/noxberry.svg -o data/icons/full/strawberry.png
rsvg-convert -w 512 -h 512  data/icons/svg/noxberry.svg -o data/pictures/strawberry.png
```

The `-grey` (inactive tray) and `-faded` (playlist watermark) variants are built
the same way from `noxberry-grey.svg` and `noxberry-faded.svg`.

## Build

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel $(nproc)
sudo cmake --install build          # installs to /usr/local
```
