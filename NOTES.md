# Mixscribe — notes

My working notes: what I want, what's decided, what's still up in the air. None of it is set in stone.

## What I want it to do

1. **After a set:** a tracklist with timestamps in YouTube format (`00:00 Artist – Title`), with links. Plus help for the promo and the YouTube video.
2. **Recognize tracks:** in old recordings where I don't know what I played, and for tracks I hear while raving.
3. **During a set** (not sure yet): recording, a live tracklist page, maybe something showing in VR.

It'll probably grow beyond that. It's meant to be a DJ sidekick, not just an "after the set" thing.

## Ground rules

- **Your own sets.** If there's ever a live tracklist for a whole club night, it only shows what each DJ chose to share. No ripping other people's sets.
- **Real data first.** If the DJ software already knows what was played, use that. Recognizing tracks by ear is the backup plan.
- **Go easy on the services we use.** A few requests here and there, not a flood.
- **Your stuff stays on your machine.** Libraries, play history, recordings: none of that goes in this repo (see `.gitignore`).
- **Works everywhere.** People in VRChat are mostly on Windows, DJ software often runs on a Mac, and I build this on Linux.

## Where the data comes from

### rekordbox history (best option for your own sets)

- rekordbox remembers every track you play and when, in its library file (`master.db`).
- Since rekordbox 6 that file is encrypted. The key is public though, and [`pyrekordbox`](https://pypi.org/project/pyrekordbox/) (MIT) can open it.
- Always work on a **copy**, never on the real file while rekordbox is running.
- **Need to check first:** do tracks played from Spotify & co show up in the history? And how big is the gap between hitting record and the first track?
- There's already a paid app that does part of this, *Now Playing*. It reads the same history and exports YouTube tracklists (but that seems to be a Pro feature).

### Track recognition (backup plan, old sets, tracks at parties)

- The only free way is what [SongRec](https://github.com/marin-m/SongRec) (GPL-3.0) does: compute Shazam's audio fingerprint on your machine and send **just the fingerprint** to Shazam (never the audio). SongRec leans on the EU right to interoperability, since there's no official Shazam app on Linux.
- In Python: [`shazamio`](https://pypi.org/project/shazamio/) (MIT), which uses [`shazamio-core`](https://github.com/shazamio/shazamio-core) (MIT, written in Rust).
- It's **unofficial**: it can break any day if Shazam changes something, and you get throttled if you overdo it. Hence "go easy".
- When Shazam finds a match, it also gives the track's Shazam link and where in the track the match is.
- The official options (ShazamKit, ACRCloud, AudD) cost money or don't run on Windows.
- Expect gaps: transitions, edits and unreleased tracks often come back as "no idea".

## Decided

| Date | What |
|---|---|
| 2026-10-17 | Name: **Mixscribe**. The scribe of the mix: it listens, figures out what's playing, writes it down. |
| 2026-10-17 | **Open source, GPL-3.0.** Anything we depend on has to be compatible (MIT is fine). |
| 2026-10-17 | The main repo is self-hosted. github.com/flrnce-me/mixscribe is a mirror. |

### How I picked the name (2026-10-17)

Dropped: *Outro* (too narrow), *Crate* (already an open-source DJ library app, plus crates.app and Crate Hackers), *Cuepilot* (a real company makes live TV software called that, and there's a Serato tool too), *Trackflow* (576 projects on GitHub), *Tracktrace* (sounds like parcel tracking).

*Mixscribe*: no trademark in TMview (EU, France, US and around 70 other offices), no active product, free on GitHub, PyPI and npm. Someone ran a transcription API on `mixscribe.io` in 2023–2024 but it's dead now, and `mixscribe.com` is just parked for sale.

## Still open

- What exactly happens *during* a set? Recording, live tracklist, something in VR?
- Does the after-set part run on the computer with rekordbox, or on a copy of its library somewhere else?
- How far does the promo part go? Just text? Visuals? Putting the video together?
- Do streamed tracks show up in the rekordbox history?
