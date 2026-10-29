# Castaway

A pixel-art fishing and hunting game that runs in the browser, on phone and desktop.

**Play it:** https://zik3434.github.io/Castaway/

- On a phone, open the link and add it to the home screen: it installs as an app, runs full screen and works offline.
- On a computer, Space or Enter casts, reels and hauls the selected line, Space holds the net in the fight, Escape closes any sheet, and the arrow keys walk the island.

Stranded on an island with one rod and one line, you fish your way off it, then fish and hunt your way across a sea of very different islands, buying rods, lines, bait, weapons and boats on the way.

## Running it yourself

The game is a single `index.html` with no build step. Open it in a browser, or serve the folder with any static server (the service worker and the home-screen install need `http://` or `https://`, not `file://`).

The site deploys itself: every push to `main` runs `.github/workflows/pages.yml`, which publishes the repository root to GitHub Pages.

`CLAUDE.md` holds the design decisions, the prototype's structure and the roadmap. Start there.
