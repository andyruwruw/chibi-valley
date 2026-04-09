# ChibiValley — Next Steps

**Unity version:** 6000.3.13f1  
**Current phase:** 0.8 — Visual Foundation

---

## Immediate Next Actions

### CURRENT TASK: Add Flat Kit to the URP Renderer and verify it's working

Complete these steps in order. Each step tells you which window to look at and exactly what to click.

---

#### Step 1: Import the actual Flat Kit URP package

> **Why this is needed:** Flat Kit ships as a two-stage install. The Asset Store import only delivers a launcher with two `.unitypackage` files inside it — one for Built-In pipeline, one for URP. You must manually import the URP one. Until you do this, no Flat Kit shaders or renderer features exist in your project.

**Window: Project**  
*(Bottom-left panel. Shows your file system under Assets/.)*

1. In the **Project** window, navigate to **Assets → FlatKit**
2. You'll see two files ending in `.unitypackage`:
   - `[Render Pipeline] Built-In.unitypackage` — skip this one
   - `[Render Pipeline] Universal (URP).unitypackage` — double-click this one
3. A new window called **Import Unity Package** opens

**Window: Import Unity Package** *(popup)*

4. Everything in the list should be checked — leave it that way
5. Click **Import** (bottom-right of the popup)
6. Unity compiles for 30–60 seconds

**Window: Console** *(bottom panel — click the Console tab if not visible)*

7. Check for red errors after compilation — if none, import succeeded
8. Back in the **Project** window, **Assets → FlatKit** should now have many subfolders: `Rendering`, `Shaders`, `Demo Scenes`, etc.

---

#### Step 2: Find your URP Renderer asset

**Window: Project**

You need to find two things — the URP Pipeline Asset and the URP Renderer Asset. They are different files.

1. In the **Project** window, navigate to **Assets → Settings** (click the arrow next to Assets, then click Settings)
2. You'll see several `.asset` files. Look for files with these icons:
   - A file named something like **PC_RPAsset** or **UniversalRenderPipelineAsset** — this is the **Pipeline Asset** (controls overall quality settings). You don't need this one right now.
   - A file named something like **PC_Renderer** or **UniversalRenderer** — this is the **Renderer Asset** (controls what render passes run). This is the one you need.
3. If you can't tell which is which: click each file. Look at the **Inspector** panel on the right side of the screen.
   - The **Renderer Asset** has a section at the bottom called **Renderer Features**
   - The **Pipeline Asset** does NOT have that section — it has things like Shadow Distance, Quality levels, etc.
4. Click the **Renderer Asset** to select it

---

#### Step 3: Add Flat Kit Outline as a Renderer Feature

**Window: Inspector**  
*(Right panel. Shows settings for whatever is currently selected.)*

With the Renderer Asset selected from Step 2:

1. Scroll down to the bottom of the **Inspector** panel
2. You'll see a section called **Renderer Features** with a list (possibly empty, or containing the "Pixelisation Feature" entry that errored earlier)
3. Click the **Add Renderer Feature** button — it's a button at the bottom of the Renderer Features list
4. A small dropdown menu appears with available features
5. Look for an entry containing **"Flat Kit"** or **"Outline"** — it may be listed as:
   - **Flat Kit Outline**
   - **FlatKit > Outline**
   - **Stylized Surface Outline**
6. Click it — a new entry appears in the Renderer Features list
7. The new entry has settings — leave them at defaults for now

> **Note:** If you don't see any Flat Kit entries in the dropdown, Flat Kit may not have registered its renderer features yet. Try closing and reopening Unity, then repeat from Step 3.

---

#### Step 4: Create your first Flat Kit material

**Window: Project**

1. In the **Project** window, navigate to **Assets → _Project → Art → Materials**
2. Right-click inside that folder → **Create → Material**
3. A new material appears with the name highlighted — type `M_FlatKitDefault` and press Enter
4. Click the new material to select it

**Window: Inspector**

5. At the top of the **Inspector**, you'll see a field called **Shader** with a dropdown — it currently says something like **Universal Render Pipeline/Lit**
6. Click the **Shader** dropdown
7. In the menu that appears, navigate to: **Flat Kit → Stylized Surface**
   - If you see a search box, type `stylized` to find it faster
8. The Inspector updates to show Flat Kit's settings. Key settings to configure:
   - **Color:** Click the colour swatch → set to a mid-tone colour like a warm grey `#B8A898` (just for testing)
   - **Cel Shading Mode:** Set to **Single [1 Step]** — gives a clean two-tone toon look
   - **Step 1 → Shading Color:** Click the swatch → set to a darker, slightly warm tone like `#7A6A5A`
   - Leave everything else at default for now

---

#### Step 5: Test the material on an object

**Window: Hierarchy**  
*(Top-left panel. Lists every object in the current open scene.)*

1. First, make sure you're in the **ShaderTest** scene. Look at the tab bar at the top of the Scene View — it should say **ShaderTest**
   - If it says **Farm** or **SampleScene**: go to **File → Open Scene** → navigate to `Assets/_Project/Scenes/_Test/ShaderTest` and double-click it
2. In the **Hierarchy** panel, right-click on empty space → **3D Object → Cube**
3. A cube appears in the **Scene View** (the 3D viewport in the centre of the screen) and in the Hierarchy list

**Window: Inspector**

4. With the Cube selected in the Hierarchy, look at the **Inspector**
5. Find the **Mesh Renderer** component — scroll down until you see it
6. Inside Mesh Renderer, find **Materials** → **Element 0** — it shows the current material
7. Click the small **circle icon** to the right of Element 0 — a picker window opens
8. In the picker, type `M_FlatKitDefault` → double-click it to assign

**Window: Scene View / Game View**

9. Look at the cube in the **Scene View** — it should now show flat toon shading (clean colour bands, no smoothly blended gradients)
10. Press the **▶ Play button** at the top of the screen to enter Play mode and check the **Game View** tab — same look

> **What you should see:** The cube has a flat lit face and a darker shadowed face. Clean, no gradients. If it looks like the default Unity grey — the material didn't assign correctly. If it's pink — the shader is missing or broken.

Press **▶ again** to exit Play mode when done.

---

#### Step 6: Set Flat Kit as the default material for new objects

**Window: Project Settings**  
*(Opened via Edit → Project Settings in the menu bar at the top of the screen.)*

1. In the menu bar: **Edit → Project Settings**
2. The **Project Settings** window opens. In the search box at the top of this window, type: `graphics`
3. Click **Graphics** in the left list
4. Look for a field called **Default Material** (it may be under a subsection — scroll down if needed)
5. Click the **circle icon** to the right of the Default Material field
6. In the picker that opens, type `M_FlatKitDefault` → double-click to assign
7. Close **Project Settings** (click the X on the Project Settings window — NOT the X on the whole Unity editor)

> From now on, any new 3D object you create will automatically use the Flat Kit toon shader instead of Unity's default pink/grey material.

---

#### Step 7: Configure the Global Volume (post-processing)

**Window: Hierarchy**

1. Right-click on empty space in the **Hierarchy** → **Create Empty**
2. A new object called `GameObject` appears — click its name in the Hierarchy, press **F2**, rename it to `GlobalVolume`

**Window: Inspector**

3. With `GlobalVolume` selected, click **Add Component** at the bottom of the Inspector
4. In the search box that appears, type `volume` → click **Volume**
5. The Volume component appears. Set:
   - **Mode:** Global
   - **Profile:** click **New** — a Save dialog appears. Navigate to `Assets/_Project/Settings/`, name it `GlobalVolumeProfile`, click Save
6. The profile is now created and assigned. Click **Add Override** inside the Volume component

**Add Bloom:**
7. Click **Add Override** → type `bloom` → click **Bloom**
8. In the Bloom section that appears, tick the checkbox next to **Intensity** → set value to `0.4`
9. Tick **Threshold** → set to `0.9`

**Add Color Adjustments:**
10. Click **Add Override** → type `color adj` → click **Color Adjustments**
11. Tick **Saturation** → set to `25`
12. Tick **Contrast** → set to `15`
13. Tick **Color Filter** → click the white colour swatch → in the colour picker, paste hex: `FFF5E0` → click OK

**Add Tonemapping:**
14. Click **Add Override** → type `tone` → click **Tonemapping**
15. Tick **Mode** → set dropdown to **ACES**

**Add Vignette:**
16. Click **Add Override** → type `vignette` → click **Vignette**
17. Tick **Intensity** → set to `0.18`

---

#### Step 8: Save the scene and commit

**Window: Scene View (top menu bar)**

1. Press **Ctrl + S** to save the ShaderTest scene
2. In your terminal / command prompt:

```bash
cd C:\Users\Andrew\Documents\Github\game\ChibiValley
git add Assets/_Project/
git commit -m "Add Flat Kit renderer feature, default material, Global Volume post-processing"
git push
```

---

## Up Next (after this task)

- Import Synty packs — subscribe at syntystore.com, download Farm, Nature, Town, Adventure, Icons, Particle FX, import into `Assets/ThirdParty/Synty/`
- Integrate ProPixelizer RenderGraph beta — once received from developer, replace current install, verify no Console errors
- Begin Phase 1: Core Engine Systems — Game Data Loader first

---

## Known Issues

### ProPixelizer: RenderGraph incompatibility with Unity 6.3 (6000.3.13f1)

**Status:** Waiting for developer beta

**Error:**
```
The render pass ProPixelizer.PixelizationPass does not have an implementation of the RecordRenderGraph method.
```

**Root cause:** ProPixelizer v1.8.2 uses the old render pass API. Unity 6000.3.x requires `RecordRenderGraph()`. Compatibility Mode is deprecated and hidden in this version — not usable.

**Developer status:** RenderGraph beta available on request via Discord/email.

**Fix:**
1. Go to https://sites.google.com/view/propixelizer/home — find Discord link
2. Request the RenderGraph beta build for Unity 6
3. When received: in Unity, delete the existing `Assets/ProPixelizer/` folder
4. Import the new package — **Assets → Import Package → Custom Package** → select the received file
5. Check the Console — if no RenderGraph error, done

**Fallback while waiting:** Flat Kit Outline renderer feature (added in Step 3 above) covers the outline need. Low-res Point-filtered textures cover the pixel art texture feel.

---

## Established Workflows

### How to add a Renderer Feature to URP (Unity 6000.3.x)
1. **Project window → Assets → Settings** → select the Renderer asset (the one with a "Renderer Features" section in the Inspector, NOT the Pipeline Asset)
2. **Inspector → scroll to bottom → Add Renderer Feature button** → select feature from dropdown
3. Configure the new feature's settings in the Inspector

### How to create a material with a specific shader
1. **Project window → right-click target folder → Create → Material**
2. Name the material (convention: prefix with `M_`)
3. **Inspector → Shader dropdown** → navigate to the desired shader
4. Configure material properties in Inspector

### Texture import settings (apply every time a texture is imported)
1. Select texture in **Project window**
2. **Inspector → Filter Mode → Point (no filter)**
3. **Inspector → Advanced → Generate Mip Maps → off**
4. **Inspector → Compression → None**
5. Click **Apply**

---

*Last updated: 2026-04-09*
