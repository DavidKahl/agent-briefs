# Build the TripoFleet asset generator

**Episode:** 05 · **Agent:** a coding agent (used: Codex) · **Use it:** before the Unreal traffic work, when you want a repeatable way to turn vehicle reference images into a fleet of textured models.

> Refined from the version used in the video.

This reconstructs the asset-generation project as one standalone brief, with the recovery and validation requirements stated upfront. It is a prompt for building the tool, not permission to start a paid generation batch.

## Quicker route: reuse Tripo-Fleet

The working implementation is available at **[DavidKahl/Tripo-Fleet](https://github.com/DavidKahl/Tripo-Fleet)**. Reusing it saves agent tokens and implementation time. It does not remove Tripo's generation charges, and the generated fleet models are not bundled with the code. See the repository's README and [image terms](https://github.com/DavidKahl/Tripo-Fleet/blob/main/ASSETS.md) for what is included.

If you want to use the tool rather than build another implementation, give your agent this shorter brief:

```text
Help me use https://github.com/DavidKahl/Tripo-Fleet in <destination folder>.
Inspect an existing checkout before changing it; otherwise clone the repository.
Read its README and instructions, then use the existing implementation. Don't
rebuild the API client or read every source file unless a specific problem needs it.

Install the locked dependencies with uv sync --locked, run uv run pytest, and
validate the included taxi with uv run tripo-fleet plan --only taxi_city. Confirm
that uv run tripo-fleet run --only taxi_city is a dry run, without uploading images
or submitting a generation. These checks should work without an API key.

Then help me configure <my reference-image folder and vehicle dimensions> through
the existing fleet configuration. Explain which files to edit and how the
normalized models will feed the Unreal traffic brief. Never print credentials.
Don't add --go or --force without my explicit approval for that paid run.
Report any incompatibility and make only the changes actually needed.
```

## The brief

Use this longer version when building the project itself is the goal:

```text
Build a small Python project that turns multiview vehicle references into a
reusable fleet of textured 3D models through Tripo's REST API. Implement the tool,
offline tests and documentation; don't stop at an architecture proposal.

This is the from-scratch route. An existing implementation is available at
https://github.com/DavidKahl/Tripo-Fleet and is the quicker, lower-token alternative
if I only need to generate assets. Use official API documentation as the authority
for current requests. If consulting that repo, read the relevant modules rather
than ingesting all its source, images and production records.

Inputs — replace before starting:
- New project folder: <absolute destination path>
- Reference-image folder: <absolute path>
- Fleet definition: <vehicle IDs, families and target lengths, or a supplied JSON>
- Blender executable: <path, discover locally, or unavailable>
- Live generation: not authorized by this build request.

Read local project/agent instructions. If the destination isn't empty, inspect
its Git state and preserve existing work. Keep changes in the requested project.

Use Python 3.11+, uv, a src layout and a tripo-fleet command. Keep dependencies
small: an HTTP client, a GLB/glTF library, image processing and pytest are enough.
Separate configuration, API transport, attempt runner, FBX conversion, geometry
normalization and reporting. No GUI, server, database service, Unreal project or
GeometryForge installation is required. An adapter for an existing fleet recipe
may be optional; the project must work without a private recipe or machine path.

1. Inspect the API and make the configuration explicit

Read the current official P-series multiview, upload, task-query, authentication,
rate-limit and billing documentation. Record the relevant URLs and checked date.
Don't copy outdated field names, hosts, limits or prices from memory.

Keep the API base URL configurable. At the time of the episode, the workflow used
https://openapi.tripo3d.ai/v3 with POST /files, POST /generation/multiview-to-model,
GET /tasks/{task_id} and GET /account/balance. Verify these for the current API and
the user's account region before live use; don't send a key to guessed hosts.

Use fleet.json with global generation defaults and per-vehicle overrides. Each
vehicle needs a stable ID, family, target length_m, optional nominal width/height,
source orientation and view paths. Resolve relative paths from the configuration
file, not the current working directory. Reject duplicate/unsafe IDs, non-positive
sizes, unknown settings and unsupported model/option combinations before upload.

The episode profile was P2-20260801, quad=true, face_limit=25000, textures and PBR
enabled, standard texture quality, and geometry/texture seeds of zero. Make these
editable and validate availability and current limits. Don't silently substitute
a different model, texture tier or paid operation.

Support any fleet size. For an episode-like example, configure city/executive
taxis, sprint/parcel couriers, cargo freighter/utility vehicles, a limousine,
sports car, ambulance, sweeper, passenger shuttle and police interceptor. Sizes
belong in data, not code. Include a six-metre taxi example and a small valid
configuration; report missing reference images rather than inventing them.

2. Validate references and provide a useful offline plan

Discover front, left, back and right in concepts/<vehicle-id>/, with explicit path
overrides. Keep view labels unambiguous. This is four views of one design under
consistent lighting, not four unrelated vehicle pictures. Top/bottom views are
not substitutes for the supported input slots.

Check decodability, file type, dimensions, file size, front-view presence and view
count against the current API requirements. Validate the chosen upload route's
formats too; the generation endpoint and file upload need not accept the same set.
The episode client used PNG/JPEG, at least two views including front, and a
256-pixel minimum. Flag conflicting or missing inputs before spending anything.

plan must work without a key or network. Show selected vehicles, resolved views,
input hashes, effective generation settings, existing attempts, readiness and
estimated credits. Label estimates and their source/date; never present the
episode's historical 110-credit jobs as a guaranteed current price. Separate the
generation fingerprint (images/settings) from local normalization settings, so
changing a target size doesn't imply another paid generation.

3. Put paid work behind a deliberate gate

run is a dry run by default: no uploads, authenticated requests or generation.
Implement --go for a deliberate paid run, --only for a subset, bounded --parallel,
and --force for an explicitly requested new attempt of an already delivered model.
A --force dry run still must not spend. Show the exact selection/settings and cost
estimate before submission. Respect an approved spending limit; if a reliable cost
bound is unavailable, say so instead of claiming an enforced monetary guarantee.

Building this tool does not authorize the agent to add --go. Finish offline first;
show me a one-vehicle plan and wait for approval covering that live run. Validate
one downloaded vehicle before proposing the rest of the fleet. A batch expansion
or another attempt needs authorization for that additional paid work.

Load TRIPO_API_KEY through one configuration function, from the environment or an
explicit local .env. Provide a blank .env.example and ignore private env files.
Never print, serialize or commit the key or Authorization header. Do not scan
unrelated workspaces for credentials. balance is a separate authenticated read,
not a generation, and plan/report/local normalization must remain usable offline.

4. Build a resumable runner, not a batch of disposable requests

For each approved vehicle: upload the validated views, submit one documented
multiview payload, persist the returned task ID immediately, poll to a terminal
state, download outputs promptly, then process them locally. Use a small number
of workers within documented/account limits; don't assume every account allows
five simultaneous jobs. Show per-vehicle progress and preserve state on Ctrl-C.

Use output/<id>/attempt-01, attempt-02, etc. Allocate attempts safely under
concurrency. Preserve every attempt, including failures. Write atomic checkpoints
and an append-only, thread-safe ledger with timestamps, stage transitions, input
fingerprints, effective settings, task IDs, errors and measured credit consumption.
Keep API status and local download/conversion/normalization status separate.
"API success" is not "usable local delivery".

Skip a matching completed delivery only when its expected outputs exist and
validate. Explain stale generation inputs instead of mislabelling an older result.
If normalization failed after API success, resume that attempt; don't pay again.

Use timeouts and bounded retry/backoff for safe reads and permitted uploads,
honouring rate-limit responses and rewinding uploaded file streams before retry.
Don't automatically retry an ambiguous paid submission. Unless current documented
idempotency guarantees make it safe, a lost response may mean the task exists and
was charged. Mark that case submission_unknown, preserve the evidence and request
reconciliation through the developer platform. Never invent a task ID or hide
another paid attempt inside "resume".

resume uses known task IDs and retained raw downloads, never submits generation.
Handle terminal errors distinctly from local polling timeouts; bound unknown
statuses instead of treating them as success. Keep failures inspectable with
actionable, credential-safe messages.

5. Download first; convert locally when necessary

Download each successful task as soon as its result is ready, not after the entire
batch finishes or a long conversion queue clears. Signed URLs expire. Query the
known task for usable output links when recovery needs them; don't regenerate a
model just because a link expired.

Use a separate unauthenticated download transport for result/CDN URLs: don't send
the API bearer header to storage hosts. Stream to .part, validate, then rename
atomically. Preserve original model bytes, preview and private task response.
Identify the actual file format; don't assume a model_url contains a GLB or trust
its filename alone.

Support raw GLB and FBX. If the native result is FBX, preserve raw.fbx and convert
locally using background Blender in an isolated scene. Keep extracted/packed
textures, a conversion log and fbx-inspection.json with native polygon counts.
Write converted.glb as a derivative, with materials/UVs intact. Detect Blender via
configuration or PATH, handle spaces in paths, and give an actionable error if
it is unavailable. Don't replace this local step with a paid API conversion.

A missing Blender or a local conversion error must leave the paid result intact
and recoverable later. Don't overwrite an artist's working scene or re-download
over a verified raw file. Requested native polygon budget, actual native polygons
and triangulated GLB triangles are different measurements: report each honestly.

6. Normalize to one stable asset contract

Deliver static GLBs in metres, +Y up, +Z nose, with the pivot at the complete
vehicle's bounds centre. Use declared source axes and inspect the first model's
actual nose against its references. Uniformly scale the longitudinal extent to
length_m, preserving generated proportions. Width/height are review references,
not instructions to stretch the vehicle into a box. Don't use the longest axis
as a substitute for knowing which way the car faces.

Compute bounds from transformed scene geometry, including all render parts.
Bake the normalization into vertices and reset node transforms only when safe.
Transform normals/tangents correctly and preserve winding, UVs, colours, materials
and texture references. For mirrored/shared geometry or unsupported skins/morphs,
use a validated documented root-transform fallback or stop with a clear reason;
don't silently corrupt the file to claim everything is baked.

Extract maps per material and write material.json describing their slots/channels.
For glTF metallic/roughness, G is roughness and B is metallic. R is AO only when
the material actually shares the packed occlusion image; otherwise retain separate
AO or document its absence. Preserve source textures, and derive an explicitly
labelled DirectX normal from the glTF normal by one green-channel flip. Keep the
GLB using its glTF normal; the DX map is a separate engine-use derivative. Record
the convention so the Unreal importer doesn't flip it again.

Record transform/method, source and output hashes, input/output dimensions,
target length, material/texture paths, triangle count and warnings in normalize.json.
Verify target length within 1%, a centred pivot, readable GLB and resolvable maps.
Changing source_forward or length_m must be fixable locally without new credits.
Always normalize from retained raw/converted input, not a previously normalized
output; repeat runs must not accumulate scale or flip the normal map twice.
Protect manually edited derivatives before a requested normalization overwrite.

7. Make results easy to inspect and hand off

Provide plan, run, resume, normalize, balance and report commands with clear help
and exit codes, plus --fleet and --output path overrides. Document where global
flags belong. Check success from actual local delivery state, not task status
alone. Keep commands working when launched from another directory.

report writes Markdown and optional JSON for every attempt, including failures:
task/local status, actual returned credits, API task time, local wall time, model
size, dimensions, native polygons where available, GLB triangles and warnings.
Use null/unknown for missing cost data, not zero; don't invent refund claims.
Overlapping per-job durations must not be summed and called batch duration.

Let the user select an accepted attempt per variant. Export a machine-readable
selected-model manifest with stable IDs, relative model/material paths, measured
bounds, conventions, attempt provenance and hashes. Don't assume newest is best
or silently change an accepted selection during recovery. This manifest is the
handoff to the Unreal traffic project; this tool does not modify that project.
Document the distinction between the GLB contract and Unreal's centimetre/+Z-up
contract. Don't hardcode the episode scene's yaw correction into all exports.

Keep raw payloads, file tokens, signed URLs and task/account identifiers in ignored
local output. Publish only sanitized documentation/report examples. Ignore custom
output directories too. Don't bundle a generated-model pack or claim image rights
come from the code's license. Separate supplied reference-image terms if publishing.

8. Prove the offline workflow before requesting a live test

Use mocked HTTP and tiny synthetic model/image fixtures. Tests must need no key,
network or paid generation. Cover invalid views/settings, dry-run zero requests,
--force without --go, safe retries, an ambiguous submission with no resubmission,
timeout/resume with the same task ID, API success/local failure, partial downloads,
and download recovery without forwarding credentials.

Test nested transforms, multi-part bounds, normals, uniform scale, pivot centring,
texture channels, repeated normalization and raw-file preservation. Include the
fallback/rejection cases and separate native-face/triangle counts. Exercise the
FBX conversion with a small local fixture when Blender is available; clearly
report a skipped Blender test rather than pretending it ran. Test spaces in paths,
config-relative paths, concurrent attempt/ledger writes and a fresh-process resume.

Ship pyproject.toml, a dependency lock, a small example fleet, .env.example,
.gitignore, CLI help and a README with exact offline-first commands, recovery steps
and the verified API references. Persist the spending/no-auto-retry rules in the
project's agent instructions so a future session doesn't start a paid batch.

Run the offline suite and one complete mocked upload-to-delivery workflow. Leave
the real one-vehicle plan ready for review. State precisely what was tested,
what needs visual inspection, and what remains unverified against the live API.
Do not present mock models, estimated credits or offline timings as generated
production results. No live generation unless I explicitly authorize it next.
```

## What it's for

This builds the asset-production tool used before the [Unreal traffic brief](01-unreal-traffic-system.md): consistent vehicle views go in; selected, normalized GLBs, textures and traceable reports come out. It does not build the Unreal map or the traffic simulation.

The important parts of the workflow are keeping paid tasks recoverable and separating generation from local processing. In the episode, an API-successful result initially failed local loading because it was FBX. Local conversion recovered it; another generation was not needed to repair that delivery.

## Tips

- **Use the existing repo to save tokens.** [Tripo-Fleet](https://github.com/DavidKahl/Tripo-Fleet) is the short route for running the workflow. Read its README, configure your inputs and inspect only the module you need to change. The longer brief is for learning or building another implementation; its stronger checkpoint/fingerprint requirements are design requirements, not a promise that every detail already exists in the reference repo.
- **Start with one vehicle.** Validate its orientation, physical size, silhouette and textures before spending on twelve. An exported forward-axis setting is not proof that the generated shape's nose is correct.
- **Prepare consistent views first.** The [clean reference-set brief](../04-hover-taxi/02-clean-reference-set.md) helps with that. Use the supported four side views for multiview generation; don't substitute a top view just because it exists in a six-view reference sheet.
- **Check current API documentation.** Use Tripo's [P-series multiview](https://developers.tripo3d.ai/en/docs/generation-multiview-to-model/p), [file upload](https://developers.tripo3d.ai/en/docs/files), [task query](https://developers.tripo3d.ai/en/docs/task-query) and [account](https://developers.tripo3d.ai/en/docs/account) references. Supported options and upload formats can differ by route; keep estimates separate from returned task consumption.
- **Keep the expensive and cheap operations separate.** New geometry/texture generation is paid work. Fixing local scale, orientation or an FBX conversion should reuse the downloaded model. Resuming a known task should never quietly submit another one.
- **Preserve the original result.** Keep raw downloads and failed attempts while reviewing. A cleaned-up report is not a reason to discard the records needed to explain a charge, compare attempts or rebuild the export.
- **A successful export is not artistic approval.** Undersides, hover-pad detail, proportions and generated defects still need a human look. This workflow makes results consistent and inspectable; it cannot guarantee that every generated model is a good design.
