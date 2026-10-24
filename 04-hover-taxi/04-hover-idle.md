# Hover idle

**Episode:** 04 · **Agent:** an agent connected to Unreal Engine through an MCP (used: Codex) · **Use it:** when a hovering vehicle (or anything held up by machinery) looks dead in the air.

> Refined from the version used in the video.

## The brief

```
The <vehicle> hovers in place in the scene, but right now it hangs dead still. Make it feel like the hover system is working to hold it there:

1. A subtle particle effect under each hover pad, suggesting the pads are idle but active and holding the vehicle in the air.
2. A second effect suggesting wind or pressure pushed down and out from the vehicle, toward the player.
3. A very subtle idle motion. The pads can keep it in position, but not perfectly: an automatic controller keeps countering any drift, and that correction should stay visible. No simple up-and-down bob.
4. Let the glow of each pad react to the corrections it's making, and give every pad its own variation, so they don't move in sync.

Keep the motion and the effects switchable independently. Package the hover logic so other vehicles can reuse it. Tell me the maximum drift you end up with.
```

## What it's for

Without point 3, you get the obvious solution: a vehicle bobbing up and down like a toy on a spring. With it, the agent in the episode built an actual controller, a damped spring pulling the taxi back, with several slow frequencies so the motion never repeats and only a few centimetres of drift.

## Tips

- Describe the behaviour and its cause ("a controller that keeps countering drift"), not the animation. The cause is what gets you a system instead of a loop.
- Ask for the maximum drift as a number. It tells you whether "subtle" came out subtle.
- Packaging the logic separately pays off the moment you swap the model. Every new vehicle just inherits the hover.
