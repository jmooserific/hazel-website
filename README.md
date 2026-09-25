# hazelpuzzle.com

Three static pages. Plain HTML and CSS, no build step, no JavaScript, and no
request to any other domain except the one Safari makes for the Smart App
Banner. The privacy page claims that, exception included, so it has to keep
being true. Don't add a font host, an analytics snippet, or a CDN link without
rewriting that page first.

Served by GitHub Pages from the repository root. `CNAME` holds the domain;
`.nojekyll` keeps Jekyll out of the way.

## Routes

| Route | File | Used by |
|---|---|---|
| `/` | `index.html` | — |
| `/privacy/` | `privacy/index.html` | App Store Connect, Privacy Policy URL |
| `/support/` | `support/index.html` | App Store Connect, Support URL |

Directory indexes rather than `privacy.html`, so the extensionless URL works on
GitHub Pages, Cloudflare Pages, Netlify and anything else without host-specific
rewrite rules. `/privacy` (no trailing slash) redirects to `/privacy/`; both are
safe to paste into App Store Connect, and neither should move once they're in
there.

Every internal link is relative, so the site works from any mount point — the
domain root, a `user.github.io/repo/` project page, or a local server pointed at
a parent directory. The only absolute URLs are the two Open Graph tags, which
have to be absolute for a crawler to resolve them, and the outbound links —
the App Store and the six museum pages the gallery credit points at. Outbound
links are not requests: nothing is fetched from those hosts unless a reader
clicks, so the privacy page's claim holds.

## Working on it locally

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000/>. A server rather than `file://`: the routes
are directories, and browsers won't resolve `privacy/` to `privacy/index.html`
over `file://`, so cross-page links break there even though the CSS and images
load fine.

## Where the design came from

Nothing here is an invented palette. The tokens in `assets/site.css` are the
shipped app's, and each is commented with its source:

- Colors, rules and the image radius — `docs/design/design_handoff_home_view/README.md`
  and `Joinery/Home/HomeTray.swift` (`TrayPalette`) in the app repo.
- The accent — the app icon's own gradient in `AppIcon.icon/icon.json`, whose
  lower stop converts from Display P3 to exactly `#FF9500`, which is
  `systemOrange`, which is `AccentColor.colorset`. Light and dark use the
  system's two values for it.
- Dark mode reuses the app's own pairing: the ink becomes the ground, and text
  on it takes the app's "text on felt" colors.

If a token here ever disagrees with the app, the app wins.

The app is called Hazel, but its repo (`../Hazel`) keeps `Joinery` as the
internal name of its targets, module and source folder, so paths into it like
`Joinery/Home/` are correct as written.

## Assets

| File | What it is |
|---|---|
| *(inline in `index.html`)* | A real cut from `PuzzleEngine`, 63 pieces (a 64 target lands on a 7×9 grid), seed 9. Drawn with `stroke="currentColor"` so CSS themes it. Sits in the reading column beside the paragraph that explains it. |
| `assets/og.png` | The same engine and seed, 12 pieces, rendered flat for link previews. Far fewer pieces because a 63-piece cut turns to mush at thumbnail size. |
| `assets/icon.svg`, `assets/apple-touch-icon.png`, `assets/favicon.png` | The app icon: `AppIcon.icon/Assets/icon_piece.svg` at the placement and gradient `icon.json` specifies. |
| *(inline in `index.html`)* | The wordmark, outlined. Same string, face and per-pair kern table as `Joinery/Home/HomeWordmark.swift`. |
| `assets/home-iphone.{webp,jpg}`, `assets/home-ipad.{webp,jpg}` | Home on each device, as a `.devices` pair under the opening paragraph — the "on iPhone and iPad" of that paragraph, shown. |
| `assets/piece.{webp,jpg}` | Pieces close up with the picture popover open, rims shaded from one direction — the relief, in "The pieces are the point". |
| `assets/generate.{webp,jpg}` | The cut mid-animation on iPad: the left of the picture already in pieces, the grid on the right still plain, the orange cutting heads between. |
| `assets/presets.{webp,jpg}` | The create sheet on Medium: the four difficulty tiles, the three chips under them, then the picture's text. Pairs with `custom`. |
| `assets/custom.{webp,jpg}` | The same sheet on Custom, with the piece count, rotation and snap distance each on its own control. |
| `assets/hazel.mp4` | One puzzle start to finish — picking a painting, watching the cut, solving it. 2:03, 496×1080, with the app's sound as a stereo AAC track. |
| `assets/hazel-poster.jpg` | The frame at 1:15 of that recording: the puzzle part solved, the border and the bridge in and loose pieces scattered around it. Shown until someone presses play. |

### Regenerating the cut

`tools/cut-export` is a small executable that links the app's `PuzzleEngine` and
prints one cut as SVG. It expects the app checkout beside this one
(`../Hazel`). It is a development tool; the published site has no build step.

```bash
swift run --package-path tools/cut-export CutExport --width 1200 --height 800 --pieces 64 --seed 9
```

`--stroke` divides the width to get the stroke weight (default 620). Paste the
output over the `<svg>` inside `<figure class="cut">`. Changing the seed changes
the cut, which is the point — it is generated, not drawn.

The tool reports **blanks** and **slivers** on stderr, which is how to shop for
a seed. A *blank* is an interior side the algorithm chose to cut without a knob;
on a still picture one reads as a false edge and pulls the eye, so a seed for
this page wants zero. A *sliver* is the short flat side a staggered junction
leaves between a diagonal pair — there are dozens in every cut and they are
meant to be there. Seed 9 at 64 has no blanks; seeds 6, 11 and 12 have two.

### Regenerating the wordmark

`tools/wordmark-export` rebuilds the logotype from the app's kern table. It is
outlines, not live text, for two reasons: the kerning is a *per-pair* table
(the z's open diagonals leave the most air, so `a`→`z` and `z`→`e` close
hardest, while the stem-facing pairs `H`→`a` and `e`→`l` ease), which no single `letter-spacing` can express; and
SF Pro can't be shipped as a webfont, so live text would fall back to Segoe UI
or Roboto off Apple platforms and the SF-tuned kerning would land on the wrong
letterforms.

```bash
swiftc -O tools/wordmark-export/main.swift -o /tmp/wordmark && /tmp/wordmark
```

Paste the output over the `<svg>` inside `<h1 class="wordmark">`. It fills with
`currentColor`, so it takes the ink in both appearances; the `<h1>` also holds
the word as visually-hidden text, so the heading still has real text in it.

Re-run this if `HomeWordmark.kernEm` changes in the app.

### The walkthrough video

`assets/hazel.mp4` sits directly under the subtitle, in the reading column
rather than the breakout — it is a phone screen, so it takes a phone's width
and is centered in the column, and blown up to the full measure it would be
over 1200px tall. It is first because it is the strongest thing on the page: it
shows the cut being drawn and the puzzle being solved, which is the whole
argument, before a word of the argument is made.

`preload="none"` and a poster frame, so a visit that never presses play costs
one 36KB JPEG rather than 6.7MB of video. There is no `autoplay`: the page has
one action on it and this isn't it, and the recording has sound, so an
autoplay would have to be `muted` to be allowed at all and would show the app
with the half of it that's audio switched off.

MP4 only. The usual reason to ship a WebM beside it is Safari, and Safari is the
one browser guaranteed to have H.264 — a second encode would be bytes in the
repository for no browser that needs them. Self-hosted, no player library and
no script, or the privacy page stops being true.

**Keep the audio.** The recording carries the app's sound, which is half of
what the video shows, so the encode passes the audio through untouched with
`-c:a copy` and never uses `-an`. The source's track is already AAC at around
46kbps; re-encoding it would only lose quality for no saving.

Check the source with `ffprobe` first. The current recording arrives as
496×1080 with square pixels, and goes out at that size. A raw simulator capture
can instead come out **anamorphic** (1206×1080 coded, with a `pasp` atom of
180:437 that displays as 496×1080). `scale` works on the coded size and ignores
that, so for one of those add `scale=496:1080,setsar=1` in front of the `fps`
filter.

```bash
ffmpeg -i "iPhone - Full Puzzle.mp4" \
  -vf "fps=30" \
  -c:v libx264 -profile:v high -crf 27 -preset slow \
  -pix_fmt yuv420p -c:a copy -movflags +faststart assets/hazel.mp4

ffmpeg -ss 75 -i assets/hazel.mp4 -frames:v 1 -q:v 3 assets/hazel-poster.jpg
```

496×1080 is 1.8× the 272px the video renders at. The last recording was scaled
up to 552×1200 to make an even 2×, but upscaling adds no detail, only bytes, so
this one ships at the size it was captured. `width`/`height` on the `<video>`
match it. 60fps down to 30 halves the bitrate and costs nothing: the only
motion is a finger dragging cardboard. `+faststart` puts the index at the front
so it plays before it has finished arriving.

The poster is a puzzle in progress rather than the cut. The cut in this
recording pans in close across the picture, so no single frame of it shows the
whole thing. A half-built puzzle with pieces lying loose around it says what the
app is at a glance, before anyone presses play.

### Replacing the screenshots

Each is a `<picture>` with a WebP source and a JPEG fallback. iPhone shots
(1206×2622, an iPhone 17 Pro) go out at 660×1435; iPad shots (1640×2360) at
984×1416, which is 0.6 of the capture and the same 2.4× over the width they
render at.

Ship the **whole screen**, uncropped. Cropping to the content was the earlier
rule and it was wrong: a crop is a claim about what the app looks like that the
app never makes, and the status bar and the island are what anyone holding the
phone actually sees. The cost is height — one 6.9-inch screen at the full
reading measure runs past 1200px — so a screen is shown at 17rem, a phone's
width, centered in the column, and renders at 272px.

`.shot` is one screen. `.shots` is a pair side by side at that same width, for
shots that only argue together — the presets and the three dials behind them.
Reach for `.shot` unless the second one is doing work the first can't.

An iPad screen is squarer, so at a phone's width it would come out shorter
than the phone shots around it and read as the smaller screen. `.shot.ipad`
widens a single one to 25.5rem, which lands it at a phone shot's height.
`.shots.devices` is the iPhone-and-iPad pair: its columns are in the ratio of
the two aspect ratios (46fr 69.5fr), so both come out the same height. If a
capture size changes, those numbers change with it.

```bash
magick shot.png -resize 660x -strip -quality 82 -sampling-factor 4:2:0 -interlace Plane assets/NAME.jpg
magick shot.png -resize 660x -strip -define webp:method=6 -quality 80 assets/NAME.webp
```

Check the capture's color profile first with `sips -g profile shot.png`. Most
simulator captures come out tagged sRGB, and the commands above are right for
those. Some come out **Display P3**, and `-strip` throws that profile away, so
the P3 values get read as sRGB and the picture goes visibly muddy, the reds and
greens especially. For a P3 capture, pull the profile out and put it back after
the strip, on both the JPEG and the WebP; browsers honor it in both:

```bash
magick shot.png /tmp/shot.icc
magick shot.png -resize 660x -strip -profile /tmp/shot.icc -quality 82 -sampling-factor 4:2:0 -interlace Plane assets/NAME.jpg
magick shot.png -resize 660x -strip -profile /tmp/shot.icc -define webp:method=6 -quality 80 assets/NAME.webp
```

`width`/`height` on the `<img>` match the export (660×1435, or 984×1416 for iPad), so the page reserves the right
space and doesn't reflow while they load. All of them are `loading="lazy"`;
they all sit below the fold. The video does not — it is the first thing under
the subtitle — but `preload="none"` means only its poster is fetched.

Images carry a 1px `--rule` border. The board shots bring the app's own ground
with them and would separate from the page without one, but the create sheet is
white to its edges and dissolves into the paper otherwise; the border is on all
of them, and on the video, so the set stays consistent.

Quality 82 rather than the 78 the crops used. A whole screen at 660px puts the
board's fine cut lines into about a third the pixels they had before, and 78
frays them; the difference is around 20KB a shot.

660px is 2.4× the 272px they render at, not 2×. The extra is for the cut lines
again: they are one device pixel wide in the capture and the first thing to go
soft, and a shot resized to 544 has visibly fewer of them than one resized to
660 and drawn at the same size.

## The App Store link

It appears twice in `index.html`: once after the opening two paragraphs, once
in the footer. Both use the same href, and there's an HTML comment above each
marking which is which.

The first is Apple's "Download on the App Store" badge, as supplied by Apple
Marketing Tools (toolbox.marketingtools.apple.com): `assets/app-store-black.svg`
on light and `assets/app-store-white.svg` on dark, switched by a `<picture>`
media query. Apple's rules are to use it unmodified, at least 40px tall, with a
quarter of its height in clear space around it. It's drawn at 48px with 12px of
padding. The footer one is a plain "On the App Store" link among the other plain
links, a way back to the first rather than a second ask. If a second badge ever
appears on the page, neither of them is the action any more.

All three pages also carry a Smart App Banner
(`<meta name="apple-itunes-app" content="app-id=6802787791">`). The page doesn't
fetch anything for it, but Safari on iOS and iPadOS asks Apple for the app's
name, icon and rating to draw it. That's the one request to another domain, and
the privacy page's "This website" section says so. Remove the tag and that
sentence together, or neither.

## Things this site deliberately doesn't have

No newsletter, no roadmap, no "coming soon", no press kit, no analytics, and no
cookie banner (there are no cookies to consent to).
