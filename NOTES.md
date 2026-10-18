# Mixscribe — notes

What we've tried and learned so far. Nothing here is set in stone, and a lot of it is "we tried this, it half worked".

## The problem

Two cases that look alike but aren't:

1. **You know what was played** (a playlist) but not **when** each track starts. You want a tracklist with timestamps for a YouTube description (`00:00 Artist – Title`).
2. **You know nothing.** An old recording, or a track you heard at a party. The tool has to recognize it from the audio.

Both matter. Plenty of DJs pick tracks as the set goes, with no playlist made beforehand, so case 2 can't go away.

## Ground rules

- **Your own sets.** No ripping other people's tracklists. Lots of DJs don't share theirs, and that's their call.
- **Go easy on the services we use.** A few requests here and there, not a flood.
- **Your stuff stays on your machine.** Libraries, play history, recordings: none of that goes in this repo (see `.gitignore`).
- **No getting around copy protection.** We don't touch streams, decrypt anything, or tell people how.
- **Honest output.** Every timestamp says how sure we are: recognized, guessed, or no idea.
- **Works everywhere.** People in VRChat are mostly on Windows, DJ software often runs on a Mac, and some of us build on Linux.

## What we tried, and what happened

Tested on two real sets of about an hour each: a dubstep/riddim set (26 tracks) and a J-core/speedcore set (19 tracks).

| What we tried | What happened |
|---|---|
| **Shazam on random 12 s clips** | Dubstep set: 13 of 20 clips recognized. J-core set: 7 of 20, and they were only 3 different tracks out of 19. Genre matters a lot. |
| **Placing tracks when the order is known:** a clip every 45 s, then zoom in between two recognized neighbours to find the switch | Dubstep set: 19 of 26 tracks placed. 7 are within about 6 s, 12 are estimates, 7 not placed at all. |
| **Clips every 15 s inside the gaps** | Nothing new. If Shazam can't hear a track, a closer look doesn't help. |
| **Guessing from track lengths:** add up the playlist's durations | Drifts. Mixed tracks play about 15% shorter than their full length (anywhere between 70% and 100% depending on the stretch). Fine as a first guess, not as a rule. |
| **Spotting where the sound changes** (timbre, chroma, spectral contrast) to find transitions | No better than chance. On the dubstep set it found 3 of 6 known transitions within 10 s, but there's a change roughly every 25 s anyway. |

## Hard cases

- **Long blends.** When two tracks overlap for 30–60 s, "the moment it starts" is fuzzy. A few seconds to tens of seconds of slack is normal.
- **Edits, bootlegs, pitched or stretched tracks, unreleased tracks.** Often come back as "no idea".
- **Very distorted genres.** Shazam struggles (the J-core set).
- **False positives.** Shazam sometimes names a track that isn't there (a pop hit over a bass-heavy part, for example). If the order is known, a hit that doesn't fit the list is probably wrong.
- **Same tempo everywhere.** In some genres a whole stretch sits around 175 BPM, so tempo can't tell neighbours apart. Tempo is also often half or double what you'd expect (100 vs 200).
- **Python 3.14.** `shazamio-core` crashes on import there. Use 3.12.

## Where the data comes from

### A playlist export (CSV)

- Exports from tools like Exportify give artist, title, order, **duration**, and tempo / key / energy.
- Handy for guessing where a track should start, and for knowing how many there are.
- The playlist may hold a track or two at the end that never got played.

### rekordbox history

- rekordbox remembers every track you play and when, in its library file (`master.db`).
- Since rekordbox 6 that file is encrypted. The key is public though, and [`pyrekordbox`](https://pypi.org/project/pyrekordbox/) (MIT) can open it.
- Always work on a **copy**, never on the real file while rekordbox is running.
- **Not checked:** do tracks played from a streaming service show up in the history?

### Track recognition

- The only free way is what [SongRec](https://github.com/marin-m/SongRec) (GPL-3.0) does: compute Shazam's audio fingerprint on your machine and send **just the fingerprint** to Shazam (never the audio). SongRec leans on the EU right to interoperability, since there's no official Shazam app on Linux.
- In Python: [`shazamio`](https://pypi.org/project/shazamio/) (MIT), which uses [`shazamio-core`](https://github.com/shazamio/shazamio-core) (MIT, written in Rust).
- It's **unofficial**: it can break any day if Shazam changes something, and you get throttled if you overdo it. Hence "go easy".
- When Shazam finds a match, it also gives the track's Shazam link.
- The official options (ShazamKit, ACRCloud, AudD) cost money or don't run on Windows.

### Other people's work

We found research but no finished open-source tool. The serious methods compare the mix against the **audio files of the tracks**, which you often don't have (streaming): [Kim et al., ISMIR 2020](https://arxiv.org/abs/2008.10267) (mix-to-track alignment, [code](https://github.com/mir-aidj/djmix-analysis)) and [Sonnleitner et al., 2016](https://research.jku.at/en/publications/landmark-based-audio-fingerprinting-for-dj-mix-monitoring/) (fingerprints for DJ mixes, with a dataset).

## Ideas we like

- **A marker key.** Press a key (or a MIDI button) at every new track while you play. Perfect timestamps, no recognition needed.
- **A tiny player to fix timestamps.** Jumps to the guessed moment, you nudge it by ear.
- **Guess, then correct.** Guess starts from the track lengths, check a few Shazam samples around each guess, carry on from the corrected point. Not tested as a full loop yet.

## Decided

| Date | What |
|---|---|
| 2026-10-17 | Name: **Mixscribe**. The scribe of the mix: it listens, figures out what's playing, writes it down. |
| 2026-10-17 | **Open source, GPL-3.0.** Anything we depend on has to be compatible (MIT is fine). |
| 2026-10-17 | The main repo is self-hosted. github.com/flrnce-me/mixscribe is a mirror. |

## Still open

- Can the 30 s previews that some music sites offer act as references for tracks Shazam can't hear? Not tried.
- Which genres does recognition work on, and which not? Two sets isn't enough to say.
- What does a good "I'm not sure" look like in the output, so people can fix it fast?
- What should happen during a set, if anything? Recording, a live tracklist, something in VR?
