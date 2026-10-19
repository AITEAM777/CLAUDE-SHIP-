# Volcanic Island Diorama

An interactive 3D miniature in a single file (`index.html`): a square cutaway block of the Pacific with a tropical volcanic island on top, a fringing reef, fish swimming behind the glass-like water face, and layered rock strata down the sides. You can trigger an eruption, change the weather and time of day, steer the ocean current and splash the water with the pointer.

Three.js r160 (OrbitControls, BufferGeometryUtils, RoomEnvironment) loads from jsDelivr, and the fonts load from Google Fonts. The terrain, plants, coral, fish, textures and sound are all generated in the page. The default scene is fixed by the JSON block `#scene-config`.

## Run it

- Open `index.html` in a current desktop or mobile browser that supports WebGL 2. The first load needs an internet connection.
- If module scripts are blocked on `file://`, serve the folder with `npx serve .` or `python3 -m http.server`.

## Controls

- **Pointer:** drag to orbit and scroll or pinch to zoom.
  - Click the water to splash (a double "blub" with a ring, bubbles and spray).
  - Drag across the water to draw a wake. A drag that starts on the water never orbits.
- **Weather:** Clear, Cloudy, Rain, Storm, Thunderstorm; Day, Sunset, Night; sliders for wind, rain and waves.
- **Volcano:**
  - Idle, Erupt, Calm, plus an intensity slider.
  - An eruption runs Rumble → Erupt → Decline → Ash.
  - Calm starts the decline; Idle clears everything quickly.
- **Ocean and current:**
  - Directions: Right → Left, Left → Right, Forward (toward the brass label), Backward, Off.
  - Sliders for current speed and water clarity, plus Splash centre and Smooth water buttons.
  - Directions are fixed to the diorama. The mini map shows the current and where you are looking from.
- **Marine life:** Add, Remove, Hide/Show fish, a fish count slider (0–200) and a fish speed slider.
- **Camera and simulation:** Reset view, Zoom in/out, Top view, Pause/Resume, Reset scene, Sound, Auto-orbit.
- **Keys:**
  - `1`–`5` weather presets, `E` erupt, `C` calm.
  - `R` reset view, `T` top view, `Space` pause.

## How it works

- **Height field:** the island, reef shelf, crater and sea floor come from one seeded height function. Everything shares it: terrain, strata walls, plant and coral placement, fish avoidance, pointer picking and the lava paths.
- **Water:**
  - A refraction pass renders the opaque scene with its depth. The water surface and the four cutaway faces then shade what lies behind them using the real path length through the water (Beer–Lambert absorption plus in-scattering), with Fresnel reflection, sun glints and foam.
  - The wind waves are a sum of sines with the same parameters on the CPU and the GPU, so the moored skiff, foam and bubbles ride the drawn surface.
- **Current:**
  - A smoothed velocity moves an advection offset for the waves, surface chop, foam streaks and caustics.
  - A 64 × 64 flow field bends the current around the coast, carrying the foam flecks and the ripple field.
  - Reversing the current visibly reverses all of this motion.
- **Ripples:** a 160 × 160 damped wave equation runs on the CPU at a fixed 60 Hz, reflects off the coast and the block walls, and is advected by the current. It is uploaded as a half-float texture. Clicks, wakes, rain drops, bombs and lightning strikes all add disturbances to it.
- **Eruption:**
  - Sorted, instanced smoke and ash puffs make the column, with buoyancy, an umbrella top and wind drift.
  - The eruption also adds additive spark fountains, ash fall and pooled volcanic bombs on ballistic paths.
  - The bombs bounce and cool on land, or splash and sink in the sea. Their range is limited so they land inside the block.
  - Lava flows follow pre-traced downhill channels. They advance, crust over, cool and steam where they reach the sea.
- **Fish:** eight procedural species with tail-beat and turning bends in the vertex shader. They school, hold reef or anemone homes, avoid the floor, shallows, surface and walls, and scatter from splashes.
- **Weather:** every parameter eases toward its preset. Clouds hover over the block and drift with the wind. Rain is GPU-animated and clipped to the footprint, and lightning lights the scene and splashes the sea when it strikes water.

## Approximations

- The water is a height-field and shading approximation, not fluid dynamics.
- Refraction is screen-space, so objects hidden from the camera can't appear through the water.
- Smoke is lit billboards, not volumetric rendering.
- Fish behaviour is a steering model, not true flocking physics.
- The terrain is stylised: the relief is exaggerated about twice against the 10 km block.
- Pixel ratio and particle counts step down automatically on slower GPUs.
