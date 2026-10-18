# The Living Ocean

An interactive 3D sailing simulation in a single file (`index.html`). A three-masted barque modelled on a black-hulled tall ship sails a Gerstner-wave ocean. Change the weather from sunshine to an extreme storm and the crew reef sails, man the lines, lash down gear, brace at the rails and take shelter while the captain directs them.

Three.js r160 (with OrbitControls and BufferGeometryUtils) loads from jsDelivr; everything else is generated in the page.

## Run it

- Open `index.html` in a current desktop browser with an internet connection for the first load.
- If module scripts are blocked on `file://`, serve the folder: `npx serve .` or `python3 -m http.server`.

## Controls

- Drag to orbit, scroll or pinch to zoom, right-drag to pan, double-click to focus on a point.
- Click a sailor to see their role, state and activity; "Focus camera" follows them.
- Keys: `1`–`5` weather presets, `Space` pause, `Esc` closes the crew card or leaves cinematic mode.

## How it works

- **Ocean and buoyancy share one wave model.** Eight Gerstner components are evaluated in the water's vertex shader and, with identical parameters, on the CPU. The ship samples nine hull points, then springs heave, pitch and roll toward the sampled surface with damping, plus wind heel.
- **Weather** blends wave height, speed, steepness, wind, cloud, darkness, rain, fog, foam and lightning over the transition time. The crew alert rises immediately and stands down once the sea has started to ease.
- **Crew** are articulated procedural figures parented to the deck. Each sailor runs a finite-state machine and walks a waypoint graph laid out around the masts and deckhouses. Jolts in the ship's motion trigger stumbling, bracing and grabbing the rail.
- **Sails** deform on the CPU (billow, flutter, folds). Each mast's deployment moves only while a sail handler is hauling at that mast's station, and reefing is staged: royals first, lower topsails last.

## Approximations

- Sails and flags use procedural cloth-like deformation, not a fabric simulation.
- Ship motion is a spring-damper response to sampled wave heights, not full rigid-body hydrodynamics.
- Crew figures are hierarchical procedural rigs with posed animations, not skinned characters.
