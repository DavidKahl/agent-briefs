# Clean reference set

**Episode:** 04 · **Agent:** an agent with image generation (used: Codex in VS Code) · **Use it:** after the [coverage check](01-reference-coverage.md), before any geometry.

> Refined from the version used in the video. The first version kept the rainy street scene in every view, and the views didn't agree with each other.

## The brief

```
Generate a clean reference set of the <object> in the attached concept image, for 3D modelling.

- Six orthographic views: front, back, left, right, top and bottom. No perspective.
- The object alone on a plain, neutral grey background. No environment, no weather, no neon, no reflections of a scene, no dramatic lighting.
- Flat, even studio light, so the shapes read from shading, not from highlights.
- The same design in every view: the same proportions, the same part positions, the same number of parts. Where the concept doesn't show a side, design it to match the visible parts, and tell me what you invented.
- The same scale and centring in every view, so the views can be laid on top of each other.

Save every view in the project, together with the exact prompt you used for it.

When all six exist, compare them with each other and list every place where they disagree (position, size or number of parts), before anyone builds from them.
```

## What it's for

Cinematic views look great and are useless for modelling: the engines sit somewhere else in every image, the proportions drift, and half the details are reflections. Six neutral, matching orthographic views give a modelling agent something it can actually measure against.

## Tips

- The comparison at the end is the important part. Image models drift between views, and it's much cheaper to catch that here than in the mesh.
- Once the brief works for one object, it scales. Give it a list of objects and it works through the list, one clean set each.
- Keep the original concept image for colour and mood. The clean set is for shape only.
