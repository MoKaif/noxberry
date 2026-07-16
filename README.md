<div align="center">

<img src="data/icons/128x128/strawberry.png" width="120" alt="NoxBerry">

# NoxBerry

**A music player for people who care about their collection — rebuilt around a modern interface.**

A personal fork of [Strawberry](https://github.com/strawberrymusicplayer/strawberry) (itself a fork of *Clementine*).
C++17 · Qt 6 · GStreamer

</div>

---

## :crystal_ball: What this fork is trying to achieve

Strawberry is an outstanding music player. Its feature depth — bit-perfect playback,
tag editing, ReplayGain/EBU R128, device sync, transcoding — is genuinely hard to
find anywhere else, and none of that is in question here.

What it *doesn't* have is a modern interface. The UI still carries a lot of its
Clementine/Amarok lineage: glossy gradients, dense spacing, small artwork, and a
sidebar that looks like it belongs to a different decade.

**NoxBerry keeps every bit of Strawberry's power and gives it the interface that
power deserves.** The target is the visual quality of Spotify, Apple Music, Arc and
Zen — without giving up a single feature to get there.

A second, longer-term goal: **make the collection smarter**. Strawberry knows what's
in your library; it doesn't know that a track also exists on YouTube and Spotify, or
that it belongs on a late-night playlist. That's the second half of this fork.

### Design goals

| | |
| --- | --- |
| **Modern desktop feel** | Quality on par with Spotify, Apple Music, Arc, Zen |
| **Space & rhythm** | Clean spacing, consistent padding, rounded corners |
| **Navigation** | A real, modern sidebar |
| **Content-first** | Card-based layouts, large album artwork |
| **Hierarchy** | Typography that tells you what matters |
| **Adaptability** | Responsive layouts, light/dark aware |
| **Key surfaces** | A better queue panel and a better Now Playing |

### The rule that makes this safe

> **The UI changes. The app does not.**

Every redesign phase holds to this:

- No changes to playback, database, playlist or application behaviour.
- All signals, slots, actions, shortcuts and menus keep working.
- Nothing is removed; the architecture is preserved.
- Presentation stays separated from business logic — the QSS theme first, then
  light `.ui` tweaks, then presentation-only paint code. Playback code is not touched.

This is a fork of a mature, working application. A redesign that breaks it is a
failure, no matter how good it looks.

---

## :art: Identity

NoxBerry is a **user-facing rename only**, and that distinction is deliberate.

- **Renamed:** window title, application display name, About dialog, app icon,
  launcher entry.
- **Deliberately NOT renamed:** the binary name, config directory, collection
  database, MPRIS D-Bus service, `.desktop` id, and every class name in the source.

Why: renaming those breaks config continuity, desktop integration and IPC, and buys
nothing visually. It also keeps the diff against upstream small enough to keep
merging their work.

**Accent colour — Berry Violet `#7c3aed`** (nox = night, plus berry). Neutral
surfaces still follow your system palette; only the accent is fixed, so the app
stays adaptive while keeping an identity.

---

## :white_check_mark: Status

### Done

**Phase 1 — Theme**
- A palette-aware, dark-first QSS theme: rounded menus, inputs and cards, slim
  scrollbars, consistent padding throughout.
- Now Playing: larger artwork (300px), rounded cover corners, real typography
  hierarchy.
- Tidier queue and player-control spacing.

**Phase 2 — Branding & sidebar**
- NoxBerry identity: title, display name, About dialog, launcher entry.
- New icon — a violet berry with a night ("nox") crescent and leafy crown.
- Berry Violet accent across selection, focus, pressed and progress surfaces.
- Sidebar: the glossy white active gradient is gone, replaced with a rounded violet
  pill and a bright label. The sidebar background is now a neutral panel colour
  instead of the saturated system highlight.

### Next — Phase 3: Layout

Where the biggest visual wins are. Each is a separate, reviewable slice:

- **Album browser** — card grid, large artwork, proper hover affordances
- **Now Playing** — richer layout, bigger art, clearer metadata
- **Queue** — better rows, drag affordance, clear now-playing marker
- **Search** — modern field, grouped results, real empty states
- **Statistics** — an actual view instead of plain text

These need presentation-only item delegates and custom paint work. Models, queries
and behaviour still stay untouched.

### Later — A smarter collection

Genuinely new features. These relax the "UI only" rule and get their own design pass:

- **Enriched track data** — a PostgreSQL side database seeded from a spreadsheet,
  carrying extra columns such as **YouTube URL** and **Spotify URL** for the same
  song, joined to the existing SQLite collection without disturbing it.
- **Mood / genre playlists** — auto playlists driven by that enriched data.
- **Plugins** — deeper Last.fm integration and similar.

See [ROADMAP.md](ROADMAP.md) for the full plan and the locked-in decisions.

---

## :rocket: Running it

> **The command is `strawberry`, not `noxberry`.**
> The binary name is one of the internal identifiers this fork intentionally keeps.
> In your application launcher it appears as **NoxBerry**.

```bash
strawberry
```

## :wrench: Build from source

```bash
git clone --recursive https://github.com/MoKaif/noxberry
cd noxberry
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel $(nproc)
sudo cmake --install build          # installs to /usr/local
```

> **Use `-DCMAKE_BUILD_TYPE=Release` for a build you actually use.** This project
> does not default a build type, and an unset one produces **no optimisation flags at
> all**. `Release` also disables debug-output spam.

Developed and tested on **Linux** (Arch). The fork's changes are UI-level, so upstream's
BSD/macOS/Windows support should carry over — but it isn't tested here.

### Requirements

**Core:** CMake 3.13+ · a C++17 compiler · pkg-config · Boost · GLib · Qt 6.4+ (Core,
Concurrent, Gui, Widgets, Network, SQL, D-Bus) · SQLite 3.9+ · ALSA (Linux) ·
GStreamer · TagLib 1.12+ · ICU · [KDSingleApplication](https://github.com/KDAB/KDSingleApplication) 1.1.0+

**Optional:** Chromaprint (fingerprinting) · FFTW3 (fast spectrum moodbar) ·
PulseAudio · libcdio (audio CD) · libmtp (MTP devices) · libgpod (iPod Classic) ·
libebur128 (EBU R128 normalization)

Also install GStreamer plugins **base**, **good**, and optionally **bad**, **ugly**
and **libav** for full codec support.

### Regenerating the icon

Master SVGs live in `data/icons/svg/`. See [ROADMAP.md](ROADMAP.md#regenerating-the-icon).

---

## :white_check_mark: Inherited features

Everything Strawberry does, NoxBerry does — this fork removes nothing:

- Play and organize your music collection
- WAV, FLAC, Ogg FLAC, WavPack, Ogg Vorbis, Opus, Ogg Speex, MPC, TrueAudio, AIFF,
  MP4/AAC, ALAC, MP3, ASF, Monkey's Audio, and DSD (DSF/DSDIFF)
- Bit-perfect playback on Linux
- MPRIS2 / D-Bus remote control, native desktop notifications, global shortcuts
- Advanced playlist management, smart and dynamic playlists
- Audio analyzer, equalizer, moodbar, and waveform seek bar
- ReplayGain and EBU R128 loudness normalization
- Tag editing, and missing tags via [AcoustID](https://acoustid.org/) / [MusicBrainz](https://musicbrainz.org/)
- Cover art and lyrics from a wide range of providers
- Transcoding, USB/MTP/iPod transfer, audio CD playback
- Scrobbling to [Last.fm](https://www.last.fm/), [ListenBrainz](https://listenbrainz.org/), Subsonic
- Internet radio, Subsonic streaming, unofficial Tidal/Spotify/Qobuz integration
- Discord Rich Presence

---

## :heart: Upstream

NoxBerry exists because Strawberry is worth building on. All of the hard engineering
underneath this fork is the work of **Jonas Kvinge** and the Strawberry and Clementine
contributors.

If you find NoxBerry useful, **support upstream** — that's where the real work happens:

[Patreon](https://www.patreon.com/jonaskvinge) · [GitHub Sponsors](https://github.com/sponsors/jonaski) · [Ko-fi](https://ko-fi.com/jonaskvinge) · [PayPal](https://paypal.me/jonaskvinge)

- **Upstream repo:** https://github.com/strawberrymusicplayer/strawberry
- **Website:** https://www.strawberrymusicplayer.org
- **Wiki:** https://wiki.strawberrymusicplayer.org
- **Forum:** https://forum.strawberrymusicplayer.org

> **Please don't report NoxBerry issues to upstream.** This is an unaffiliated personal
> fork. Anything broken here is this fork's problem, not theirs.

---

## :page_facing_up: License

GPL-3.0-or-later, same as Strawberry. See [COPYING](COPYING).
