# AI Tools Reference

A living document of AI tools available for asset creation and workflow automation. Updated as we discover new tools or pricing changes.

---

## 3D Model Generation

### Meshy.ai
**Best for:** Characters, animals, props, environment objects — primary generation tool  
**Strengths:**
- Text-to-3D and image-to-3D
- Exports FBX, OBJ, GLB (Unity-ready formats)
- Built-in auto-rigging feature for characters
- Low-poly output is achievable with prompting
- Fastest iteration cycle of current tools
- Texture baking included

**Weaknesses:**
- Topology can be messy, may need Blender cleanup
- Rigging quality inconsistent, complex animations may need Mixamo
- Limited control over exact poly count
- Stylized/low-poly results require good prompting discipline

**Pricing:** Free tier (limited credits/month), Pro ~$20/mo, Business ~$80/mo  
**URL:** https://www.meshy.ai

---

### Tripo3D
**Best for:** Props, hard-surface objects, environment assets  
**Strengths:**
- Strong mesh topology quality (cleaner than Meshy for props)
- Fast generation
- Good at mechanical/structured objects
- FBX/OBJ/GLB export

**Weaknesses:**
- Less consistent on organic shapes (animals, characters)
- Fewer post-generation editing options than Meshy
- Smaller community/tutorial ecosystem

**Pricing:** Free tier available, paid plans ~$20+/mo  
**URL:** https://www.tripo3d.ai

---

### Rodin (Hyper3D)
**Best for:** High-fidelity hero assets that will be retopologized  
**Strengths:**
- Highest quality geometry of current AI tools
- Very detailed, photorealistic capability
- Good for creating a high-poly source to bake from

**Weaknesses:**
- Outputs high-poly by default — requires retopology for game use
- Retopology means more Blender work, against our minimal-Blender goal
- Slower generation
- Overkill for low-poly aesthetic

**Pricing:** Credit-based, free tier limited  
**URL:** https://hyper3d.ai

---

### Spline AI
**Best for:** Quick UI elements, simple 3D web graphics  
**Strengths:**
- Browser-based, no install
- Good for simple geometric shapes
- Easy to iterate

**Weaknesses:**
- Weak for game-quality assets
- Limited export options for Unity
- Not suited for characters or complex props

**Pricing:** Free tier, Pro ~$9/mo  
**URL:** https://spline.design

---

## Rigging & Animation

### Mixamo (Adobe)
**Best for:** Character rigging and animation — essential tool  
**Strengths:**
- Free (requires Adobe account)
- Auto-rigs humanoid characters in seconds
- Large library of pre-made animations (walk, run, idle, emote, etc.)
- Exports directly as FBX for Unity
- Works well with Meshy output after minor cleanup

**Weaknesses:**
- Humanoid characters only (won't rig animals/quadrupeds)
- Animation library is generic — custom animations require other tools
- Auto-rig quality degrades on non-standard proportions (chibi/stylized)
- Adobe account required

**Pricing:** Free  
**URL:** https://www.mixamo.com

---

### Cascadeur
**Best for:** Custom character animation with physics assist  
**Strengths:**
- AI-assisted keyframe animation with auto-physics (secondary motion)
- Good for the "snappy" expressive animations described in GOALS.md
- Exports FBX
- Physics-based follow-through built in

**Weaknesses:**
- Learning curve — not as instant as Mixamo
- Free tier is limited
- Better for bipeds than quadrupeds

**Pricing:** Free (limited), Indie ~$12/mo, Pro ~$60/mo  
**URL:** https://cascadeur.com

---

### ActorCore / AccuRIG
**Best for:** Quadruped/animal rigging and animation (pigs, cows, chickens)  
**Strengths:**
- Supports non-humanoid rigs including quadrupeds
- Animation library includes animal locomotion
- FBX export

**Weaknesses:**
- Less free content than Mixamo
- Animal animation library smaller than humanoid

**Pricing:** Credit-based, some free assets  
**URL:** https://actorcore.reallusion.com

---

## Texture & 2D Art Generation

### Stable Diffusion (local / ComfyUI)
**Best for:** Pixel art textures, UI elements, concept art, sprite sheets  
**Strengths:**
- Free to run locally
- Full control over style with LoRA/checkpoints
- Pixel art models available (e.g. pixel-art LoRA)
- No usage limits

**Weaknesses:**
- Setup overhead (GPU required for speed)
- Requires prompting skill and iteration
- Consistency across assets requires workflow discipline

**Pricing:** Free (local), various cloud options  

---

### Midjourney
**Best for:** Concept art, mood boards, UI art, NPC portraits  
**Strengths:**
- Highest aesthetic quality of image generators
- Great for establishing visual style references
- Fast iteration

**Weaknesses:**
- No local control
- Consistency between generations is hard
- Not ideal for tiling textures or technical art needs
- Subscription required

**Pricing:** Basic ~$10/mo, Standard ~$30/mo  
**URL:** https://www.midjourney.com

---

### Leonardo.ai
**Best for:** Game-specific texture generation, tiling textures  
**Strengths:**
- Game asset focused
- Tiling texture generation feature
- Pixel art model available
- Good consistency tools

**Weaknesses:**
- Quality ceiling below Midjourney for painterly art
- Free tier is limited

**Pricing:** Free tier (150 tokens/day), paid ~$12/mo+  
**URL:** https://leonardo.ai

---

## 3D Cleanup & General Purpose

### Blender (+ AI plugins)
**Best for:** Mesh cleanup, retopology, custom rigging, UV unwrapping  
**Strengths:**
- Free and open source
- Industry standard — every tool exports to Blender-compatible formats
- Python scripting for automation
- AI plugins available (CSM, various importers)

**Weaknesses:**
- Steep learning curve
- We want to minimize time here — use only for what other tools can't do

**Pricing:** Free  
**URL:** https://www.blender.org

---

## Recommended Pipeline

```
Concept/Style → Midjourney (mood board, NPC portraits)
         ↓
3D Generation → Meshy.ai (primary) / Tripo3D (props)
         ↓
Cleanup (if needed) → Blender (minimal)
         ↓
Rigging → Mixamo (humanoids) / ActorCore (animals)
         ↓
Animation → Mixamo library / Cascadeur (custom)
         ↓
Textures → Leonardo.ai (tiling) / Stable Diffusion (pixel art)
         ↓
Unity (URP, Unity 6)
```

---

*Last updated: 2026-04-09 — prices and tool capabilities change frequently, verify before committing to a paid tier.*
