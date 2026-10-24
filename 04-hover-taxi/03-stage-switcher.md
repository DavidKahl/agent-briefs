# Stage switcher

**Episode:** 04 · **Agent:** an agent connected to Unreal Engine through an MCP (used: Codex) · **Use it:** when you want to show how a scene grew, for a video, a review or a portfolio.

> Refined from the version used in the video.

## The brief

```
In my Unreal project <project path>, make a copy of the playable map <map name> as <map name>_Stages. Don't change the original map or any shared asset. Everything for this goes into the copy or a new folder, /Game/EpisodeStages.

I need to record the stages of how this scene grew. Build a simple stage switcher in the copy, driven by the number keys during Play, that changes only what each stage needs:

1. <stage 1, e.g. the template mannequin as the player, nothing else>
2. <stage 2, e.g. my own character instead of the mannequin>
3. <stage 3, e.g. the vehicle standing static, no motion, no effects>
4. <stage 4, e.g. motion on, effects off>
5. <stage 5, e.g. motion and all effects on>
6. <stage 6, e.g. the full interaction and the final sequence working>

Reuse the existing systems and switch them on and off. Don't duplicate or modify them.

Add one CineCamera at <the recording angle> and one close-up camera at <the detail you want to show>, both selectable for recording. Switching stages must not move the cameras, the main object's resting position or the exposure, so the cuts read as one shot changing.

When you're done: verify every stage in Play, confirm the original map is untouched, then save, reopen the project and check that everything persisted. Report what you could not check.
```

## What it's for

Once a project has moved on, its earlier stages are gone. This rebuilds them as switchable states in a copy of the map, so you can walk around and press a key to see the scene at any stage. In the episode, it's the device that carries the whole Unreal part, and it worked on the first try.

## Tips

- "Save, reopen and check" is there for a reason. An agent can fix something in the running editor session and never save it. It looks done until you restart.
- "Don't move the cameras or the exposure" is what lets you cut between stages in the edit as if it were one shot.
- Ask it to report what it couldn't check. A good agent will tell you, and that list is where your review starts.
