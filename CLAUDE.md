# CLAUDE.md

The website for Hazel, a jigsaw puzzle app for iPhone and iPad. Three static
pages, plain HTML and CSS, served by GitHub Pages from the repository root.

Read `README.md` before changing anything. It holds the constraints that keep
the privacy page true (no JavaScript, no requests to other domains, no
analytics), how every asset was made, and how to regenerate them.

## Writing copy

All user-facing text follows `docs/voice-and-tone.md`. That covers page copy,
headings, `<title>`, meta and Open Graph descriptions, figcaptions, alt text,
and the video's fallback text. Read it before writing or editing any of them.

The short version, plus how it applies to this site:

- One person telling a friend about something he made. First person singular,
  never "we". Brief, warm, and glad rather than boastful.
- Say less. The short version is usually the right one.
- Explain unfamiliar ideas the first time they appear on a page, like where
  cuts meet slightly offset. Don't explain everyday puzzle words like knob or
  border; that talks down to the reader.
- Leave the page title and subtitle ("Jigsaw puzzles cut from scratch") alone
  unless asked. The guide's subtitle sample is for the App Store, not the site.
- No comparisons with other apps, named or not, and no internal vocabulary
  (engine, generator, record, false edge, sliver, stagger and the like).
- Price and what's left out are stated once per page, as a fact: free, no
  ads, timers, or account, nothing to buy. No justification for why it's free.
- About one exclamation point per page, attached to a feeling.
- No urgency, no asks, nothing that leans on the solo-developer story.

The privacy page is the exception to "say less": it's a policy, so accuracy
comes first. If its substance changes, update the date at the top.

## When you change copy

- `README.md` refers to section headings and paragraph positions on the home
  page (where the badge sits, which figure goes with which section). Keep it
  in step.
- Keep the title, meta description and Open Graph tags in step with the home
  page's opening.

## Previewing

`.claude/launch.json` has a `site` configuration that serves the repository
on port 8123. Use a server, not `file://`; the routes are directories.

Everything in the repository root is published, including this file, the
README and `docs/`.
