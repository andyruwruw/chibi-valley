# ChibiValley

A cozy farming sim with a low-poly PS1/neo-retro aesthetic. Think Stardew Valley meets chibi low-fi 3D — expressive animals with real personality, a hand-crafted world full of secrets, and a nature mythology woven underneath it all.

Built in Unity 6 (URP) by a solo developer using an AI-heavy asset pipeline.

---

## Features (Planned)

- Farming with tilling, watering, seasonal crops, and three quality tiers
- Livestock with procedurally assigned personalities, inter-animal bonds, and expressive animations
- Large explorable world with connected zones, puzzles, and secrets
- NPC relationships driven by conversation — multiple relationship bars, deep NPC memory
- Dating and marriage system
- JSON-driven skill trees across multiple tracks (Farming, Livestock, Exploration, Crafting, Social)
- Day/night cycle (30 min real-time days), 4 seasons of 28 days, dynamic weather
- Grid-based inventory with dedicated tool slots
- FMOD adaptive audio with dynamic music layers
- Fully controllable (keyboard/mouse + controller)
- Targets: Windows, Mac, Steam Deck (Linux)

## Style

Low-fi 3D / neo-retro — geometric low-poly models, purposefully pixelated textures, vertex wobble, stylized bloom and god rays, dream-pop color palette, kawaii UI.

---

## Prerequisites

- [Unity Hub](https://unity.com/download)
- Unity **6000.x LTS** (install via Unity Hub — see setup guide)
- Git + [Git LFS](https://git-lfs.com)
- [Visual Studio 2022](https://visualstudio.microsoft.com/) with **Desktop development with C++** workload (required for IL2CPP builds)

## Getting Started

```bash
git clone https://github.com/YOUR_USERNAME/ChibiValley.git
cd ChibiValley
git lfs pull
```

Then open the `ChibiValley/` folder in Unity Hub as an existing project. Unity will compile for 2-5 minutes on first open.

> For full setup including Unity installation, URP configuration, platform build targets, and package installation — see [`docs/setup-guide.md`](docs/setup-guide.md).

## Project Structure

```
ChibiValley/          Unity project root
  Assets/
    _Project/         All game source (art, scripts, scenes, prefabs, data)
    StreamingAssets/  JSON game data files (crops, items, NPCs, skills)
    ThirdParty/       Purchased asset packs (not committed to git)
    Plugins/          Third-party plugins (Yarn Spinner, FMOD, etc.)
docs/                 Design decisions, tool references, workflow guides
GOALS.md              Full feature goals and style reference
CLAUDE.md             AI assistant instructions for this project
```

## Documentation

| Doc | Contents |
|-----|----------|
| [`PLAN.md`](PLAN.md) | Full development roadmap — all phases and tasks with completion status |
| [`NEXT_STEPS.md`](NEXT_STEPS.md) | Current session state, immediate next actions, known issues, established workflows |
| [`docs/asset-list.md`](docs/asset-list.md) | Full asset shopping list — rendering, environment, UI, audio, utilities with prices and recommendations |
| [`docs/goals.md`](docs/goals.md) | Original vision document — full feature list, style guide, art direction reference |
| [`docs/setup-guide.md`](docs/setup-guide.md) | Full Unity 6 installation, URP config, Git LFS, build targets (Windows/Mac/Steam Deck), core packages |
| [`docs/ai-tools.md`](docs/ai-tools.md) | All AI tools evaluated for 3D generation, textures, music, and SFX — strengths, weaknesses, pricing, recommended pipeline |
| [`docs/animal-animation-options.md`](docs/animal-animation-options.md) | Per-animal animation tool options (Cascadeur, Mixamo, ActorCore, Asset Store packs, procedural) with decision triggers |
| [`docs/level-design-tools.md`](docs/level-design-tools.md) | World building tools (Synty packs, Unity Tilemap, ProBuilder) and world structure breakdown by area type |
| [`docs/visual-style-shaders.md`](docs/visual-style-shaders.md) | PS1 shader options, URP post-processing stack, texture import guidelines, color palette reference |
| [`docs/narrative-tbd.md`](docs/narrative-tbd.md) | Open narrative questions — player starting situation and other unresolved story decisions |

## Tech Stack

| Area | Tool |
|------|------|
| Engine | Unity 6 LTS |
| Render Pipeline | URP |
| Dialogue | Yarn Spinner |
| Audio | FMOD Studio |
| 3D Generation | Meshy.ai |
| Character Animation | Mixamo + Cascadeur |
| Environment | Synty POLYGON packs |
| Game Data | JSON (StreamingAssets) |
| Save System | JSON serialization |
| Version Control | Git + Git LFS |
