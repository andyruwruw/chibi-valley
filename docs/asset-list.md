# ChibiValley — Asset Shopping List

All assets evaluated for the project. Decisions logged here even when not purchased.  
Verify Unity 6 / URP compatibility on the Asset Store page before buying — this changes frequently.

---

## Category 1: Rendering & Visual Style

*The most important purchases — these define the entire look of the game.*

### 1A. Toon + Pixel Art Rendering Stack (DECIDED — see recommendation at bottom of section)

---

#### ProPixelizer — Realtime 3D Pixel Art ✅ INSTALLED
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/fullscreen-camera-effects/propixelizer-realtime-3d-pixel-art-177877)  
**Price:** ~$30  
**Unity 6 / URP:** URP confirmed. Unity 6 compatibility — **verify before buying** (last confirmed on Unity 2023.2)  
**Status:** Already installed at `Assets/ProPixelizer/`

**What it does:** Converts 3D objects to look like 2D pixel art in real-time. Per-object pixelization — you can mix pixelated and non-pixelated objects in the same scene. Macropixel size up to 5×5 screen pixels. Shader Graph support for authoring custom pixelated shaders.

**Strengths:**
- Per-object control — apply to characters only, or environment only, or everything
- Pixel-perfect outlines around objects (huge for chibi characters)
- Color palette reduction per object
- Dither patterns for shading
- Shader Graph integration — extensible
- Actively maintained, large user base

**Weaknesses:**
- Does NOT do PS1 vertex wobble or affine texture mapping — it's pixel art, not PS1 simulation
- Heavier performance cost than a simple shader swap
- Unity 6 compatibility unconfirmed — **check the Asset Store Q&A tab before buying**

**Best for ChibiValley:** Character outlines, making chibi characters read cleanly at any scale, pixel-perfect rendering on character textures

---

#### Retro Shaders Pro for URP — Daniel Ilett
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/retro-shaders-pro-for-urp-287937) | [itch.io](https://danielilett.itch.io/psx-shaders-pro-for-unity-urp) | [Author site](https://danielilett.com/retro-shaders-pro/)  
**Price:** ~€14.71 (~$16)  
**Unity 6 / URP:** ✅ Confirmed  
**Decision: NOT PURCHASING** — aesthetic direction changed to toon + pixel art, not PS1 retro simulation.

**What it does:** Full PS1/N64 shader simulation — vertex wobble, affine texture warping, color depth limiting, dithering, CRT post-process.

**Why we're not using it:** ChibiValley's aesthetic is now toon + pixel art (Flat Kit + ProPixelizer). PS1 artifacts like vertex wobble and affine warping actively conflict with clean toon shading. Retro Shaders Pro solves a problem we no longer have.

**Keep in mind:** If the aesthetic direction ever shifts back toward retro, this is the pack to buy. Very affordable and Unity 6 confirmed.

---

#### PSX Shader Kit
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/psx-shader-kit-183591)  
**Price:** ~$15  
**Unity 6 / URP:** Check store page

**What it does:** PS1-style shader kit — vertex snapping, dithering, low-res rendering.

**Strengths:** Affordable, straightforward  
**Weaknesses:** Less actively maintained than Retro Shaders Pro, fewer features  
**Verdict:** Retro Shaders Pro is better in every way at a similar price — skip this unless RSP has compatibility issues.

---

#### PSX Retro
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/psx-retro-261462)  
**Price:** ~$20  
**Unity 6 / URP:** Check store page

**Strengths:** Low poly, scanlines, vertex snapping, PSX dithering  
**Weaknesses:** Smaller community, less documentation  
**Verdict:** Retro Shaders Pro is more comprehensive — only consider as a fallback.

---

#### URP-PSX (Open Source)
**GitHub:** [Kodrin/URP-PSX](https://github.com/Kodrin/URP-PSX)  
**Price:** Free

**What it does:** Open source PS1 retro graphics plugin for URP with Shader Graph.  
**Strengths:** Free, Shader Graph based, customizable  
**Weaknesses:** Community project, no guaranteed Unity 6 support, no paid support  
**Verdict:** Good reference or starting point if commercial options fail, not recommended as primary.

---

#### Flat Kit: Toon Shading and Water — Dustyroom
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/vfx/shaders/flat-kit-toon-shading-and-water-143368) | [Docs](https://flatkit.dustyroom.com)  
**Price:** ~$50–60 (check store page for current price)  
**Unity 6 / URP:** ✅ Confirmed — Unity 6 Preview support added

**What it does:** A complete stylized shading toolkit — toon/cel surface shading, a feature-rich stylized water shader (URP only, 30+ parameters), depth/normal/color outlines, gradient fog, Light Plane god rays as 3D geometry, terrain shader.

**Strengths:**
- Best stylized water shader available for URP — farm ponds, rivers, beach zone, ocean
- Light Plane shader produces better god rays than screen-space approaches
- Actively maintained by Dustyroom, excellent documentation
- Unity 6 confirmed ✅
- Gradient fog adds atmosphere to forest and cave zones

**Weaknesses:**
- Toon surface shading is a *modern, clean* look — **directly conflicts with the PS1/Retro Shaders Pro aesthetic** if applied to the same objects
- Outlines are redundant if using ProPixelizer (which has its own outline system)
- Pricey for something you're buying mostly for one feature (water)

**How to use it with ChibiValley's stack:**

| Flat Kit Feature | Use it? | Notes |
|-----------------|---------|-------|
| Toon surface shader | ❌ No | Use Retro Shaders Pro for all surface shading |
| **Water shader** | ✅ Yes | Farm pond, rivers, beach, ocean |
| **Light Plane (god rays)** | ✅ Yes | Outdoor scenes — better than screen-space |
| Outlines | ❌ No | ProPixelizer handles this |
| Gradient fog | ⚠️ Optional | Nice for forest/cave atmosphere |
| Terrain shader | ⚠️ Optional | Compare with Retro Shaders Pro terrain shader |

**Verdict: Buy it for water and Light Plane alone.** If you have any bodies of water in your world (farm pond, river, beach zone), this pays for itself. Do not apply the toon surface shader to any asset — it will fight the PS1 look.

---

### RECOMMENDATION: Flat Kit (primary) + ProPixelizer (pixel art layer) ✅ DECIDED

Aesthetic direction: **low-poly toon pixel art**. Clean cel-shaded geometry with a pixel art rendering layer on top. Pretty, modern, original — not retro simulation.

| Effect | Tool |
|--------|------|
| Surface shading — all geometry | **Flat Kit** |
| Water (pond, river, beach, ocean) | **Flat Kit** |
| God rays (3D Light Plane) | **Flat Kit** |
| Gradient fog (forest, caves) | **Flat Kit** |
| Pixel-perfect outlines on characters | **ProPixelizer** ✅ installed |
| Macropixel rendering on characters + props | **ProPixelizer** ✅ installed |
| Custom pixel art shaders (Shader Graph) | **ProPixelizer** ✅ installed |
| Bloom, color grading, vignette | URP Volume (built-in) |

**ProPixelizer is already installed.** Buy Flat Kit to complete the stack.  
**Combined additional cost: ~$50–60 (Flat Kit only).**

---

## Category 2: Environment & World Assets

### Synty POLYGON Packs
**Store:** [Synty Store](https://syntystore.com) | [Unity Asset Store](https://assetstore.unity.com/publishers/5217)  
**Subscription:** $30/mo — access to 130+ packs  
**Individual packs:** $49.99–$79.99 each (farm $49.99, others vary)

**Subscription vs. Individual:**

| Option | Cost for 5 packs | Best if... |
|--------|-----------------|-----------|
| Subscription | $30/mo, cancel anytime | You need 3+ packs and want flexibility |
| Individual (5 packs) | ~$275 one-time | You know exactly what you need and won't need more |
| Subscription (1 year) | $360 | You're actively developing and want everything |

**Recommendation: Start with the subscription.** You'll need at minimum 5 packs (below), and at $30/mo you break even vs. buying 1 pack in month one. Cancel when you've imported everything you need and are done with art production. Download everything you might need before cancelling.

**Priority packs for ChibiValley:**

| Pack | Contents | Priority |
|------|----------|---------|
| [POLYGON Farm](https://syntystore.com/products/polygon-farm-pack) | Farm buildings, crops, fences, animals, tools, vehicles | ⭐ Essential |
| [POLYGON Nature](https://syntystore.com/products/polygon-nature-pack) | Trees, rocks, foliage, terrain pieces, water | ⭐ Essential |
| [POLYGON Town / Village](https://syntystore.com/products/polygon-town-pack) | NPC homes, shops, market stalls, street props | ⭐ Essential |
| [POLYGON Adventure](https://syntystore.com/products/polygon-adventure-pack) | Dungeons, caves, ruins, chests, traps | ⭐ Essential |
| [POLYGON Icons](https://syntystore.com/products/polygon-icons-pack) | 1000+ UI icons for inventory items | High |
| [POLYGON Particle FX](https://syntystore.com/products/polygon-particle-fx-pack) | Pre-built stylized particle effects | High |
| [POLYGON Modular Fantasy Hero Characters](https://syntystore.com/products/polygon-modular-fantasy-hero-characters) | Modular NPC bodies for populating the town | Medium |
| [POLYGON Dungeons](https://syntystore.com/products/polygon-dungeon-pack) | More dungeon/underground content | Medium |
| [POLYGON Starter Pack](https://syntystore.com/products/polygon-starter-pack) | Free — basic props and primitives | Free ✅ |

> **Note on characters:** You said no Synty modular characters for the *player*, but Synty character packs are fine for NPC bodies that you customize with textures/materials. They give you consistent proportions and rigs across your NPC cast without having to generate each one via Meshy.

---

## Category 3: UI Assets

### Pixel Art Full GUI / UI Kit + 151 Icons
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/2d/gui/pixel-art-full-gui-ui-kit-151-icons-205222)  
**Price:** ~$15–20  
**Best for:** Inventory slots, buttons, panels, dialogue boxes

**Strengths:** Ready-made pixel art UI components, consistent style  
**Weaknesses:** Style may not match your exact colour palette — expect to retint sprites

---

#### Wooden Pixel Art GUI / UI Kit — 750+ PNG
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/2d/gui/wooden-pixel-art-gui-ui-kit-over-750-png-264906)  
**Price:** ~$20  
**Best for:** Cozy wooden/nature-themed UI panels

**Strengths:** Huge variety, very cozy aesthetic, matches ChibiValley vibe  
**Weaknesses:** Wooden theme may be too rustic for some UI sections

---

#### Pixel UI
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/2d/gui/pixel-ui-128440)  
**Price:** ~$10  
**Best for:** Basic pixel-style UI elements

**Strengths:** Clean, minimal, easy to customize  
**Weaknesses:** Smaller set than the above options

---

### RECOMMENDATION: Wooden Pixel Art GUI Kit + POLYGON Icons

The Wooden GUI Kit handles panels, buttons, and frames. POLYGON Icons handles the inventory item icons (already stylistically matched to Synty assets). Together they cover most UI needs.

Generate remaining UI art (NPC portraits, map art, title screen) via **Midjourney** with chibi/pixel art prompts.

---

## Category 4: Game Feel / Juice

### Feel (formerly MMFeedbacks) — MoreMountains
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/tools/particles-effects/feel-free-215738) | Free version available  
**Price:** Free (Feel Free) / ~$55 (Feel — full version)  
**Unity 6 / URP:** ✅ Confirmed

**What it does:** A game feel toolkit — camera shakes, flashes, sound triggers, particle bursts, UI animations all wired to "Feedbacks" that fire on game events. The free version covers 90% of needs.

**Strengths:**
- Designed specifically for "game juice" — the rewarding feel on actions
- Handles screen shake, flash, haptics, particle triggers from one component
- Widely used in indie games, huge community
- No code needed for basic feedback setup

**Weaknesses:**
- Full version ($55) needed for some advanced features
- Adds another system to learn

**Best for ChibiValley:** Harvest sparkle, petting hearts, level up flash, dialogue pop — all the kawaii juice moments

**Recommendation: Start with Feel Free, upgrade to paid if needed.**

---

### DOTween (HOTween v2)
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/tools/animation/dotween-hotween-v2-27676) | Free version available  
**Price:** Free / ~$15 (Pro)

**What it does:** The standard Unity tweening library. Animate anything (position, scale, colour, UI) with clean chainable code. `DOTween.Sequence()` is how all UI slide animations, bounces, and transitions should be done.

**Strengths:**
- Industry standard — almost every Unity indie game uses this
- Extremely fast, zero GC allocation in hot paths
- Free version is fully functional

**Weaknesses:** None meaningful — just learn the API

**Recommendation: Install the free version immediately. Use it everywhere.**

---

## Category 5: Utility / Developer Tools

### Easy Save 3
**Store:** [Unity Asset Store](https://assetstore.unity.com/packages/tools/input-management/easy-save-the-complete-save-data-serializer-system-768)  
**Price:** ~$45  
**Decision: SKIPPED** — we're using custom JSON serialization. Log for reference only.

---

### Dialogue System for Unity
**Store:** Unity Asset Store  
**Price:** ~$65  
**Decision: SKIPPED** — using Yarn Spinner (free, already installed).

---

### TextMeshPro
**Price:** Free — built into Unity  
**Status:** ✅ Already available — use this for ALL text in the game. Never use the legacy Unity Text component.

> **Setup:** **Window → TextMeshPro → Import TMP Essential Resources** — do this now if not done. Imports the default fonts and shaders TMP needs.

---

### NavMesh Components (Unity)
**Price:** Free — Unity package  
**Install:** `com.unity.ai.navigation` via Package Manager

**What it does:** NavMesh baking and runtime agents for NPC and animal pathfinding.  
**Recommendation: Install now** — needed for Phase 4 (animals) and Phase 6 (NPCs).

---

### Cinemachine
**Status:** ✅ Already installed (Phase 0.6)

---

### Newtonsoft JSON for Unity
**Install:** `com.unity.nuget.newtonsoft-json` via Package Manager  
**Price:** Free  
**Recommendation: Install now** — needed for Phase 1.1 (Game Data Loader). Far more capable than Unity's built-in JsonUtility.

---

## Category 6: Audio Assets

### FMOD Studio
**Status:** ✅ Already installed (Phase 0.7)

---

### Royalty-Free Music Sources

| Source | Style | Price |
|--------|-------|-------|
| [itch.io music tag: cozy](https://itch.io/game-assets/tag-music) | Indie dev music, often cozy/lo-fi | Free–$15/pack |
| [OpenGameArt.org](https://opengameart.org) | Community, varied quality | Free |
| [Epidemic Sound](https://www.epidemicsound.com) | Professional, large library | ~$15/mo |
| [Musicbed](https://www.musicbed.com) | High quality indie/folk | $$$, skip for now |
| [Suno.ai](https://suno.com) | AI-generated, prompt-based | Free tier / $8/mo Pro |

**Recommendation:** Generate base tracks in Suno (free tier), supplement with itch.io royalty-free packs for variety.

---

### SFX Sources

| Source | Contents | Price |
|--------|----------|-------|
| [Freesound.org](https://freesound.org) | Community recordings, CC licensed | Free |
| [Kenney.nl](https://kenney.nl/assets) | Game-ready SFX packs | Free |
| [itch.io SFX](https://itch.io/game-assets/tag-sound-effects) | Indie SFX packs | Free–$20/pack |
| [ElevenLabs Sound Effects](https://elevenlabs.io/sound-effects) | AI-generated SFX | Free tier / paid |
| Synty POLYGON Particle FX | Visual + audio particles | Synty subscription |

**Recommendation:** Kenney.nl for UI clicks and basic SFX (free, game-ready, huge library). Freesound for specific recordings. ElevenLabs for anything unusual you can't find.

---

## Full Recommended Purchase List

Ordered by priority:

| # | Asset | Source | Price | Phase Needed |
|---|-------|--------|-------|-------------|
| 1 | **ProPixelizer** | Unity Asset Store | ~$30 | ✅ Already installed |
| 2 | **Flat Kit: Toon Shading and Water** | Unity Asset Store | ~$50–60 | Phase 0.8 |
| 3 | **Synty Subscription** (1–2 months) | Synty Store | $30/mo | Phase 0.8 |
| 4 | **DOTween Free** | Unity Asset Store | Free | Phase 9 |
| 5 | **Feel Free (MMFeedbacks)** | Unity Asset Store | Free | Phase 11 |
| 6 | **Newtonsoft JSON for Unity** | Package Manager | Free | Phase 1.1 |
| 7 | **NavMesh Components** | Package Manager | Free | Phase 4 |
| 8 | **TextMeshPro Resources** | Unity (built-in) | Free | Phase 9 |
| 9 | **Wooden Pixel Art GUI Kit** | Unity Asset Store | ~$20 | Phase 9 |
| 10 | **Kenney.nl SFX packs** | kenney.nl | Free | Phase 10 |
| 11 | **Suno.ai Pro** | suno.com | $8/mo | Phase 10 |

**Estimated cost:** ~$120–130 one-time + $30/mo Synty (cancel after 1–2 months) + $8/mo Suno  
**Immediate buys to unblock Phase 0.8:** Flat Kit (~$50–60) + Synty subscription ($30) — ProPixelizer already installed

---

*Last updated: 2026-04-09 — verify all prices and Unity 6 compatibility on store pages before purchasing*
