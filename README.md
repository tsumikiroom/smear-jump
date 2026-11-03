# smear-jump

Tap the screen and the picture jumps there, smearing like a hand-drawn animation frame.

**Live:** https://tsumikiroom.github.io/smear-jump/ (made for phone browsers)

## How it works

- **Motion** — the picture travels to the tapped point with smootherstep easing (slow in, slow out). An optional arc bends the path and an optional spin rotates the picture on the way.
- **Bands** — the picture is cut into ~20 bands that run along the direction of travel. Band borders are shifted at random, so the edges come out jagged.
- **Lag** — each band follows the motion with its own delay (about 0.12 s, varied per band). The leading pixels of a band stay at the current position; everything behind them is stretched over *(distance covered during the lag) × exaggeration (~4×)*. When the motion stops, the lag catches up and the picture returns to its normal shape.
- **Leading-edge table** — for each row of the travel frame, the CPU finds the first opaque pixel seen from the leading side and uploads it as a float texture (one row per picture frame). The fragment shader only looks it up, so the per-pixel cost stays small. The table is rebuilt for every frame at once, only when the picture's angle relative to the heading changes.
- **Moving picture (default)** — a galloping horse: a 24-frame cut-out loop packed into one atlas (`assets/horse_atlas.png`, 6 × 4 tiles of 224 px), played at 12 fps. The frame that is showing is the one that gets smeared. The footage was generated with MiniMax H3.
- **Bird** — a flapping bird, packed the same way (`assets/bird_atlas.png`), also generated with MiniMax H3.
- **Drawn pictures** — four more drawn at runtime with Canvas 2D (shapes and text).

One `index.html` plus the atlas, WebGL2, no build step. Open it through a web server (e.g. `python -m http.server`); opened as a local file, the browser blocks reading the atlas pixels and only the drawn pictures appear.

## Controls

- Tap anywhere: jump.
- Bottom bar: pick a picture, playback speed (1×, 1/4, 1/16, stop), and a collapsible **Knobs** panel (jump time, lag, stretch, bands, jag, lag spread, arc, spin, size).

## URL options

Useful for reproducible stills, e.g. `index.html?pic=horse&tap=0.2,0.25&t=0.22&arc=0.5&spin=1&seed=3`

- `tap=u,v` jump target as fractions of the screen from the top-left
- `t=seconds` freeze that far into the jump
- `seed=n` fixed band randomness
- `pic=horse|bird|word|star|ball|blocks`, `knobs=1`, and any knob name (`dur`, `lag`, `stretch`, `bands`, `jag`, `spread`, `arc`, `spin`, `size`)
