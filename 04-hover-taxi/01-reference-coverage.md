# Reference coverage check

**Episode:** 04 · **Agent:** any agent that can read images (used: Codex in VS Code) · **Use it:** before any modelling starts, whenever you build from a concept image.

> Refined from the version used in the video.

## The brief

```
I want to build a 3D model from the concept image below. Before you model anything, check what the image actually gives you.

1. List every major part of the object and rate how well the image covers it: seen clearly, partly seen, or not visible.
2. For every part that is only partly seen or hidden, say what you would have to invent, and how likely that guess is to be wrong.
3. Tell me how the object will be seen in use (<e.g. from below and behind, because it flies>) and which of the invented parts that exposes.
4. Name the views you would need to build it without guessing, ordered by how much they would change the model.
5. Don't start modelling. Wait for my answer.

Present the coverage as a table.

Reference image: <path or attachment>
```

## What it's for

A single concept image usually shows one flattering angle. This brief makes the agent tell you where it would be guessing before it builds anything on those guesses. In the episode, it pointed out that the hover pads underneath, the part a flying car shows most, were exactly the part it would have had to invent.

## Tips

- Step 3 matters more than it looks. A game asset is seen from angles a concept artist never draws: from below, from behind, up close.
- Read the "partly seen" rows carefully. That's where agents quietly fill in details that you only notice once the model exists.
- Follow it up with the [clean reference set](02-clean-reference-set.md), not with more cinematic shots.
