# Anomalous Materials

A Half-Life themed pixel side-scrolling platformer in a single self-contained HTML file.

You play Gordon Freeman in his scientist lab coat — not the HEV suit — running right
through Black Mesa Sector C in the minutes after the resonance cascade. The level never
ends; it is generated ahead of you as you run, and it gets harder the further you get.

There is no build step and there are no dependencies. Open `index.html` in a browser and
it runs.

## The dimension shift

The interesting part is not the running. It is that the level is two stacked worlds
occupying the same screen space, and Shift moves you between them.

Every platform belongs to one of three layers:

- **Both** — ordinary floor, solid in either world.
- **Black Mesa only** — concrete, blast pillars, walls. Solid while you are in Black Mesa,
  empty air the moment you phase to Xen.
- **Xen only** — floating organic shelves. Empty air in Black Mesa, solid in Xen.

That inversion is the whole toolkit:

- A chasm you cannot jump has a Xen shelf hanging in the middle of it. Phase in, cross the
  shelf, phase back out.
- A blast pillar blocks the corridor in Black Mesa. It does not exist in Xen, so you phase
  *through* the wall rather than climbing over it.
- A floor section may be a Black Mesa grate with nothing under it in Xen, so phasing while
  standing on it drops you into the void.

Gravity is lower in Xen (`0.22` versus `0.48`) and the jump is weaker to match, so Xen
jumps float — longer hang time, more air control, less snap.

Enemies invert too. Headcrabs are alive and lethal only in Black Mesa; in Xen they go
inert and you can walk straight through them. Xen floaters are the reverse — harmless
scenery in Black Mesa, deadly once you phase in.

### Phase charge

Being in Xen costs charge. It drains at `0.62` per frame while you are there and regenerates
at `0.34` per frame once you are back in Black Mesa, so a round trip is never free. Xen
energy cells scattered through the level restore `38` each.

Two rules keep the mechanic honest:

- **You cannot phase into a solid mass.** The shift is attempted, the game tests your
  hitbox against everything solid in the destination world, and if anything overlaps it
  reverts the shift and flashes `PHASE BLOCKED / SOLID MASS`. You cannot clip inside
  concrete on purpose.
- **You can be ejected into one.** If charge reaches zero the borderworld throws you back to
  Black Mesa whether or not there is room. The same overlap test runs, and this time
  failing it is fatal — `REMATERIALIZED INSIDE MASS`. Phasing under a slab with 10% charge
  left is how most runs end.

Below 12% charge the game refuses to let you enter Xen at all, which is the difference
between a mistake and a death sentence.

## Controls

| Input | Action |
| --- | --- |
| Left / Right arrow, or A / D | Move |
| Space | Jump — hold for a higher jump |
| Shift | Phase between Black Mesa and Xen |
| K | Crowbar swing |
| R | Restart |
| M | Mute |

Touch controls appear automatically on mobile.

## How to play

Open the file directly:

```
index.html
```

Double-clicking it works. If you would rather serve it over HTTP:

```
npx serve
```

then open the URL it prints.

Score comes from vials. Distance is tracked separately and your best distance is saved to
`localStorage`, so it survives a reload.

## How it works

### Procedural generation

The level is built one chunk at a time, just off the right edge of the camera. A difficulty
value ramps from 0 to 1 over the first 9000 units of distance and biases the chunk roll as
it climbs.

After a fixed safe opening runway, each chunk is one of five patterns:

- **Plain floor** — a flat run, usually with a headcrab, sometimes a vial. Its share of the
  roll shrinks as difficulty rises.
- **Staircase** — two to four ascending ledges, often with a cell on the last one.
- **Xen-bridged chasm** — a gap too wide to clear, with a Xen-only shelf floating in the
  middle and a cell above it. Gaps widen with difficulty.
- **Blast pillar** — a Black Mesa-only pillar standing in an otherwise plain corridor. You
  phase through it.
- **Xen shelves over a grate** — a Black Mesa-only floor with a row of Xen-only shelves
  above it, so both worlds are traversable but along different routes.

Chunks are appended until the generator is far enough ahead of the camera, and platforms,
mobs and pickups that fall behind the camera are dropped.

### Movement feel

Standard platformer affordances, all of them tuned rather than defaulted:

- **Coyote time** — 6 frames of grace after walking off a ledge during which a jump still counts.
- **Jump buffering** — a jump pressed slightly before landing fires on touchdown.
- **Variable jump height** — releasing Space early cuts the upward velocity.
- **Constant forward push** — the camera keeps moving, so standing still is not a strategy.

Headcrabs and floaters die to a stomp from above or a crowbar swing. Sound is generated in
the browser with WebAudio oscillators; there are no audio files.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The entire game — markup, CSS and JavaScript |

## License

No license specified.
