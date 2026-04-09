# Visual Style & Shader Implementation

Reference for achieving ChibiValley's visual aesthetic in Unity URP.

---

## Aesthetic Direction

**Low-poly toon pixel art.** Clean cel-shaded geometry with a pixel art filter on top. Modern, pretty, and charming — not retro simulation. Think the graphic clarity of a cartoon combined with the handcrafted feel of pixel art.

Key qualities:
- Flat, bold cel-shaded surfaces (Flat Kit)
- Pixel-perfect outlines on characters and objects (ProPixelizer)
- Macropixel rendering that makes 3D look like living pixel art (ProPixelizer)
- Stylized water (Flat Kit)
- 3D god rays / Light Plane (Flat Kit)
- Bright, saturated, dream-pop colour palette
- Low-res Point-filtered textures for the handcrafted texture feel

What we are **not** doing:
- PS1 vertex wobble
- Affine texture warping
- Color depth dithering / retro artifacts
- CRT effects

---

## Shader Stack

| Layer | Tool | Covers |
|-------|------|--------|
| Surface shading | **Flat Kit** | All geometry — characters, environment, props |
| Pixel art rendering | **ProPixelizer** ✅ installed | Per-object pixelization, outlines, color palettes |
| Water | **Flat Kit** | Ponds, rivers, ocean, rain puddles |
| God rays | **Flat Kit Light Plane** | Outdoor forest and farm scenes |
| Atmosphere fog | **Flat Kit Gradient Fog** | Forest, cave, early morning scenes |
| Post-processing | **URP Volume** | Bloom, color grading, vignette |

---

## Flat Kit

**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/flat-kit-toon-shading-and-water-143368)  
**Docs:** [flatkit.dustyroom.com](https://flatkit.dustyroom.com)  
**Status:** Purchase pending (Phase 0.8)

### Surface Shader Setup

Flat Kit's **Stylized Surface** shader is the default material for all game objects.

Key settings to configure globally:
- **Cel shading bands:** 2–3 bands for a clean cartoon feel (not painterly gradient)
- **Step smoothness:** Keep low (0.02–0.05) for crisp band edges, not soft gradients
- **Self-shadowing:** Enabled — adds depth without complex lighting setup
- **Specular highlight:** Subtle, small radius — gives surfaces a slight sheen without being glossy

Per-asset tuning:
- Characters: 2 bands, warm shadow colour (not grey — shift toward amber/orange)
- Foliage: 2 bands, cooler shadow colour (slightly blue-green)
- Buildings/props: 3 bands for more surface complexity
- Terrain: Use Flat Kit Terrain Shader — handles Unity terrain splat maps

### Water Shader Setup

Flat Kit's water shader is the only water solution in the project.

Use cases: farm pond, river, beach/ocean, rain puddles on ground

Key parameters to tune:
- **Shallow colour / Deep colour:** Warm turquoise shallow, deep teal — cozy, not realistic
- **Foam:** Enabled on edges — gives a clean cartoon shoreline
- **Vertex waves:** Subtle amplitude — gentle, not dramatic
- **Refraction:** Subtle — keeps the toon look, avoid high values
- **Transparency:** Semi-transparent in shallow areas

### Light Plane (God Rays)

Flat Kit's **Light Plane** shader renders god rays as 3D geometry — more controllable than screen-space.

Setup in outdoor scenes:
1. Create a plane mesh angled from the sun direction
2. Apply the Light Plane material
3. Set colour to warm yellow-white (matches directional light)
4. Set fade distance so rays fade naturally at edges
5. Place in forest gaps, barn windows, cave entrances

### Gradient Fog

Apply via a **Flat Kit Fog Image Effect** component on the camera (or as a Renderer Feature):
- **Fog colour:** Slightly warm white (not grey)
- **Distance fog:** Enabled for outdoor zones — softens horizon
- **Height fog:** Useful in cave zones — fog pool near ground

---

## ProPixelizer ✅ Already Installed

**Location in project:** `Assets/ProPixelizer/`  
**Docs:** [propixelizer.github.io](https://propixelizer.github.io/docs/)

ProPixelizer runs as a URP Renderer Feature that pixelates objects on a per-object basis. Objects not using a ProPixelizer material render normally — allowing mixed scenes.

### Renderer Feature Setup

ProPixelizer needs to be added to your URP Renderer:

1. In the **Project window**, navigate to `Assets/Settings/`
2. Find your URP Renderer asset (named something like `UniversalRenderer` or `PC_Renderer`)
   > This is not the same as the URP Pipeline Asset — it's the Renderer, one level deeper. The Pipeline Asset references the Renderer.
3. Click the Renderer asset to select it
4. In the **Inspector**, find the **Renderer Features** list at the bottom
5. Click **Add Renderer Feature** → select **ProPixelizer Render Pass**
6. A ProPixelizer settings block appears — configure:
   - **Pixel Size:** Start at `2` — means each "macropixel" is 2×2 screen pixels. Increase for a blockier look.
   - **Outline colour:** Dark (near-black) with slight warm tint — not pure black

### Per-Object Setup

ProPixelizer materials are assigned per-object, not globally. This lets you apply pixelization selectively:

**Apply ProPixelizer to:**
- Player character ✅ (chibi characters read best with pixel outlines)
- NPCs ✅
- Animals ✅
- Key props (tools, items, crops when mature) ✅
- UI elements (optional — can work well for inventory icons)

**Leave as standard Flat Kit on:**
- Large terrain
- Buildings (test both — buildings may look better without pixelization)
- Sky/clouds

### Material Setup Per Object

1. Select a character or prop mesh in the **Hierarchy** or **Project** window
2. In the **Inspector**, find the **Mesh Renderer** component
3. Click the material slot → create a new material
4. In the **Shader** dropdown on the material: select `ProPixelizer/ProPixelizer` (or the Shader Graph variant)
5. Set **Pixel Scale** to match your global pixel size setting (2–4)
6. Set **Outline Width:** 1–2 pixels
7. Assign your low-res Point-filtered texture to the Albedo slot

### Shader Graph Integration

ProPixelizer exposes a **SubTarget for Shader Graph** — you can build custom shaders in Shader Graph that are pixelated. This is how you'd create:
- Character with animated face texture swaps (eye expressions)
- Special crop growth shaders
- Item glow effects that respect pixel boundaries

---

## URP Post-Processing Stack

Applied via a **Global Volume** in each scene. Set up one master Volume in the Bootstrap scene that applies everywhere, then add per-scene Volume overrides for zone-specific adjustments.

| Effect | Setting | Purpose |
|--------|---------|---------|
| **Bloom** | Intensity 0.3–0.5, Threshold 0.9 | Soft glow on bright surfaces, lanterns, magic |
| **Color Adjustments** | Saturation +25, Contrast +15 | Dream-pop brightness and pop |
| **Color Filter** | Slight warm cream tint | Cozy, non-clinical lighting feel |
| **Tonemapping** | ACES | Prevents blown-out highlights |
| **Vignette** | Intensity 0.15–0.2 | Subtle frame focus |
| **Depth of Field** | Very subtle, far focus only | Optional — test if it helps or hurts the toon look |

Per-zone Volume overrides:
- **Cave zones:** Reduce Saturation, add a slight cool blue Color Filter, increase Vignette
- **Festival scenes:** Increase Bloom intensity, warmer Color Filter
- **Winter:** Slightly desaturate, cooler tones

---

## Texture Guidelines

Low-res textures are essential — they're what gives the handcrafted pixel art feel underneath the toon shading.

- **Resolution:** 32×32 or 64×64 for most assets, 128×128 max for hero character faces
- **Filter mode:** **Point (no filter)** — set this on every texture import. This is the single most important setting.
- **Mipmaps:** Disabled on all pixel art textures
- **Compression:** None or lossless — lossy compression blurs pixel edges
- **Format:** PNG for lossless import into Unity

When importing a texture in Unity:
1. Select the texture file in the **Project window**
2. In the **Inspector**: set **Filter Mode** to **Point (no filter)**
3. Expand **Advanced**: set **Generate Mip Maps** to off
4. Set **Compression** to **None**
5. Click **Apply**

Texture creation tools:
- **Aseprite** — dedicated pixel art editor, best tool for hand-painted textures
- **Leonardo.ai** — AI generation with pixel art model for quick texture generation
- **Stable Diffusion** with pixel art LoRA — for batch texture generation

---

## Color Palette Reference

Target: "dream-pop" / "cottagecore" — bright, saturated, high contrast, warm

| Element | Colour Direction |
|---------|-----------------|
| Grass / foliage | Warm sage, lime green — not cool or grey |
| Soil / earth | Warm terracotta, rich brown |
| Sky (day) | Powder blue fading to warm white at horizon |
| Sky (sunset) | Golden orange to dusty rose |
| Sky (night) | Deep navy with warm purple undertone |
| Water | Warm turquoise shallow, teal deep |
| Buildings | Warm cream, terracotta, muted sage |
| UI backgrounds | Warm parchment, light wood |

Global URP Color Adjustments:
- Saturation: +20 to +30
- Contrast: +10 to +20
- Color Filter: `#FFF5E0` (warm cream) at low intensity (~0.15)

---

## Reference Visual Comparisons

Games with a similar aesthetic to aim for:
- **Lil Gator Game** — low-poly toon with clean outlines
- **Alba: A Wildlife Adventure** — bright, saturated, low-poly nature
- **A Short Hike** — pixel art meets 3D low-poly
- **Chicory: A Colorful Tale** — bold outlines, flat colour fills
- **Mineko's Night Market** — chibi characters, warm palette, cozy

The combination of Flat Kit toon shading + ProPixelizer outlines should land somewhere between A Short Hike and Lil Gator Game — which is exactly the sweet spot for ChibiValley.

---

*Last updated: 2026-04-09*
