# Castaway

A pixel-art fishing game. You are a fisherman stranded on an island. You fish your way off it, then fish your way across a fictional sea, buying rods, lines, bait and boats, and (around 200 hours in) a starship, to fish in space. No character movement: locations are scenes you travel between on a map.

`index.html` is the entire playable prototype (vanilla JS, canvas, no build step). Open it in a browser. Read this file before changing anything; it holds every design decision made so far, what is placeholder, and what comes next.

## Decisions already made (do not relitigate without asking)

- **Stranded is the hook, and it is used.** You start on an island with no market. Fish go in a bucket. Driftwood comes up on the lines; six pieces build a raft. The raft is the first boat. The first port buys the whole haul, and that is the moment money starts to exist. Selling is irrelevant until you leave the island.
- **Passive lines bring volume and cash. Skill lines bring trophies.** A set line hooks common fish with no fight and never reaches trophy size. The rod gives a bite you must answer within a few seconds, then a bar-and-fish fight; rare species come only from the rod or the trophy line.
- **The trophy line is the expensive passive line.** Long wait, needs better bait, hooks big fish, but you still have to land it in the fight when you come back. It exists so patience pays without replacing skill.
- **"Leave the rod out and wait" means during a session, while you work another line.** Passive lines also keep ticking while the game is closed: a hooked fish is saved as hooked and waits for you. Passive lines do not earn money offline on their own; you still have to land and sell.
- **The map is revealed by boat range.** Raft → range 1, skiff → 2, cutter → 3, trawler → 4, starship → 5. Only the next unreachable node is shown, as a fogged `???`. Rod → bigger fish → money → boat → range → new islands → rarer fish is the progression ladder.
- **Own art, higher resolution than Stardew.** The bar-and-fish fight mechanic is fair game (mechanics are not protected); the art, sounds, names and UI are ours. Nothing is copied from Stardew Valley or One Cast. Everything in the prototype is placeholder code-drawn pixel art at 192×108 scene resolution; the resolution decision for real art is still open (see below).
- **Space is the endgame and the pitch line, not a reskin.** Space fishing only earns its place if the mechanics change (gravity throws off the net, alien behaviors). Build hours 1–20 first; space is an expansion.
- **The world is fictional, the fish are real.** Island names are placeholders until the world is named. Every species outside space is a real fish, placed by biome (warm shallows, temperate pier, kelp forest, deep sea, cold fjord), with its real adult size range. Space gets invented species later.

## Open questions and placeholders

- World name and island names: placeholders. Current set: Wreck Shoal (start), Gull Harbor (first port, shop), Kelp Reach (reef, no market), Black Trench (deep, buyer boat sells only), Frost Fjords (ice station, shop), The Stars (locked teaser). Species names are real and settled (53 fish, about ten per location).
- Art resolution. Prototype scene is 192×108 logical pixels. "Higher quality than Stardew" means more pixels per sprite; every step up multiplies asset cost. Decide before drawing anything real. Suggestion: 320×180 scene, 24–32 px character.
- Real-time vs accelerated clock. Prototype: 1 real minute = 1 game hour (24-minute day) so bite windows are testable. Passive-line timers are real time (`Date.now()`), so they survive closing the game.
- All numbers below are prototype values at roughly one-tenth of intended wait times. Prices and fish values are untuned guesses.
- Travel cost. Sailing is free in the prototype (it pulls in every line). Fuel or tickets per trip are an option.
- Bucket cap (20) and what "bucket full" means at a location with no market. Prototype: the catch is recorded in the index and released.
- What makes hour 50 feel different from hour 5. Current answer: the fish index (count, best size, trophy count per species). Likely additions: rare variants, weather, size records with names.

## Presentation decisions (second pass)

- **The scene is the whole screen.** One fixed canvas fills the viewport; the HUD, the tide line and the control panel float over it. No screen-in-a-screen. The panel is translucent so the water shows through.
- **Logical resolution adapts to the device.** `layout()` picks an integer scale (phone: 2 → about 195×375 logical px; desktop: 3–4) so pixels stay crisp. The surface line sits at 40% of the height in portrait, 52% in landscape. Everything is positioned relative to `W`, `H`, `SURF` and `shoreW`; never hardcode pixel positions.
- **Own look, not One Cast's.** Dithered sky and sea gradients (2×2 Bayer, cached per hour), a big seated fisherman (24×30) with a wide hat and beard, a sun that rises over the water on the right and sets behind the island on the left, light shafts, a sandy seabed that darkens with depth, per-location foregrounds (beach with wreck and palm, pier with shack and lantern, rocks with kelp, your own boat at the trench, an ice shelf with aurora and snow). The map is a parchment chart with ink islands, dotted routes and a compass, not a world map.
- **Title screen, then an opening cutscene on New voyage.** Four beats, each tappable, skippable: the storm takes the boat; the fisherman drifts on a plank holding the rod up; washed up on Wreck Shoal; sits up on the same plank he fishes from in the game. The last beat's framing is the game's framing, so the cut into play is seamless. Continue skips it. The menu can replay it.
- **Wipe protection.** Start over asks for a second tap.

## Catch screen and control panel (third pass)

- **Every landing opens a catch screen.** Headline by priority (Trophy! → New record! → New species! → Landed!), tag pills for the rest, the fish lit in a dark panel, name, size, a sub line (rig, time since the line went out), and three stat tiles: worth, bait, time of day. Driftwood and junk get the same screen with a single button.
- **The fish is held until you decide.** At a market: **Sell** (that one fish, money now) or **Keep** (bucket, sell the haul later). Elsewhere: **Keep** or **Release**. Bucket full: Keep is disabled and Release is offered. Tapping outside the card keeps the fish (or releases it if the bucket is full). The pending catch lives in `S.pending` and is saved, so a reload reopens the screen. The index is updated at landing time, so a released fish still counts as caught.
- **The panel is the always-there tab.** Tide line; `LINES` header with a live "n lines ready" / "n on!" count; four line cards; a compact `ROD · SCRAPS  CHANGE` row where tapping the rig or bait chip cycles to the next option and CHANGE unfolds the full rig and bait pickers; the rig note; the big Cast / Set the line / Pull in / Reel in / Haul in button; then a bottom bar with **Inventory** (bucket count and value) and, at a market, **Sell haul**. The driftwood row stays above the bar on the shoal before the raft.
- **Inventory sheet.** Bucket list with sprites, size, trophy star, value and a Let go button per fish; whole-bucket total and Sell the haul at a market; bait counts; gear (rod, boat, lines, trophy rig, driftwood).
- **Every species has its own sprite, built from a body plan.** `FISH_ART` gives each species a plan (`gen`: size, body depth, where the body is deepest, peduncle and snout thickness, tail type `fork`/`lunate`/`hetero`/`round`/`trunc`/`none`, dorsal and anal fins by type and span, adipose fin, pelvic fin, barbels, a beak, an underbite, open `jaws` with teeth, an angler's lure, a tail light) or a hand-drawn silhouette (`sil`, used for the sunfish), plus a palette (`col`: back, belly, fin, mark, glow) and a pattern (`pat`: bars, saddles, stripes, lateral line, spots, scales, lights, bands, scutes…). `genSil` rasterises the silhouette; `shadeSil` adds the outline, sheen, back/mid/belly tones, fin rays, the eye and the pattern. Sprites run 28–64 px wide and 9–27 tall, two to four times the pixels of a 16 px Stardew fish, in line with the "higher resolution than Stardew" decision. They appear on the catch screen (always 3×, so size differences show), in the index (unknown species as a dark silhouette), in the inventory and in the fight. The swimming fish in the scene stay one-colour silhouettes on purpose: they are far off and under murky water. The fisherman and the scenes have not had their real art pass yet.

## The prototype, section by section

Everything lives in one `<script>` in `index.html`. Sections, in file order:

| Section | What it holds |
| --- | --- |
| `T` (tuning) | hour length, wait ranges per rig, hit window, bucket cap, raft wood count |
| `BAITS`, `RODS`, `BOATS`, `SLOT_PRICES`, `TROPHY_RIG_PRICE` | shop data |
| `LOCS` | locations: range gate, shop/sell flags, chart coordinates, blurb |
| `SPECIES` | 53 real fish by biome: location weights, bite-time windows, real size range in cm, base value, fight behavior (`smooth`/`dart`/`sinker`/`floater`), difficulty, min bait tier, rare flag, scene silhouette `shape` (`fry`/`bream`/`trout`/`pike`/`eel`/`big`/`flat`), `big` for double-size silhouettes; plus `wood` and `junk` entries. `ID_MAP_V3` maps the old fictional ids onto these |
| state (`S`, `freshState`, `save`, `load`) | the whole save in one object (`v: 4`), persisted to `localStorage` under `castaway-proto-v2`; `load` migrates v2 and v3 saves (v3→v4 remaps species ids in the index, bucket, pending catch and hooked lines) |
| fishing (`pool`, `rollCatch`, `castLine`, `tick`, `land`, `resolveCatch`, …) | the catch is rolled at cast time for the moment the bite will happen; the line just waits for that timestamp. `land` records the catch in the index and parks it in `S.pending`; `resolveCatch` sells, keeps or releases it |
| shop (`shopItems`, `buy`) | next rod, next boat, extra slots, trophy rig, bait packs |
| rendering (DOM) | HUD, tide line, lines header, four slot cards, the action box for the selected slot (compact rig·bait row, `rigOpen` unfolds the pickers), the driftwood row and the inventory / sell bar |
| sheets | chart (canvas + legend + sail), index (with silhouettes), inventory (bucket, bait, gear), shop, menu |
| catch screen | `showCatch` renders `S.pending` into the card; `fmtDur` for the wait |
| pixel art | `MASKS` (single-color string masks: distant fish shapes, gulls, crab, sun, map pieces), the species sprite generator (`genSil`, `shadeSil`, `fishPal`) and `FISH_ART` (body plans and palettes per species; `fishMap` builds and caches the coloured map, `fishC` caches canvases per id/scale/mode, `blitFishCentered`), `SPRITES` + `PAL` (multi-color: `sit`, `lie`, `float`), cached to offscreen canvases by `maskC`/`sprite`, `blit` with flip, Bresenham `bres` |
| sky model | `SKY` keyframes by hour, `skyAt`, `nightness`, star/cloud/rain/snow/mote particle tables seeded by a fixed LCG |
| canvas & layout | `layout()`, cached `skyLayer`, `seaLayer`, `shaftLayer`, `floorLayer` (per-location seabed) |
| scene pieces | `drawSkyBase`, `drawSeaBase`, fish (`reseedFish`/`stepFish`/`drawFish`), `palmTree`, `pine`, `farIsland`, `drawBoat`, `drawShore(id)` (returns the fisherman's seat), `drawFisherman`, `drawGame` |
| title & cutscene | `drawTitle`, `drawStorm`, `drawDrift`, `beachScene`; `CUT` beat table (duration, caption, draw fn); `MODE` is `title`/`cut`/`game`; `fadeTo` for transitions |
| frame loop | one `requestAnimationFrame` loop dispatching on `MODE`; pauses when the tab is hidden |
| chart | 160×120 parchment canvas, islands by range, fog `???` for the next range, ink ring on the selection, boat marker, compass |
| the fight | 128×128 canvas: hold indicator left, water column x 24–88 with the species sprite, progress tube right; hold to lift the net, release to drop; fish moves by behavior; progress fills inside the net, drains outside; difficulty widens the drain |
| input / boot | click delegation on `data-act`, pointer hold for the fight, offline resolution of lines on load, title first |

Line state machine: `idle → waiting → bite (rod, 3 s window) → fight → idle` or `idle → waiting → hooked (set/trophy, waits forever) → haul or fight → idle`. One rod can be active at a time; set lines can all be out at once.

Catch value: `base × (0.6 + 0.9 × sizeFraction)`, doubled at trophy size (sizeFraction ≥ 0.85). Trophy line skews size up (`u^0.45`), set line skews down and caps at 0.8, rod is linear.

## How to run and test

- Open `index.html` in any browser. Fonts load from Google Fonts; everything else is inline. Works offline except the fonts (monospace fallback).
- Reset a save: Menu → Back to title → Start over (tap twice), or in the browser console `localStorage.removeItem('castaway-proto-v2')` and reload.
- Visual check without a phone: Playwright with a 390×760 mobile viewport, screenshot the title, each cutscene beat (click `#cut` to advance) and the game. Animated buttons (`.cta.hit`) need `{ force: true }` to click in Playwright.
- To test the island loop fast, lower `T.setWait` or raise the `wood` weight in `SPECIES`.
- To check the catch tables without a browser: extract the script and run `pool()` for every location × phase × bait × rig; none should be empty (last run, with the real roster: 160 pools, zero empty, ~28% driftwood on the shoal day rod).

## Art direction

- Side-on cross-section filling the screen: sky, surface line, underwater, with the shore on the left third and open water to the right. The fisherman sits at the water's edge on something that belongs to the location (wreck plank, pier end, rock, his own deck, ice). Lines go out to floats spread across the water.
- Palette: tar navy ground, brass accents, lantern light. Day cycle through dawn/day/dusk/night sky keyframes; the sun rises over the sea and sets behind the island.
- Each location needs its own foreground, seabed, fish roster, and bite windows. That is where content cost goes: budget 5–6 distinct locations for a first release, not twenty.
- Replace the string-mask sprites with real sprite sheets once the resolution is decided. Keep `image-rendering: pixelated` and integer scaling. Keep the caching pattern: anything static per hour or per location is rendered once to an offscreen canvas.

## Roadmap, in order

1. Tune the first 20 minutes: time to raft, first sale amount, time to skiff. Target: raft in ~4 minutes, harbor in 5, a reason to come back to the shoal (Ghost Tarpon at night).
2. Fight feel: per-species behaviors are table stakes. Add one twist that makes the mechanic ours (line tension that snaps if you hold too long, or a second axis).
3. Index as the long-term driver: size records, rare variants, catch log per location.
4. Weather and seasons as bite gates.
5. Audio: a bite thump, a reel, a sale chime. Original.
6. Real art pass at the chosen resolution.
7. Split the single file into modules only when it passes ~2,000 lines or a second person joins.
8. Space: new physics in the fight, alien roster, the Starship at $500,000 stays as the visible endgame price.

## Conventions for working in this repo

- Keep it dependency-free and single-file until the roadmap says otherwise. No frameworks, no bundler.
- Every wait, price and weight is data in the tables at the top; change numbers there, not in logic.
- Never add art, names, sounds or UI copied from existing games.
- When adding a species: pick a real fish that belongs in that location's biome; add location weight, time window, real size range, value, behavior, difficulty, min bait, rare flag, scene `shape`, and a `FISH_ART` entry (a body plan and palette; copy the closest existing plan and adjust, or a hand-drawn `sil` for odd shapes). Then re-run the empty-pool check. Judge sprites on a contact sheet first: render every `fishMap` at 3× in a scratch page.
- Fish are drawn head right, tail left; the scene flips them as needed.
- Save format is versioned (`v: 3`). Bump it and write a migration in `load()` when the shape changes.
- Testing in Playwright: the game saves on `visibilitychange`, so a reload overwrites anything you put in `localStorage` from the page. Seed a save with `context.addInitScript` in a fresh context instead.
- Reference material the designer liked: One Cast (onecast.github.io) for the fast-travel, multi-line, location-and-bite-window structure; Stardew Valley for the fight mechanic. Reference only.
