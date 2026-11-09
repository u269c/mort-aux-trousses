# Design notes

How *La Mort aux Trousses!* is shaped, and why.

## Intentions

- **One thumb, one button.** The game is designed for a phone held upright. You never move left or right: you only decide *when* to jump and *how long* to hold. Every other decision in the game comes from that one input.
- **One file, no build.** Everything lives in `index.html`: plain JavaScript, a single `<canvas>`, and no libraries or bundler. You can email it, host it anywhere or open it straight from disk, and it will still work in ten years.
- **Retro but vivid.** A 768 × 1024 portrait play field, flat saturated colours, chunky pixel type (*Press Start 2P*) and simple shapes. Satan is the deliberate exception (see below).
- **Forgiving to fail, rewarding to risk.** Small mistakes should not feel unfair, but ambition should pay off. A lot of the tuning comes from that tension: side bumps are free, a fall rewinds you to your last ledge, and taking the higher, harder route is how you score big.

## Shape of a run

The scroll speed is tied to your progress through a level and rises smoothly from start to finish. Your jump is a fixed launch speed. While the button is held and you're still rising, gravity drops to about a third, for at most 0.26 s. Releasing early caps your upward speed. A short buffer remembers a jump pressed just before landing, and a short grace period lets you jump just after running off an edge. Both make the controls feel responsive on touch screens.

Ledges come in two kinds:

- **Columns** (rooftops, mansion walls, swamp banks, cave pillars) are solid all the way down.
- **Floating ledges** (girders, balconies, logs, rock shelves) are **one-way**: you jump up through them and only land when coming down. Without this, hazards placed above a ledge could become impossible to avoid.

Gaps between ledges are generated from the current speed, so a jump is always physically possible. Gaps leading to a higher ledge are kept shorter.

## Forks as a ladder

The branching structure was the main design problem.

A **true binary tree** (every route splits in two at every fork) would mean 8 or 16 separate stacked lanes by the end of a level. They would not fit on a phone screen, and a fall would rarely land anywhere useful.

The game uses a **ladder** instead:

- Each fork adds one new **height** on top. At every fork, each existing height gets a step that lets you climb one level, or you can stay where you are.
- **All heights keep generating in parallel** to the end of the level. A fall from height 3 will often land on height 2 or 1, which turns a mistake into a demotion instead of a death.
- Steps are **staggered sideways** (about 500 px per height), so every climb has clear air above it and no ledge sits too close over another.
- With *n* forks there are *n + 1* finishing heights, but 2ⁿ distinct **paths** through them (2, 2, 8, 8, 16 per level). Each path has a named ending, so the endings track the choices you made, not just where you finished.

Difficulty scales with height: ledges get narrower (down to about half width), ledges move more, and the hazard rate rises. The reward scales with it too: a points multiplier per metre, and a clear bonus of 500 × (1 + height)².

## The levels

Each level adds one idea on top of the basics.

| Level | Forks | Idea it introduces |
|---|---|---|
| 1. Halloween City | 1 | The basics: gaps, girders, flying hazards, and the first fork |
| 2. Haunted Mansion | 1 | Hazards that move on their own (drifting ghosts, flying books), swinging chandeliers |
| 3. Drowned Marsh | 3 | The ground becomes unstable: logs sink while you stand on them, skeletons erupt from gaps, souls heal you |
| 4. Hollow Dark | 3 | You can't see far: a light radius around the reaper, plus goblins that warn with red eyes, then pounce from behind |
| 5. Dante's Inferno | 4 | Endurance: twice as long, with a new colour scheme and banner for each of the nine circles, ending on the ice of Treachery |

Healing souls appear from level 3 on: three on the ground route, two on the first height and one on the very top. The middle heights have none, which makes them the riskiest place to be.

## Satan

The duel happens on whichever height you reach. The other ledges crumble away and the screen stops scrolling.

**Difficulty** is a level from 1 to 8, worked out as 1 + finishing height + high roads taken. Each level stacks on the previous ones: thrown weapons, more life, sine-wave bat escorts, speed, bigger volleys, double life, and finally feints (a fake charge that skids to a stop, a ram wind-up that turns into a leap, or a throw wind-up that turns into a charge). Every attack has a visible wind-up glow, so a skilled player can always read it.

**Look.** Satan is intentionally drawn in a completely different style from everything else: painted gradients, a noise skin texture, wet highlights, an open ribcage with a beating heart, guts and a tail simulated as hanging chains, dripping blood that pools on the ice, and eyes that track the reaper. Seeing him should feel wrong next to the cute pixel world.

## The reaper

A hooded robe with green eyes glowing in the dark of the hood. The arms splay apart (one reaching forward, one trailing back) so they never cover the face. The face is the main way the game gives feedback:

- **Idle:** blinking
- **Jumping:** wide eyes
- **Falling:** a wobbly mouth
- **Hit:** red X eyes
- **Close call or dodge:** a happy face with blushing cheeks
- **End of level:** a thumbs-up, then a slow walk into the portal

## Code map

`index.html` is organised in labelled sections, roughly in this order:

1. **Constants and palettes:** physics numbers, per-level and per-circle colours
2. **Levels:** one config object per level (length, fork positions, speed range, hazard mix, special flags)
3. **Language:** the `I18N` table (French and English). Every on-screen string goes through `T()`, and level names, endings and circle names live here too.
4. **Level construction:** parallel *lanes* (one per height), `authorFork()`, `authorEnd()`, platform and hazard placement
5. **Hazards:** per-type update, hit shapes, near-miss detection
6. **Satan:** difficulty ladder, attack state machine, chain physics, animation targets
7. **Update loop:** speed, camera, physics, souls, goal and duel triggers
8. **Drawing:** backgrounds, platforms, hazards, the reaper, Satan, HUD and screens
9. **Input:** pointer and keyboard; long press on Game Over returns to the menu

All drawing is done in code with the Canvas 2D API. There are no image or sound files: sound effects come from Web Audio oscillators, and textures are generated at startup.
