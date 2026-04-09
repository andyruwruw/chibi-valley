# ChibiValley — Claude Instructions

## Developer Profile

**Background:** Experienced software engineer with deep web and backend experience (treat as senior level for all code, Git, CLI, and tooling discussions — be terse and technical).

**Unity / 3D experience:** None. Treat all Unity Editor interactions, 3D modeling, and animation topics as if explaining to someone who has never opened these tools. Every Unity step must be fully explicit — no assumed knowledge of panels, menus, or concepts.

**Analogy bridge:** When explaining Unity concepts, map them to web/backend equivalents:
- `GameObject` ≈ DOM element
- `Component` ≈ middleware / decorator
- `Prefab` ≈ React component
- `Inspector` ≈ DevTools property panel
- `Scene` ≈ a page/route
- `ScriptableObject` ≈ a config/data schema object
- `Unity Package Manager` ≈ npm
- `StreamingAssets/` ≈ `public/` or `static/` folder

---

## Project Summary

**Working title:** ChibiValley  
**Genre:** Cozy farming sim (Stardew Valley feel, Fields of Mistria skill system)  
**Art style:** Low-poly isometric 3D, toon pixel art aesthetic — Flat Kit cel-shading, ProPixelizer pixel art rendering, pixelated textures, stylized lighting, chibi characters  
**Setting:** Original world, vaguely European pastoral, cozy nature mythology underneath  

**Engine:** Unity 6 LTS  
**Render pipeline:** URP (Universal Render Pipeline)  

**Key tech decisions:**
- Dialogue: Yarn Spinner
- Audio: FMOD + Suno (AI music) + royalty-free tracks + SFX packs
- Game data: JSON files in `StreamingAssets/`
- Save system: JSON serialization to disk
- Skill tree: JSON-driven, multiple tracks, permanent unlocks
- Version control: Git + Git LFS on GitHub
- IDE: Visual Studio 2022 (primary), VS Code (quick edits)

**Asset pipeline:**
```
Concept art  → Midjourney / Leonardo.ai
3D models    → Meshy.ai (primary) / Tripo3D (props)
Cleanup      → Blender (minimal — last resort only)
Rigging      → Mixamo (humanoids) / ActorCore (animals)
Animation    → Mixamo library + Cascadeur (personality/custom)
Textures     → Leonardo.ai / Stable Diffusion (pixel art)
Environment  → Synty asset packs (POLYGON series)
```

**Reference docs:** See `docs/` for detailed tool comparisons, workflow guides, and TBDs.

---

## Next Steps & Workflow Log

`NEXT_STEPS.md` (project root) is the active working document. Always keep it updated.

**Format rules for NEXT_STEPS.md:**
- The **Immediate Next Actions** section must always contain the single next task broken into explicit window-by-window steps — name every panel, every menu path, every button the user must click, in order
- Write as if the user has never seen Unity before — no assumed knowledge of where anything is
- Always specify the exact Unity version in use: **Unity 6000.3.13f1**. If a menu path, button label, or panel name differs in this version vs. older tutorials, note the difference
- Every step that opens a window must name that window. Every step that changes a setting must say what the setting is called AND where it lives in that window
- **Established Workflows** section: whenever a repeatable process is figured out (import a model, create a material, add a renderer feature), write it down here in full so it never needs to be re-derived
- **Known Issues** section: track blocking problems with their current status and resolution options

Update `NEXT_STEPS.md` at the end of every session or whenever a workflow is established or a task completes.

---

## Task Tracking

`PLAN.md` is the canonical development checklist. Keep it up to date:

- When starting a task, change `[ ]` to `[~]` (in progress)
- When a task is fully complete, change `[~]` to `[x]`
- If a task is intentionally skipped or deferred, mark it `[-]` with a note
- Never mark a task complete if it is partially done — use `[~]`
- After completing any task, check if dependent tasks in later phases are now unblocked and note it

---

## Documentation Rules

- All public C# members must have XML doc comments (`/// <summary>`)
- Every new game system gets a corresponding doc in `docs/`
- Update existing docs when systems change — never let them go stale
- Unity-specific guides must include full step-by-step instructions (see Unity Instructions below)

---

## Code Quality & Linting

Follow standard C# / Unity conventions:

- **Classes, methods, properties:** `PascalCase`
- **Private fields:** `_camelCase` (underscore prefix)
- **Constants:** `ALL_CAPS` or `PascalCase` in a `static class`
- No magic numbers — use named constants or data-driven config
- No `TODO` comments in committed code — log open questions in `docs/narrative-tbd.md` or the relevant doc file
- Prefer composition (components) over inheritance
- Keep MonoBehaviour scripts thin — logic goes in plain C# classes, MonoBehaviours just wire things together

---

## Asset Decisions

Whenever an asset decision comes up (3D tools, audio tools, shader packs, Unity Asset Store packs, AI tools):

1. **Always present a comparison table first** with columns: Name | Best For | Strengths | Weaknesses | Pricing
2. **Log all evaluated options** to the relevant `docs/` file — even options not chosen
3. **Never commit to one tool** without showing alternatives
4. **Flag per-asset decisions** — some choices (especially animal animation) need to be made per-item, not globally

---

## Unity Instructions

For any task that involves the Unity Editor:

- Write **explicit numbered steps** — assume the user cannot infer any navigation
- **Always write full menu paths** — never abbreviate (correct: `Edit → Project Settings → Player → Other Settings`, never: "go to Player Settings")
- **Name every panel** the user needs to look at (Hierarchy, Inspector, Project window, Scene View, Game View, Console)
- **Explain what a thing is before saying to click it** — e.g., "The Inspector is the properties panel on the right — it shows the settings for whatever you have selected."
- For non-Unity tasks (Git, CLI, code), revert to terse technical style

---

## Platform Targets

- **Primary:** Windows (PC)
- **Secondary:** Steam Deck (Linux build), Mac
- **Future:** Console (design with controller support in mind from day one)
- **Deferred:** Multiplayer (singleplayer ships first)
