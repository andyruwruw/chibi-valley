# Asset Creation Workflow

End-to-end workflow for creating game assets using AI tools and importing them into Unity with the ChibiValley PS1/neo-retro aesthetic. Follow this document for every new asset — do not skip phases.

---

## Asset Type Classification

Before starting, classify the asset. The workflow branches at rigging/animation.

| Type                   | Examples              | Rigging?                    | Animation?                    |
| ---------------------- | --------------------- | --------------------------- | ----------------------------- |
| **Character**          | Player, NPCs          | Yes — Mixamo                | Yes — Mixamo + Cascadeur      |
| **Animal**             | Pig, cow, chicken     | Yes — Asset Store / AccuRIG | Yes — Asset Store + Cascadeur |
| **Prop (static)**      | Crate, fence, mailbox | No                          | No                            |
| **Prop (interactive)** | Tool, chest, door     | No                          | Optional (simple open/close)  |
| **Environment**        | Rock, tree, building  | No                          | No                            |

---

## Phase 1: Concept & Reference

### 1.1 — Define the asset

Write a one-paragraph brief before touching any tool. Include:
- What the asset is and its role in the game
- Approximate real-world size relative to the player character
- Style notes: primary color, mood, any specific shape language
- Poly count target (see table below)
- Whether it will be animated

**Poly count targets:**

| Category | Max Tris |
|----------|----------|
| Player / NPC character | 800–1,500 |
| Animal | 400–1,000 |
| Large prop / building | 300–800 |
| Small prop / item | 50–300 |
| Environment piece (rock, tree) | 100–400 |

These are soft targets. PS1 aesthetic tolerates slightly higher counts if silhouette reads as simple. Reject AI outputs over 2× the target unless retopology is trivially fast.

### 1.2 — Generate a visual reference (Midjourney)

Use Midjourney to produce 2–4 reference images before generating 3D. This anchors the AI mesh generators and gives you a reject/accept bar.

**Prompt formula:**
```
[subject], low poly, chibi, neo-retro, PS1 aesthetic, isometric view,
bright saturated colors, dream-pop palette, clean silhouette,
simple geometric shapes, no texture, flat render, white background
```

**Example for a pig:**
```
chibi pig, low poly, PS1 aesthetic, neo-retro, simple geometric shapes,
bright pink, warm tones, clean silhouette, isometric view, white background
```

Save the best 1–2 images. You will use these as the image-to-3D input in Meshy.

---

## Phase 2: 3D Mesh Generation

### 2.1 — Choose the right tool

| Asset Type | Primary Tool | Fallback |
|------------|-------------|---------|
| Characters, animals, organic props | **Meshy.ai** | Tripo3D |
| Hard-surface props (tools, crates, mechanical) | **Tripo3D** | Meshy.ai |
| Do NOT use | Rodin — too high-poly for this aesthetic | |

### 2.2 — Generate in Meshy.ai

1. Go to [meshy.ai](https://www.meshy.ai) and log in.
2. Click **"Image to 3D"** in the left sidebar (preferred — uses your Midjourney reference).
   - If you don't have a good reference image, use **"Text to 3D"** instead.
3. **Image to 3D settings:**
   - Upload your best Midjourney reference image.
   - **Art Style:** select `Low-Poly` if available, otherwise `Cartoon`.
   - **AI Model:** use the latest available model.
   - Leave other settings at default for the first pass.
4. Click **Generate**. Wait for the result (typically 1–3 minutes).
5. Evaluate the result (see Phase 3). If unsatisfactory, click **Regenerate** — do this up to 3 times before switching approach.

**Text to 3D settings (if used):**
- Prompt format: `[subject], low poly, chibi, PS1 style, [key colors], simple geometry, clean silhouette`
- Negative prompt: `high poly, realistic, detailed texture, photorealistic, complex mesh`
- Art Style: `Low-Poly` or `Cartoon`

### 2.3 — Texture baking in Meshy

After mesh generation, Meshy will offer a **"Generate Texture"** step:

1. Click **"Generate Texture"** on your approved mesh.
2. In the texture prompt, enter: `[subject], pixel art, low resolution, flat colors, PS1 style, [color description], no shading, no highlights`
3. Generate 2–3 texture variants. Save all of them — you can swap in Unity later.
4. **Do not finalize textures in Meshy** — you will replace or heavily modify them in Phase 4.

### 2.4 — Export from Meshy

1. Click **"Download"** on your approved mesh.
2. Export format: **FBX** (required for Mixamo rigging) or **GLB** (fine for static props).
3. Texture export: also download the generated textures as **PNG**.
4. Save both to: `[your local working folder]/raw/[asset-name]/`
   - e.g., `raw/pig/pig_mesh.fbx`, `raw/pig/pig_texture_v1.png`

### 2.5 — Tripo3D alternative (hard-surface props)

1. Go to [tripo3d.ai](https://www.tripo3d.ai) and log in.
2. Use **"Image to 3D"** with your reference, or **"Text to 3D"**.
3. The workflow is the same as Meshy — generate, evaluate, export FBX/GLB + PNG textures.
4. Tripo3D exports may have cleaner topology for geometric shapes but fewer style controls.

---

## Phase 3: Mesh Evaluation & Cleanup

### 3.1 — Evaluation criteria

Open the downloaded FBX in **Blender** (File → Import → FBX) for a quick inspection. You are checking:

| Check | Pass | Fail — Action |
|-------|------|----------------|
| Silhouette reads clearly | Recognizable at thumbnail size | Regenerate with better reference |
| Poly count | Within 2× target | Decimate in Blender (see 3.2) or regenerate |
| No floating geometry | All parts connected or intentionally separate | Delete floating verts in Blender |
| No interior hidden faces | Clean from all angles | Delete in Blender |
| Roughly correct proportions | Chibi/stylized reads correctly | Regenerate or scale bones post-rig |

**Blender is a last resort.** If more than 30 minutes of Blender work is needed, regenerate the mesh. The goal is: open in Blender, spend 0–15 minutes, close Blender.

### 3.2 — Decimation in Blender (if over poly count)

Only do this if the mesh topology is clean but too dense.

1. Open Blender. File → Import → FBX → select your file.
2. Click on the mesh in the viewport to select it (it will turn orange).
3. In the right-side **Properties panel** (icon looks like a wrench), click the **Modifier Properties** tab (wrench icon).
4. Click **"Add Modifier"** → search for **"Decimate"**.
5. Set **Ratio** to reduce poly count. Start at `0.5` (50% reduction). Adjust until the silhouette still reads correctly.
6. Click **"Apply"** to finalize.
7. File → Export → FBX → save as `[asset-name]_decimated.fbx`.

### 3.3 — Approve and file

Once the mesh passes evaluation:
- Rename the export: `[asset-name]_clean.fbx`
- Move to: `raw/[asset-name]/[asset-name]_clean.fbx`
- Note the actual tri count in your asset brief.

---

## Phase 4: Texture Generation

The goal is **low-resolution pixel art textures** that feel hand-crafted. Meshy's auto-textures are a starting point only — they typically need replacement.

### 4.1 — Texture size targets

| Asset Type | Resolution |
|------------|-----------|
| Characters, animals | 64×64 |
| Large props / buildings | 64×64 |
| Small props, items | 32×32 |
| Terrain / ground tiles | 64×64 |

Never exceed 128×128. The pixelated look requires Point (no filter) sampling — high-res textures defeat the aesthetic.

### 4.2 — Generate textures with Leonardo.ai

Leonardo.ai is preferred for tiling textures and game-specific art. Use Stable Diffusion locally for batch work or when you need full control.

**Leonardo.ai steps:**

1. Go to [leonardo.ai](https://leonardo.ai) and log in.
2. Click **"Image Generation"** in the left sidebar.
3. Select a pixel art model — look for **"Pixel Art"** or **"RPG Asset"** in the model selector. If unavailable, use the **"Anime Pastel Dream"** model as fallback.
4. Prompt formula:
   ```
   pixel art texture for [subject], [color palette description], flat colors,
   no shading, simple, low resolution, game asset, top-down or isometric,
   dream-pop palette, bright, saturated
   ```
5. Set output resolution to the smallest available (512×512 is fine — you will downsample it).
6. Generate 4–8 variants. Download all passing ones.
7. **For tiling textures** (ground, paths): enable the **"Tiling Texture"** option in Leonardo before generating.

**Stable Diffusion (local, if preferred):**
- Use a pixel art LoRA (e.g., `pixel_art_v2.safetensors`) with any base model.
- Same prompt formula as above.
- Output at 512×512 minimum, then downsample in the next step.

### 4.3 — Downsample to target resolution

The generated image (512×512+) must be downsampled to the target resolution (32×32, 64×64) with hard pixel edges preserved. Standard image resize will blur it — use nearest-neighbor.

**Using any image editor (Photoshop, GIMP, Aseprite):**
1. Open the generated texture.
2. Image → Image Size (or equivalent).
3. Set width/height to your target (e.g., 64×64).
4. **Interpolation / Resample method: set to `Nearest Neighbor` (or `None`).** This is critical. Bilinear/bicubic will blur the pixels.
5. Save as PNG (lossless).

**Output file:** `[asset-name]_tex_v1.png` in your raw folder.

### 4.4 — UV review (optional but recommended for characters)

If the texture doesn't map correctly on the mesh, a quick UV check in Blender:
1. File → Import → FBX → load your mesh.
2. In the top menu bar of Blender, click **"UV Editing"** (one of the workspace tabs).
3. The left panel (UV Editor) shows how the mesh unwraps. The right panel (3D Viewport) shows the mesh.
4. In the 3D Viewport, press **A** to select all faces.
5. The UV map should appear in the left panel. Look for obvious overlaps or stretched areas.
6. If the mesh came from Meshy, the UV is usually usable — only fix it if the texture clearly misaligns on visible surfaces.

---

## Phase 5: Rigging

Skip this phase for static props and environment pieces.

### 5.1 — Characters (Humanoid) → Mixamo

Mixamo is the primary rigging tool for all humanoid characters (player, NPCs).

1. Go to [mixamo.com](https://www.mixamo.com) and log in (Adobe account required — free).
2. Click **"Upload Character"** in the top-right area of the screen.
3. Upload your `[asset-name]_clean.fbx` file.
4. Mixamo's auto-rigger will open. You will see a front-facing T-pose view of your character.
5. Place the rig markers:
   - The screen shows 5 marker dots. Drag each to the correct body part:
     - **Chin:** bottom of the jaw
     - **Left/Right Wrists:** inside of the wrist
     - **Left/Right Elbows:** inside elbow joint
     - **Left/Right Knees:** front of the knee cap
     - **Groin:** top of the inner thigh
   - For chibi characters, markers may need slight adjustment — the head is larger, limbs shorter.
6. Check **"Use Symmetry"** if the character is symmetrical (usually yes).
7. Click **"Next"** then **"Rig"**. Wait 10–30 seconds.
8. Preview the rig by selecting any animation from the left panel (e.g., "Walking"). Evaluate:
   - Do the joints bend naturally at elbows/knees?
   - Does the head move correctly?
   - Are there skin weight issues (mesh tearing or twisting)?
9. If the rig looks wrong: click **"Back"**, readjust markers, re-rig. Try up to 3 times.
10. Once satisfied, download the rigged character:
    - Format: **FBX for Unity**
    - Check **"With Skin"**
    - Leave skeleton at default (30 fps)
11. Save as `[asset-name]_rigged.fbx`.

### 5.2 — Animals (Quadrupeds) → Asset Store Pack or AccuRIG

See `docs/animal-animation-options.md` for the full per-animal decision matrix.

**Fast path (Asset Store pack with existing rig):**
- Search Unity Asset Store for a low-poly version of the specific animal.
- If the art style is close enough and style will be applied via shader anyway, use the pack rig directly.
- This saves the entire rigging step.

**AccuRIG path (if no suitable pack):**
1. Go to [actorcore.reallusion.com](https://actorcore.reallusion.com).
2. Use AccuRIG to auto-rig your Meshy-generated animal mesh.
3. AccuRIG supports quadruped presets — select the closest animal template (e.g., "Quadruped", "Horse", "Feline").
4. Export as FBX.
5. Save as `[asset-name]_rigged.fbx`.

---

## Phase 6: Animation

Skip this phase for static props and environment pieces.

### 6.1 — Characters: Mixamo animation library

For standard locomotion and utility animations, pull directly from Mixamo's library. These are applied to your already-rigged character.

1. In Mixamo, your rigged character should still be active. If not, upload `[asset-name]_rigged.fbx`.
2. Browse the **left panel** — this is the animation library (hundreds of animations organized by category).
3. For the player character, you need at minimum:
   - **Idle** — search "idle", pick a relaxed standing idle
   - **Walk** — search "walking", pick a standard walk cycle (in-place preferred)
   - **Run** — search "running", pick a standard run cycle (in-place preferred)
   - **Tool use placeholder** — search "digging" or "swinging axe"
4. For each animation:
   - Adjust **"Overdrive"** slider to tweak speed (snappier = higher)
   - Check **"In Place"** checkbox to remove root motion (Unity will handle character movement)
   - Download as **FBX for Unity**
   - Name: `[asset-name]_anim_[animation-name].fbx` (e.g., `player_anim_walk.fbx`)

### 6.2 — Personality / custom animations: Cascadeur

For any animation that expresses personality — reactions, emotional idles, event-specific animations (animal flop, happy bounce, sulk) — use Cascadeur.

1. Open Cascadeur (desktop application).
2. File → Import → FBX → load your `[asset-name]_rigged.fbx`.
3. The character appears in the viewport. The timeline at the bottom is empty.
4. Cascadeur's core loop:
   - Click a frame number in the timeline to set your position.
   - In the viewport, drag rig handles to pose the character.
   - Cascadeur's **"AutoPhysics"** (button in the top toolbar) will fill in secondary motion between your key poses.
5. Set keyframes at the major pose moments. Let AutoPhysics handle inbetweens.
6. Preview with the Play button. Adjust key poses until the animation reads with the correct snappy, expressive feel.
7. Export: File → Export → FBX → save as `[asset-name]_anim_[name].fbx`.

**Minimum custom animations per animal species:**
- Personality idle (lazy sprawl, curious sniff, energetic bounce — per trait)
- Happiness reaction (triggered by petting)
- Sad/neglected state idle
- Product collection reaction

### 6.3 — Animals: locomotion library

For walk/trot/run cycles, prefer Asset Store packs or AccuRIG's motion library over custom Cascadeur work. These are background animations — not character moments.

---

## Phase 7: Unity Import

> **Unity UI orientation:** All steps below assume the default Unity Editor layout. The **Project window** is the file browser at the bottom of the screen. The **Hierarchy** is the scene object list on the left. The **Inspector** is the properties panel on the right. The **Scene View** is the main 3D viewport in the center.

### 7.1 — Organize files before importing

Before dragging anything into Unity, confirm your local file set:

```
raw/[asset-name]/
  [asset-name]_clean.fbx          ← static mesh (no rig)
  [asset-name]_rigged.fbx         ← mesh with rig (if animated)
  [asset-name]_anim_idle.fbx      ← animations (one file each)
  [asset-name]_anim_walk.fbx
  ...
  [asset-name]_tex_v1.png         ← pixel art texture
```

### 7.2 — Import FBX files into Unity

1. In Unity, look at the **Project window** (bottom of the screen). Navigate to the correct folder:
   - Characters: `Assets/_Project/Characters/[CharacterName]/`
   - Animals: `Assets/_Project/Animals/[AnimalName]/`
   - Props: `Assets/_Project/Props/[PropName]/`
   - Environment: `Assets/_Project/Environment/[Category]/`
   - If the folder doesn't exist, right-click in the Project window → **Create → Folder**.
2. Open your operating system's File Explorer and navigate to your `raw/[asset-name]/` folder.
3. Drag and drop the FBX files into the correct Unity folder in the Project window.
4. Unity will import them and show a progress bar. Wait for it to complete.
5. Click on the imported FBX file in the Project window. The **Inspector** on the right will show its import settings.

### 7.3 — Configure FBX import settings (mesh)

With the FBX selected in the Project window, the Inspector shows tabs at the top: **Model | Rig | Animation | Materials**.

**Model tab:**
1. Click the **"Model"** tab.
2. Set **"Scale Factor"** to `1` (Meshy exports at correct scale for Unity).
3. Check **"Read/Write Enabled"** — OFF (leave it off unless you need runtime mesh modification).
4. **Mesh Compression:** None.
5. Check **"Generate Colliders"**: OFF for characters/animals (you will add a capsule collider manually). ON for environment props.

**Rig tab (animated assets only):**
1. Click the **"Rig"** tab.
2. **Animation Type:**
   - Characters (humanoid): set to `Humanoid`
   - Animals / non-humanoid: set to `Generic`
3. For Humanoid: click **"Configure..."**. Unity will map the Mixamo bones to its Humanoid avatar. Verify the bone mapping looks correct (green checkmarks). Click **"Done"**.
4. Click **"Apply"** at the bottom of the Inspector.

**Animation tab (if this FBX contains animations):**
1. Click the **"Animation"** tab.
2. Confirm **"Import Animation"** is checked.
3. For each animation clip listed in the **"Clips"** section:
   - Click the clip to select it.
   - Set the correct **Start** and **End** frame numbers (from Mixamo/Cascadeur export).
   - For looping animations (idle, walk, run): check **"Loop Time"** and **"Loop Pose"**.
   - For one-shot animations (reactions, events): leave Loop Time unchecked.
4. Click **"Apply"**.

### 7.4 — Import texture

1. Drag and drop `[asset-name]_tex_v1.png` into the same Unity asset folder as the FBX.
2. Click the texture in the Project window. The Inspector shows texture import settings.
3. Configure these settings exactly:
   - **Texture Type:** Default
   - **sRGB (Color Texture):** checked (ON)
   - **Alpha Source:** From Input (if the PNG has alpha) or None
   - **Filter Mode:** `Point (no filter)` ← **critical** — this preserves hard pixel edges
   - **Mip Maps:** unchecked (OFF) ← **critical** — mipmaps blur the pixel art
   - **Compression:** None ← compression adds artifacts that fight the pixel look
   - **Max Size:** match your target (64 for a 64×64 texture)
4. Click **"Apply"**.

### 7.5 — Create the material

1. In the Project window, right-click in the asset folder → **Create → Material**.
2. Name it `[AssetName]_Mat`.
3. Click the new material to select it. The Inspector shows its settings.
4. In the **"Shader"** dropdown at the top of the Inspector, select your PS1 shader pack's shader. It will be listed under the pack's name (e.g., `PSXRetro/Standard` or similar — exact name depends on which pack you purchased).
   - If the shader pack is not yet purchased/imported: temporarily use `Universal Render Pipeline/Lit` and revisit.
5. Find the **"Base Map"** (or "Albedo" / "Main Texture") slot in the material settings. Click the small circle next to it and select your texture from the asset picker, or drag the texture from the Project window into that slot.
6. Configure PS1 shader-specific settings:
   - **Vertex Wobble Intensity:** start at `0.5`–`1.0`, adjust per asset (buildings can be lower, characters higher)
   - **Affine Mapping:** ON
   - **Color Depth Reduction:** ON
   - Exact setting names vary by shader pack — match these concepts to what your pack exposes.

### 7.6 — Assign the material to the mesh

1. In the Project window, expand the FBX file (click the triangle/arrow next to it). You will see sub-assets including mesh(es) and the default material.
2. Find the mesh prefab or drag the FBX into the **Hierarchy** (the left panel). It will appear in the scene.
3. Click the object in the Hierarchy to select it. In the Inspector, scroll down to find the **"Mesh Renderer"** component.
4. Expand the **"Materials"** section inside Mesh Renderer.
5. For each material slot, click the circle next to it and select your `[AssetName]_Mat`.
6. Confirm the mesh in the Scene View now shows your pixel art texture with the PS1 shader effect.

---

## Phase 8: Animator Controller Setup (Animated Assets Only)

> The Animator Controller is Unity's state machine for blending animations — think of it as a finite state machine where each state is an animation clip, and transitions between states are triggered by code. It's the wiring layer between your game logic and the imported animation clips.

### 8.1 — Create the Animator Controller

1. In the Project window, right-click in the asset folder → **Create → Animator Controller**.
2. Name it `[AssetName]_Animator`.
3. Double-click the Animator Controller to open the **Animator window** (a graph editor showing animation states).

### 8.2 — Add animation states

1. In the **Animator window**, right-click in the empty graph area → **Create State → Empty**.
2. Name the state (e.g., "Idle"). In the Inspector on the right, find the **"Motion"** field and drag your `idle` animation clip into it.
3. Repeat for each animation: Walk, Run, and any others.
4. The first state added becomes the **default state** (shown in orange). This should be "Idle". If not, right-click "Idle" → **Set as Layer Default State**.

### 8.3 — Add transitions

1. Right-click the **"Idle"** state → **Make Transition**. An arrow will follow your cursor.
2. Click **"Walk"** to create the transition arrow Idle → Walk.
3. Click the transition arrow to select it. In the Inspector:
   - **Has Exit Time:** OFF (transitions should be code-triggered, not time-based)
   - **Transition Duration:** `0.1`–`0.2` (short blend for snappy PS1 feel)
4. Create reverse transitions (Walk → Idle, Walk → Run, Run → Walk, etc.).

### 8.4 — Add parameters

Parameters are the variables your C# code will set to drive transitions.

1. In the **Animator window**, click the **"Parameters"** tab in the top-left.
2. Click the **+** button to add parameters:
   - `Speed` — Float (set from movement speed in code)
   - `IsGrounded` — Bool (for jump states if needed)
   - Additional booleans for state-specific triggers
3. Click each transition arrow and add a **Condition** in the Inspector that uses these parameters (e.g., "Speed greater than 0.1" → transition to Walk).

### 8.5 — Assign controller to the character

1. Select the character GameObject in the Hierarchy.
2. In the Inspector, find the **"Animator"** component.
3. Drag your `[AssetName]_Animator` from the Project window into the **"Controller"** field.

---

## Phase 9: Create a Prefab

> A Prefab is a reusable template of a GameObject — equivalent to a React component. Every time you need this asset in a scene, you drag out the Prefab rather than reassembling it from scratch.

1. In the Hierarchy, verify the character/prop GameObject is configured correctly (material, Animator, collider all set).
2. Drag the GameObject from the **Hierarchy** into the **Project window** (into your asset folder).
3. Unity creates a Prefab file (blue cube icon). Name it `[AssetName]_Prefab`.
4. The original object in the Hierarchy is now linked to the Prefab (shown with a blue name).
5. Right-click the Hierarchy object → **Prefab → Unpack Completely** to remove the scene copy, keeping only the Prefab in the Project.

---

## Phase 10: Style Compliance Check

Before marking an asset done, verify every item in this checklist:

- [ ] Texture uses **Point (no filter)** filtering in the import settings
- [ ] Texture has **mipmaps disabled**
- [ ] Texture resolution is at or below 128×128 (ideally 32 or 64)
- [ ] PS1 shader is applied (not URP/Lit default shader)
- [ ] Vertex wobble is visible in Play mode
- [ ] Colors match the dream-pop palette (warm, saturated, not muted or grey)
- [ ] Silhouette reads clearly at thumbnail scale
- [ ] Poly count is within target range (check in Blender if needed)
- [ ] Asset looks correct under the scene's URP Global Volume (bloom, color grade)
- [ ] If animated: animations loop cleanly with no pop

To preview the full aesthetic in Unity:
1. Enter **Play mode** (click the Play button at the top center of Unity — the three buttons that look like a media player).
2. The Scene View shows the game running with all post-processing active.
3. Exit Play mode (click Play again) before making any changes — changes made in Play mode are lost.

---

## Phase 11: Documentation & Filing

1. Move all source files to your permanent raw storage (outside the Unity project, backed up).
2. Log the asset in `PLAN.md` — mark the relevant task `[x]` complete.
3. If this asset introduces a new system or tool decision, update or create the relevant doc in `docs/`.
4. Note any style issues or mesh quality problems for this asset's generation approach — use this to improve prompts for the next similar asset.

---

## Quick Reference: Decision Trees

### Which 3D generator?
```
Is it organic (character, animal, plant)?  → Meshy.ai
Is it hard-surface / mechanical?            → Tripo3D (fallback: Meshy)
```

### Which texture tool?
```
Need tiling? (terrain, floors)             → Leonardo.ai with Tiling Texture ON
Need pixel art / custom style?             → Stable Diffusion (local) with pixel LoRA
Quick iteration?                            → Leonardo.ai pixel art model
```

### Which rigger?
```
Humanoid character?                         → Mixamo
Farm animal (common species)?               → Asset Store pack rig (preferred) → AccuRIG
Unique creature?                            → AccuRIG → Blender (last resort)
```

### Which animator?
```
Standard locomotion (walk, run, idle)?      → Mixamo library
Personality / emotional / event animation? → Cascadeur
Animal locomotion?                          → Asset Store pack → AccuRIG library
Terrain-adaptive foot placement?            → Unity Animation Rigging (procedural layer)
```

---

## Asset Folder Naming Convention

```
Assets/_Project/
  Characters/
    Player/
    NPC_[Name]/
  Animals/
    [AnimalName]/
  Props/
    Tools/
    Items/
    Decorations/
  Environment/
    Farm/
    Town/
    Nature/
    Cave/
  UI/
    Icons/
    Portraits/
```

All materials: `[AssetName]_Mat`  
All prefabs: `[AssetName]_Prefab`  
All animator controllers: `[AssetName]_Animator`  
All textures: `[AssetName]_tex_[variant].png`

---

*Last updated: 2026-04-09 — evaluate and iterate this workflow with each new asset batch*
