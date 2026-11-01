# Episode 05 · The Tripo fleet

A fleet of Tripo-generated vehicles becomes flying traffic in Unreal: different speeds, vertical overtaking, visible hover pads and camera views that show the traffic working. The vehicles can be replaced through a catalog, without rewriting the traffic system.

## The briefs

1. [Build the traffic system](01-unreal-traffic-system.md): one standalone brief for importing the fleet, building the simulation, adding the two traffic modes and checking the saved result.
2. [Build the TripoFleet generator](02-tripo-fleet-generator.md): turn consistent reference views into a resumable asset-generation project, with local conversion, normalization and measured reports. Includes a shorter prompt for reusing the existing tool.

These briefs rewrite the workflow using what we learned while building the final result. They bring the requirements together upfront; they aren't transcripts of the prompts used during the episode. The traffic brief offers native simulation as an option, while the episode's traffic simulation used Blueprints.

For the asset-to-scene workflow, start with the generator brief, then use its normalized models with the Unreal brief. To save implementation time and agent tokens, use the existing [Tripo-Fleet repository](https://github.com/DavidKahl/Tripo-Fleet) instead of rebuilding the generator. Model generation still uses your Tripo API credits; the repo is not a generated-model download pack.

The generator starts with reference images; the traffic brief starts with vehicle models and your own Unreal environment. This repository contains the instructions, not the models, textures, marketplace environment or finished Unreal project. Replace the placeholders before use, and choose the Unreal brief's code policy when building the scene.
