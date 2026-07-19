# Music Box

A persistent, site-wide mini music player. Pin it to the bottom of the
screen on any site and it follows visitors from page to page, playing a
YouTube (or YouTube Music) playlist — no API key required, since it runs
entirely on YouTube's IFrame Player API.

Extracted from seanpfinn.com, where it plays on every page.

## Features

- Album art, title, and artist appear as soon as a track is cued — no need
  to press play first.
- Full skip-back / play-pause / skip-forward transport controls.
- Scrubbable playback progress bar with a live time label; click/tap
  anywhere on it to seek.
- A drag handle beneath the progress bar — swipe up (or tap) to open an
  "Up Next" panel listing the next few tracks in the playlist, each with
  its own art/title/artist, clickable to jump straight to it. Swipe down
  (or tap again) to close it.
- Shuffles the playlist: each page load walks the tracks in a fresh random
  order — next/previous and end-of-track auto-advance all follow it — starting
  on a random track with no flash of track 1 beforehand.
- Ticker/marquee for song titles that don't fit — scrolls out and back once
  per 25s cycle, not continuously.
- Automatically strips YouTube's auto-generated "Artist - Topic" channel
  suffix, and falls back to splitting "Artist - Title" out of the video
  title when YouTube doesn't report a channel name.
- Loads cued and ready to go, but never autoplays — playback only starts
  when the visitor presses play.
- On mobile: after 10 seconds idle, slides most of the way off-screen,
  leaving the album art peeking in as a handle. Swipe left (or tap the
  peek) to bring it back, swipe right to park it again — at any time, not
  just once the timer's fired.
- A light and a dark visual variant (see Theming below).
- Fixed 360px pill on desktop, fills the width within a margin on mobile.

## Setup

1. Copy `miniplayer.css` and `miniplayer.js` into your project.
2. Link the stylesheet and script on whichever page(s) you want the bar on
   (or in a shared layout/partial, to have it follow visitors everywhere):

   ```html
   <link rel="stylesheet" href="miniplayer.css" />
   ...
   <script src="miniplayer.js"></script>
   ```

3. Open `miniplayer.js` and set `YT_PLAYLIST_ID` to your playlist's ID —
   find it in the playlist's share URL:

   ```
   https://music.youtube.com/playlist?list=PLxxxxxxxxxxxxxxxx
                                            ^^^^^^^^^^^^^^^^^^ this part
   ```

   YouTube Music playlists share the same catalog/IDs as youtube.com, so
   either kind of playlist URL works.

That's it — no build step, no dependencies, no API key. `index.html` in
this folder is a minimal working demo (currently pointed at a sample
playlist) if you want to see it running before wiring it into your project.

## Theming

The widget ships with two color variants:

- **Light pill** (default) — translucent white glass, dark text. Reads well
  on a light page.
- **Dark pill** — translucent black glass, white text. Activates under a
  `.dark` class on `<html>` (or any ancestor of `.miniplayer`).

If your project already toggles a `.dark` class for its own dark mode, the
widget's theme will follow it automatically. If not, it'll just always use
the light variant — harmless, nothing to configure. If your project uses a
different convention (e.g. `[data-theme="dark"]`), find-and-replace `.dark`
in `miniplayer.css`.

The font defaults to Geist with a system-font fallback
(`var(--font, 'Geist', -apple-system, BlinkMacSystemFont, sans-serif)`) —
define a `--font` custom property on your page to override it, or just
edit the fallback list directly.

## Notes / constraints

- **Single instance.** The widget looks for `#miniplayer` before creating
  itself, so including the script twice (or on multiple pages that share a
  layout) won't double it up — but it only supports one player per page.
- **Shuffle is done client-side.** YouTube's own `setShuffle()` and
  array-cueing are unreliable for a playlist loaded by ID (it keeps playing
  in canonical order, and a playlist doesn't fire an end-of-track event to
  intercept), so the widget reads the playlist's video IDs once, keeps its
  own shuffled order of them, and plays single videos it controls —
  advancing through that order on next/previous and when each track ends. It
  holds off showing metadata until the first shuffled track is cued, so
  there's no flash of track 1.
- **Title/artist metadata comes from noembed.com** (a free, no-API-key
  oEmbed proxy) whenever YouTube's own `getVideoData()` hasn't filled in
  yet, which is normal immediately after cueing a track. If noembed.com is
  ever unreachable, the title/artist just won't populate until playback
  starts and YouTube's own data becomes available.
- The mobile breakpoint is `50.5625rem` (809px) — change the `@media` query
  in `miniplayer.css` (and the matching `isMobile()` check in
  `miniplayer.js`) if you want a different cutoff.
