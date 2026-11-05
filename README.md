# Sweetwater

A browser recreation of Sweetwater, the frontier town from Westworld. Walk off
the train, follow Main Street, push through the batwing doors of the Mariposa,
climb the Coronado staircase and watch the light go gold over the mesas.

It runs in a browser with no build step and no CDN: plain ES modules and a
vendored copy of three.js r160.

## Why it exists

A town is a hard test for a renderer and for a level layout at the same time. It
has to hold a street of thirty shopfronts, interiors you can enter, a skyline
that reads at sunset, and a player who must never get stuck in a doorway. This
is a study in building all of that from primitives in code rather than from a
modelling package.

## What you can do

- **Walk the town** on foot, in first person, at day or dusk.
- **Enter the buildings**: the Mariposa, the Coronado, the General Store, the
  Sheriff's Office, the Gazette, the chapel and the rest of the street.
- **Climb the mesa** and look back at the town.
- **Fast travel** from the town map, and read the compass and place cards as you
  arrive somewhere.
- **Meet the hosts**: around thirty ambient characters on looping beats, Dolores
  by the general store among them.
- **Play on a phone**, where a stick and buttons appear automatically.

## Running it

```bash
npm install --omit=dev     # optional, only pulls ws for the multiplayer relay
node server.js             # http://localhost:3000
```

`PORT` and `HOST` are read from the environment, defaulting to 3000 and
0.0.0.0.

## Controls

| Key | Action |
|---|---|
| `W A S D` | Move |
| `Shift` | Run |
| `Ctrl` | Crouch |
| `Space` | Jump |
| Mouse | Look, click to capture the pointer |
| `E` | Interact |
| `T` | Lantern |
| `M` | Town map, click a marker to fast travel |
| `Esc` | Pause and settings |
| `F3` | Performance statistics |

## What is not here

- There is no story, no mission and no combat. It is a place to walk around.
- The multiplayer relay is a small presence channel, not a game server. Nothing
  is persisted.
- The town is not an exact recreation of the set. It is built from the readable
  parts: the street plan, the shopfront names and the silhouette.
- Textures are CC0 ambient sets and the skies are Poly Haven captures; see
  `public/assets/hdri/CREDITS.txt`.

## How it is built

```
public/
  index.html            loader, menu, HUD, map, settings
  js/main.js            boot sequence, game loop, keybinds
  js/core/              renderer, post effects, quality presets, assets, audio
  js/world/             terrain, sky, builder, buildings, props, hosts
  js/player/Player.js   capsule collision, step-up, ramp climbing, jump buffer
  js/ui/HUD.js          compass, toasts, place cards, map
  assets/textures/      ten CC0 PBR sets
  assets/hdri/          day and dusk skies
  assets/models/        horse and stork
  vendor/three/         three.js r160 with the examples it imports
server.js               static server with ETag, brotli and SPA fallback
```

The town is assembled by a small builder DSL: primitives are declared in a
transform stack, tagged with a material and merged into a handful of draw calls.
One build call emits the street, the interiors, the colliders and the ramp list.

Two collision details carry the experience. Street dressing is laid down before
the buildings and each shop keeps its footprint plus an approach corridor clear,
so barrels and porch clutter can never seal a doorway. Stairs are drawn as
treads but collide as a single slope, so the player glides up instead of catching
a toe on each riser.

### Tests

`test/` holds the browser and unit checks: `smoke.mjs`, `browser-gameplay.mjs`,
`browser-verify.mjs`, `hosts.test.mjs`, `mariposa.test.mjs`, `sky.test.mjs`,
`compression.test.mjs`. Run one with `node test/smoke.mjs`.

### Deploy

`render.yaml` and `DEPLOY.md` describe the Render deployment; `Procfile` is there
for a plain process host.

## Licence

MIT, see `LICENSE`.
