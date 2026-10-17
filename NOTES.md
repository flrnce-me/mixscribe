# Mixscribe — design notes

Working notes: what Mixscribe is meant to do, the decisions taken so far, and the open questions. Nothing here is final.

## Scope

1. **After the live** — a reliable, timestamped tracklist of a set in YouTube description format (`00:00 Artist – Title`), with streaming links; the material for the post-set promo and the YouTube video.
2. **Track recognition** — identify tracks in older recordings that have no play history, and tracks heard while raving.
3. **During the live** *(exploratory)* — set recording; a live tracklist page, possibly shown in VR (VRChat).

The scope is expected to grow. Mixscribe is a DJ companion, not only an "after the set" tool.

## Principles

- **Your own sets first.** Mixscribe documents the user's own music. It must not become a tool to rip full tracklists of other DJs' sets without their consent. Any "live tracklist" feature for a club night only shows tracks a DJ has chosen to share.
- **Exact data beats guessing.** When the DJ software keeps a play history, use it. Audio recognition is the fallback.
- **Light on the services we rely on.** Recognition requests stay occasional and modest.
- **Personal data stays local.** DJ libraries, play histories and recordings never go into the repository (see `.gitignore`).
- **Cross-platform.** VRChat users are mostly on Windows, DJ software often on macOS, development happens on Linux.

## Data sources

### rekordbox play history (preferred for your own sets)

- rekordbox records every track played, with its time, in its local library database (`master.db`).
- Since rekordbox 6, `master.db` is an **encrypted SQLite database** (SQLCipher). The key is publicly known, and [`pyrekordbox`](https://pypi.org/project/pyrekordbox/) (MIT) can read it.
- Work on a **copy** of the database, never on the live file while rekordbox runs.
- **To verify first:** whether tracks played from streaming services (e.g. Spotify) appear in the history; the offset between the start of the recording and the first track.
- Existing alternative: the commercial app *Now Playing* reads the same history and exports YouTube-style tracklists (export seems to require its Pro version).

### Audio recognition (fallback, older sets, tracks heard in clubs)

- The only free route is the approach pioneered by [SongRec](https://github.com/marin-m/SongRec) (GPL-3.0): compute Shazam's audio fingerprint locally and send **only the fingerprint** (never the audio) to Shazam's servers. SongRec relies on the EU interoperability exception (no official Shazam client on Linux).
- In Python: [`shazamio`](https://pypi.org/project/shazamio/) (MIT), built on [`shazamio-core`](https://github.com/shazamio/shazamio-core) (MIT, Rust).
- **Unofficial**: no guarantee, it may break whenever Shazam changes its protocol, and it is rate-limited in practice. Hence the "light" principle.
- A Shazam match also returns the track's Shazam link and the matched offset within the track.
- Official routes (ShazamKit, ACRCloud, AudD) are paid or not available on Windows.
- Limits: transitions, edits and unreleased tracks often come back unmatched.

## Decisions

| Date | Decision |
|---|---|
| 2026-10-17 | Name **Mixscribe** — the scribe of the mix: it listens, identifies and writes down. |
| 2026-10-17 | **Open source, GPL-3.0.** Dependencies must stay GPL-compatible (MIT is fine). |
| 2026-10-17 | Main repository is self-hosted; github.com/flrnce-me/mixscribe is a push mirror. |

### Name research (2026-10-17)

Rejected: *Outro* (too narrow), *Crate* (existing open-source DJ library manager, crates.app, Crate Hackers), *Cuepilot* (commercial live-production software CuePilot, plus a Serato hot-cue tool), *Trackflow* (576 GitHub projects), *Tracktrace* (parcel-tracking jargon).
*Mixscribe*: no trademark found in TMview (EUIPO, INPI, USPTO and ~70 offices); no active product; free on GitHub, PyPI and npm. A defunct transcription API used `mixscribe.io` in 2023–2024; `mixscribe.com` is parked on a domain marketplace.

## Open questions

- What exactly happens *during* the live? (recording, live tracklist, VR overlay…)
- Where does the after-live part run: on the machine running rekordbox, or on a copy of its database elsewhere?
- How far should the promo part go (text only? visuals? video assembly?)
- Do streamed tracks show up in rekordbox history?
