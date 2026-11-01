# Build a flying traffic system in Unreal

**Episode:** 05 · **Agent:** Codex with Unreal editor access · **Use it:** when you have a collection of vehicle models and want a reusable, playable traffic scene, starting without any traffic implementation.

> Refined from the version used in the video.

This combines the final requirements into one brief. It includes a native-code option for the simulation; that is a proposed improvement, not a claim about how the Blueprint version in the episode was built.

## The brief

```text
Build a working sci-fi flying-traffic demonstration in my Unreal project. Implement and verify it;
don't stop at a design document.

Inputs — replace these before starting:
- Unreal project: <absolute project path>
- Playable source map: <map asset path>
- Vehicle folder and selected-model manifest: <absolute paths; say "no manifest" if absent>
- New content root: <asset root, for example /Game/TripoFleetTraffic>
- New demonstration map: <new map asset path under that root>
- Code policy: <C++ allowed, or Blueprint only>
- Existing hero taxi and departure sequence: <actor/asset paths, or none>
- Street/skyline direction: <description, or determine it from the source map>

The result is 100 vehicles in four directional corridors, with two selectable traffic modes: a
simple, single-speed baseline and a livelier version with three speed groups and smooth vertical
overtaking. I can still walk around with the existing player and switch to cinematic traffic views.
Mesh replacement must be a catalog edit, not a simulation rewrite.

1. Inspect, protect and prove the tool connection

Read the project's instructions and inspect the supplied models/manifests before writing. Use
supplied models only; no paid generation or asset downloads. Identify the installed Unreal version,
current map, player, input bindings, available importer and editor automation. Discover the Unreal
MCP connection if available; check the installed bridge/launch documentation before declaring it
unavailable. Use editor Python for asset preparation where appropriate. Confirm actual API
signatures with a small working call instead of inventing nodes or methods.

Duplicate the source map into the requested demonstration map. Back up existing destinations before
changing them and record protected-file hashes. Write new assets only under the new content root,
and scripts, intermediate files, captures and reports under Saved/TrafficIntegration. Source
models/attempts, original maps and shared environment/character/hero assets stay untouched. Save an
explicit package allowlist; never Save All.

Use session-only tool activation where supported. Don't change engine/plugin settings, .uproject,
shared assets or unrelated files without approval. Under "C++ allowed", a small implementation in
the existing project runtime module is authorized: list and back up those source files first. A new
module/plugin or project-file change needs approval. Under "Blueprint only", no native edits or
rebuilds.

2. Choose a small runtime architecture and prove motion early

Use one central traffic manager and one movable ISM pool per vehicle variant: twelve pools for this
fleet, not 100 ticking actors. Disable fleet collision and dynamic shadows. Keep simulation data
separate from meshes and rendering. No vehicle physics, navigation, per-vehicle lights or
per-vehicle Niagara systems are required.

With C++ allowed and a working toolchain, prefer a compact native simulation core with
Blueprint-facing data, controls and editor placement. Otherwise use a centralized Blueprint manager
with the same data contract. Python/MCP are authoring tools, never runtime dependencies. Keep the
runtime independent of the editor.

First prove one imported vehicle moves in an ISM pool in Play. Then prove the simple 100-vehicle
mode, then add passing, cameras and optional hero integration. Keep a working saved result at each
step. If an approach blocks, diagnose the smallest failing case and report it; don't bury the
failure under a larger Blueprint graph or silently reduce the promised fleet.

3. Import once and establish a replacement contract

Use the manifest's selected normalized deliveries, not whichever attempt is newest. Inspect units,
axes, textures and any normalization metadata. Import one taxi first and verify its known physical
length, nose and roof in the map. Calibrate one batch conversion; don't assume a magic yaw
correction or convert units twice. Each pool needs a complete vehicle render mesh; for multipart
exports, assemble an integration-owned mesh without collision helpers, preserving its materials.

Runtime contract: centimetres, nose +X, roof +Z, bounds-centred pivot, unit instance scale. Verify
all vehicle lengths within 1% of their supplied target sizes. Measure actual width and height too;
they drive clearance. If normalization is needed, create documented derivatives in staging, leaving
originals untouched. Report inconsistent source exports rather than hiding them with unexplained
instance transforms.

Create editable catalog records keyed by stable VariantId. Each record contains StaticMesh, material
overrides, measured length/width/height, speed group, passing eligibility, population weight and
glow tuning. Resolve runtime visuals only through this catalog. Rebuilding a pool must refresh
bounds from the selected mesh. A compatible mesh/material replacement changes one record and
refreshes its pool, without recompiling the manager or regenerating placement data.

For these moderate-sized moving meshes, start with conventional LODs at 100/40/12/4% triangles and
screen sizes 1.0/0.10/0.04/0.012; Nanite off. Inspect silhouettes and material sections. Avoid
unnecessary mesh sections and report their draw cost. Don't generate collision or lightmap UVs for
this visual-only fleet.

Preserve supplied PBR colours. Base colour is sRGB; roughness, metallic and AO are linear. For glTF
metallic/roughness, G is roughness and B is metallic; R is AO only when the source explicitly packs
it that way. Use neutral AO otherwise. Read normal-map conventions: a supplied DX normal needs no
additional green flip. Support ordinary textured PBR meshes without requiring vertex colours or
palette recolouring.

Make the hover pads/engine accents readable from the street, not just in the asset viewer. Use a
supplied emissive map or reviewed pad mask, excluding body paint and glass. Expose intensity and
colour in material instances. For the night scene, test cyan (linear RGB 0.015, 0.9, 1.0) and
intensity around 60 as a starting point, then tune at the existing exposure. No extra lights on
every vehicle. Check bloom and distant aliasing before calling it finished.

4. Define editable corridors and population data

Place a traffic anchor aligned to the street, with local +X toward the chosen skyline and +Y across
the street. Use these initial corridor centres in metres relative to that anchor:

Corridor       Centre (X,Y,Z)     Travel direction     Upper passing height
Blue           (0,-10,70)         +X                   82 m
Green          (0,10,70)          -X                   82 m
Red            (190,0,100)        +Y                   112 m
Yellow         (210,0,100)        -Y                   112 m

Each corridor has a 900 m travel interval, a normal level and an upper passing level. Endpoints,
heights, directions and the anchor are editable. Provide GetLaneTransformAtDistance for later hero
placement. Orient vehicle noses along travel; a lane change is vertical within its corridor, not
into opposing traffic.

Distribute 25 vehicles per corridor with physically safe initial gaps. Mix slow and fast types
throughout each corridor instead of clustering all trucks together. Use stable vehicle IDs and a
deterministic seed. Initial placement must meet measured-envelope clearance; report impossible
packing rather than spawning intersections.

Default fleet counts and groups:
taxi_city 12 medium; taxi_executive 12 medium;
courier_sprint 12 fast; courier_parcel 10 fast;
cargo_freighter 8 slow; cargo_utility 8 slow;
limousine 6 slow; sportscar 8 fast;
rescue_ambulance 6 fast; municipal_sweeper 6 slow;
passenger_shuttle 6 slow; patrol_interceptor 6 fast.

Map actual source IDs to these roles without renaming source files. Preserve any legacy ID spelling
in the catalog. Keep counts configurable so adding a variant doesn't require another manager
implementation.

5. Implement the two traffic modes

Key 5: simple traffic. All vehicles have a 32 m/s free-flow target, use the normal level and do not
pass. No driver speed variation. Keep hard clearance and hero-yielding active, so safety can still
reduce actual speed.

Key 6: mixed traffic. Slow 20 m/s, medium 32 m/s, fast 45 m/s, with deterministic individual
variation of up to +/-8%. These are metres per second: convert to 2000/3200/4500 cm/s exactly once.
They correspond to 72/115.2/162 km/h. Free-flow targets are not a promise that every vehicle
maintains that speed in a queue.

Preserve existing keys 1–4 and their authored behavior. If an existing stage 4 already includes
traffic, use the final fleet there while retaining its old departure shot. Don't add a low-poly
fleet or another speed preset. Switching modes preserves player position, control rotation and
camera selection, and reproducibly resets the selected traffic simulation. Only the active
manager/pools tick and render.

Run simulation at fixed 20 Hz and passing decisions at 5 Hz. Cache ordered neighbours per
corridor/level; don't repeatedly scan every vehicle against every other vehicle for ordinary
following. Use compact state and reused buffers, with no per-frame asset loads, actor searches or
component creation. Interpolate previous/current simulation states each rendered frame and submit
one batch transform update per pool.

Keep unwrapped longitudinal distance for interpolation and use positive modulo for route evaluation.
Handle neighbours and reservations across the wrap seam. Don't interpolate a wrap as a flight
through the entire scene. Keep wrap endpoints outside the showcase compositions.

6. Make overtaking reactive, safe and visibly continuous

Fast/medium eligible vehicles react to the actual queue speed ahead, not just the lead vehicle's
preferred speed. Heavy/passenger/utility vehicles and limousines stay below. Eligible vehicles
follow, request a safe upper-lane gap, climb, pass and return below when a safe gap exists. Use
bounded acceleration/braking and a two-second desired following headway. Hard geometric clearance
remains mandatory when traffic compresses into a queue.

Use distinct seeded reaction times, initially 1.4–2.6 seconds, a minimum five-second stay above and
an 8–12.5 second cooldown. Avoid synchronized decisions. A return should speed-match the receiving
lane first. Release obsolete passing targets and let a blocked pass be abandoned only when a safe
return exists. Never force a merge to make the demo look active.

Climb/descent takes about four seconds. Use an explicitly floating-point eased curve, with zero
vertical velocity/acceleration at the endpoints, and subtle eased pitch up to about 3 degrees.
Interpolate the actual rendered ISM transforms. Changing a lane ID alone is not animation. Verify
many intermediate heights during both ascent and descent; guard against integer division or
integer-selected Blueprint math snapping directly between levels.

Use measured vehicle envelopes expanded for safety and body tilt. Maintain at least two metres
between inflated envelopes. Reserve both source and destination levels throughout a transition;
check front/rear clearance over its full duration with conservative relative-motion prediction.
Arbitrate simultaneous requests deterministically and reject unsafe changes while continuing to
follow. Check swept envelopes between simulation steps, including reservations and the wrap seam.
Disabled mesh collision is not a collision-avoidance system.

Choose a deterministic recording seed where a complete ascend–pass–descend is visible from the side
camera using those same reactive rules. Don't substitute a scripted showcase car or random periodic
lane teleports.

7. Add views, a clock and the optional hero merge

C cycles character -> close traffic/skyline shot -> side view showing both levels -> overhead ->
character, in both traffic modes. Traffic continues in every view. The overhead must frame a remote
lane segment without showing the street set. Use actual game-view CineCameras, not continuous
SceneCapture passes. Keep exposure consistent for comparisons; don't brighten the whole map to
compensate for weak vehicle emission.

Pause movement/look while using showcase views and restore the previous input state on return. Mode
changes preserve the selected view and player pose. An active hero cinematic takes camera priority;
returning from it restores the selected review view without leaving stacked input locks.

Expose a simulation clock for Sequencer. Same seed/time must produce the same state regardless of
render rate. Backward seeks reset and replay fixed steps; forward playback advances only the
required steps. Don't replay the whole simulation from zero every rendered frame.

If a hero taxi/sequence was supplied, reuse its E interaction. If none exists, add an interaction in
the copied map: an editable proximity zone accessible from either door, an "Enter Taxi" prompt and E
to start the supplied sequence. A cut that hides the player is sufficient; restore visibility and
controls on completion or interruption. No new entry animation is required.

Reserve the hero's future swept merge route at the authored times, using measured bounds of its
complete assembly and allowances for its motion. Traffic must yield early enough through braking,
waiting or safe passing; don't delay takeoff, teleport cars or rewrite the hero route to conceal a
conflict. Integrate through a small replaceable route/reservation interface.

Preserve existing departure camera work. If a new follow shot is required, follow closer until just
below the wall tops, ease out upward movement, keep tracking until looking along the skyline-bound
departure direction, then ease out rotation. Keep the camera away from the right wall and never
expose empty space beyond the set. If no hero was supplied, deliver the traffic/cameras and document
the merge interface; don't invent or download a replacement hero.

8. Verify the actual saved runtime, not just a reference script

Check 100 instances, twelve active pools, all imports/LODs/materials, correct orientation and
readable pad glow. Demonstrate a catalog-only mesh/material swap, refresh its bounds, verify only
that variant changes, then restore the delivered fleet.

Test following, blocked climbs, simultaneous requests, blocked returns, a moving passing target,
wrap seams and hero reservations at multiple traffic phases. Run ten simulated minutes across ten
seeds against the actual runtime algorithm; use accelerated/headless stepping if available. A
separate reference implementation helps, but its results alone don't validate the game. Report
exactly which tests ran in the engine.

Check equivalent states at equal simulation times under 30/60/120 render schedules, backward seeks
and restarts. Inspect actual instance heights/tilt during transitions and record one complete
passing manoeuvre. Distinguish motion-sampling tests from measured FPS. A few screenshots do not
prove collision avoidance.

Target 60 FPS at 1920x1080 on this machine. After warmup, measure identical views with traffic
disabled, simple traffic and mixed traffic at unchanged quality/exposure. Record hardware, actual
screen percentage, Frame/Game/Draw/GPU timings and traffic CPU mean/p95, including the 20 Hz step
spikes and per-frame instance updates. Aim below 2 ms incremental traffic CPU per rendered frame;
report misses honestly and optimize the measured bottleneck within the code policy. Don't hide cost
by lowering global quality or quoting an amortized step average as the worst frame.

Save only allowed packages, close and reopen the editor, then verify keys 5/6, C, movement
restoration, persisted settings and E/hero integration when supplied. If a check needs physical
input and automation is unavailable, say so. Recheck protected hashes.

Leave the demonstration map open. Deliver a short report under Saved/TrafficIntegration with
controls, architecture, catalog/swap steps, exact additions/changes/backups, measured results,
captures, limitations and retained rebuild inputs. Don't claim tests, screenshots, frame rates or
persistence you could not verify.
```

## What it's for

This gets a fresh session from supplied vehicle models to the final kind of traffic shown in Episode 05: a simple comparison mode, mixed speeds, smooth overtaking, brighter pad accents and closer camera views. The hero merge is optional so the fleet can be built and checked before a departure cinematic exists.

The original work evolved through a low-poly fleet, eight Tripo vehicles, twelve vehicles, several speed presets and later corrections. This brief starts with the final contract and two traffic modes, so the agent doesn't have to rebuild each intermediate version.

## Tips

- **Fill in the inputs first.** This assumes you already have the vehicle files and an Unreal environment. It doesn't authorize paid generation or include the episode's assets. A selected-model manifest also prevents importing an old attempt or an incorrectly scaled export.
- **Choose the code policy deliberately.** The episode used Blueprints. A small native simulation core is an option here because the original Blueprint step measurements did not establish a sub-2 ms traffic budget. Neither implementation earns a performance claim without a measurement.
- **Review rendered motion.** Our lane-state tests passed while the vehicles still snapped between heights: integer-selected math had quantized the height blend. Inspecting intermediate instance transforms caught what logical state tests missed.
- **Keep the three promises separate:** mesh swaps happen through data; passing responds to traffic; cameras make the result readable. A convincing camera angle is not evidence that the other two work.
- **Treat speed as a target and spacing as a constraint.** Two-second headway, vehicle size, population density and a 900 m route can create real queues. Check that the chosen recording seed shows a natural complete pass; don't solve a crowded lane by switching off clearance.
- **Check the small bright features at their intended distance.** The supplied Tripo textures looked fine up close but the hover pads disappeared from the street. Material-level emission made them readable without adding a light to every vehicle. The intensity in the brief is a scene-dependent starting point.
- **Use the engine's evidence.** Epic documents [ISM components](https://dev.epicgames.com/documentation/en-us/unreal-engine/instanced-static-mesh-component-in-unreal-engine) for grouping repeated meshes and [stat commands](https://dev.epicgames.com/documentation/en-us/unreal-engine/stat-commands-in-unreal-engine) for profiling. Verify the APIs and options in the installed engine version rather than copying assumptions from another project.
- **Keep the scope manageable.** This brief builds visual traffic, not a drivable-car game, a new character controller or a replacement environment. Get one pooled vehicle moving before polishing all twelve, and keep each milestone saved and reviewable.
