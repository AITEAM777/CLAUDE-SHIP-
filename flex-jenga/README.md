# FLEX / JENGA.

A physics-driven 3D Jenga game in one file: `index.html` (Three.js 0.160 + Rapier 3D 0.19, loaded from jsDelivr).
Open it in a current desktop browser; no build step. An internet connection is needed for the two CDN libraries.

## Play

| Input | Action |
| --- | --- |
| Left drag on a block | Pull it out along its axis (the hand is a damped spring, so tight blocks resist) |
| Left drag while carrying | Move the block over the top; assisted placement snaps to the nearest free slot |
| Release | Drop. The turn counts once the block settles on top |
| Right drag | Orbit the camera |
| Wheel | Smooth zoom |
| Space | Precision slow motion (speed chosen in the panel: 1×, 0.5×, 0.25×) |
| Q / E | Turn a carried block |
| Esc | Let go |

Rules: you may not take blocks from the top complete layer or the unfinished layer above it, an extracted block must be placed on top before the next pull, and the game ends when any tower block falls out or the tower tips past 28°.

Modes: Classic, Flex, Extreme flex, Earthquake (tremors are announced two seconds ahead), Precision (three strikes for force over the limit).
Challenges: Survival, Daily (same tower for everyone on a given date), Randomized (block tolerances and initial offsets vary).
Practice mode enables Undo. Personal records live in `localStorage`. After a collapse, Replay plays back the last seconds from recorded snapshots.

## Physics

- Units are centimetres, grams and seconds; blocks are 7.5 × 2.5 × 1.5 cm with small seeded height tolerances, so load is shared unevenly as in a real tower.
- Rapier rigid bodies with 360 Hz substeps and stiff contacts so an 18–30 layer stack stands on its own.
- **Flex wood** is a compliant-contact model: each block reads its vertical contact load every step, compresses towards a load-dependent target through a damped spring, and its collider height and friction are updated from that compression. Bending and twisting in the render are driven by the same load data. This is a hybrid, not a finite-element soft body.
- Stress Vision colours each block by its share of load relative to an even split; the Stability card combines support margin, tilt and motion; the centre-of-mass marker shows the mass above the weakest layer.

## Verified (headless Chromium + SwiftShader, scripted)

- Tower stands for 12 / 18 / 24 / 30 layers in every material preset.
- Real mouse path: hover probe, extraction, carry to the top, placement, scoring; a block dropped beside the tower can be picked up again.
- Physically driven collapse (removing the centre and one side block of a low layer) ends the game; the result card, records and replay work.
- Pause, slow motion (0.25× measured), practice undo (blocks restored within 0.1 cm), camera presets, material switching, earthquake warning and tremor, precision strikes.
- Physics cost about 1 ms per 60 Hz frame for 54 blocks on the test machine.

Not verifiable headlessly: how sounds actually sound, and frame rate on a real GPU.
