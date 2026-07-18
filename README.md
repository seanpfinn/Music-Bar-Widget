# Music Bar

A persistent, site-wide mini music player. Pin it to the bottom of the
screen on any site and it follows visitors from page to page, playing a
YouTube (or YouTube Music) playlist — no API key required, since it runs
entirely on YouTube's IFrame Player API.

Extracted from seanpfinn.com, where it plays on every page.

## Features

- Album art, title, artist, and full skip-back / play-pause / skip-forward
  transport controls.
- Autoplay that actually works around browser policy: starts muted (which
  every browser allows), then unmutes itself on the visitor's first tap,
  click, or keypress anywhere on the page.
- Random starting track on each page load.
- Ticker/marquee for song titles that don't fit — scrolls out and back once
  per 25s cycle, not continuously.
- Automatically strips YouTube's auto-generated "Artist - Topic" channel
  suffix, and falls back to splitting "Artist - Title" out of the video
  title when YouTube doesn't report a channel name.
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
- **Autoplay-with-sound is blocked by every browser** without a prior user
  gesture on the page. There's no way around this in JS — starting muted
  and unmuting on first interaction (already handled) is the standard
  workaround and gets you as close to "just plays" as policy allows.
- **No way to know a playlist's length up front.** The IFrame API doesn't
  expose playlist metadata before loading it once, which is why random
  track selection works by loading the playlist, reading its length off
  the first response, then reloading at a random index — you'll see a very
  brief flash of track 1's info before it settles on the random pick.
- The mobile breakpoint is `50.5625rem` (809px) — change the `@media` query
  in `miniplayer.css` (and the matching `isMobile()` check in
  `miniplayer.js`) if you want a different cutoff.
