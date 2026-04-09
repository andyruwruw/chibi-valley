# ChibiValley — Master Development Plan

Ordered by dependency. Each phase must be substantially complete before the next begins — later systems are built on earlier foundations. Subtasks within a phase can often be parallelized.

> **Updating this file:** Mark `[ ]` → `[~]` when starting, `[~]` → `[x]` when complete, `[-]` when deferred. Never mark complete if partially done.

---

## Status Key

| Symbol | Meaning |
|--------|---------|
| `[ ]` | Not started |
| `[~]` | In progress |
| `[x]` | Complete |
| `[-]` | Deferred / skipped |

---

---

## Phase 0: Project Foundation
*Everything needed before any game code is written. Do not skip or rush this — mistakes here are expensive to fix later.*

### 0.1 Unity Installation
- [x] Install Unity Hub from https://unity.com/download
- [x] Sign in to Unity Hub, activate free Personal license
- [x] Install Unity **6000.x LTS** with modules: Windows IL2CPP, Mac Mono, Linux IL2CPP, Documentation
- [x] Install Visual Studio 2022 with **Desktop development with C++** workload (required for IL2CPP compilation)
- [x] Verify install: open Unity Hub → Installs, confirm version shows LTS badge

### 0.2 Project Creation
- [x] Create new project in Unity Hub using **Universal 3D** template (not Built-in)
- [x] Name: `ChibiValley`, Location: `C:\Users\Andrew\Documents\Github\game`
- [x] Verify URP is active: **Edit → Project Settings → Graphics** → Scriptable Render Pipeline Settings shows a URP asset (not None)

### 0.3 Project Settings
- [x] **Edit → Project Settings → Player**: set Company Name, Product Name (`ChibiValley`), Version (`0.1.0`)
- [x] **Other Settings → Color Space**: switch to **Linear** (confirm the dialog)
- [x] **Other Settings → Scripting Backend**: IL2CPP on all three platform tabs (Windows, Mac, Linux)
- [x] **Other Settings → API Compatibility Level**: `.NET Standard 2.1`
- [x] **Edit → Preferences → External Tools**: set script editor to Visual Studio 2022, click Regenerate project files
- [x] **Edit → Project Settings → Quality → Anisotropic Textures**: Disabled
- [x] Configure URP Asset (`Assets/Settings/UniversalRenderPipelineAsset`): MSAA = Disabled, HDR = Enabled, Post Processing = Enabled

### 0.4 Folder Structure
- [x] Create full folder hierarchy under `Assets/_Project/` (Art, Audio, Data, Prefabs, Scenes, Scripts, Settings)
- [x] Create `Assets/StreamingAssets/` with subdirectories: Crops, Items, Animals, NPCs, Skills
- [x] Create `Assets/ThirdParty/` with subdirectories: Synty, Shaders
- [x] Create `Assets/Plugins/` for third-party Unity packages
- [x] Move default SampleScene to `Assets/_Project/Scenes/Farm/`, rename to `Farm`

### 0.5 Version Control
- [x] `git init` in `ChibiValley/` project root
- [x] `git lfs install`
- [x] Create `.gitignore` (ignore Library/, Temp/, Obj/, Builds/, .vs/, .idea/, Synty assets)
- [x] Create `.gitattributes` (LFS tracking for .fbx, .png, .jpg, .wav, .mp3, .ogg, .unity, .prefab, .asset, .meta, .blend, .glb)
- [x] Initial commit: `git commit -m "Initial project setup — Unity 6 URP"`
- [x] Create private GitHub repo `ChibiValley`, push main branch
- [x] Verify LFS is tracking binary files: `git lfs ls-files` returns results after adding an asset

### 0.6 Core Package Installation
- [x] **Window → Package Manager → Unity Registry**: install **Input System** — accept restart prompt, set Active Input Handling to **Both**
- [x] Install **ProBuilder** — verify toolbar appears in Scene View
- [x] Install **Animation Rigging**
- [x] Install **Cinemachine**
- [x] Install **2D Sprite** (for UI pixel art tools)
- [x] Install **Yarn Spinner** via git URL: `https://github.com/YarnSpinnerTool/YarnSpinner-Unity.git#current`
- [x] Verify all packages show in **Window → Package Manager → In Project** without errors

### 0.7 FMOD Setup
- [x] Download FMOD Studio + FMOD Unity Integration from https://www.fmod.com/download (free account)
- [x] Install FMOD Studio desktop application
- [x] Create new FMOD Studio project named `ChibiValley`, save alongside the Unity project (outside `Assets/`)
- [x] Import FMOD Unity Integration `.unitypackage` into Unity: **Assets → Import Package → Custom Package**
- [x] Follow FMOD setup wizard in Unity, link to the FMOD Studio project
- [x] Verify: FMOD menu appears in Unity menu bar, no console errors

### 0.8 Visual Foundation (Shaders + Synty)
- [x] ProPixelizer installed (`Assets/ProPixelizer/`)
- [x] Purchase **Flat Kit: Toon Shading and Water** from Unity Asset Store
- [x] Import Flat Kit into project
- [~] Add **ProPixelizer Render Pass** to the URP Renderer asset (`Assets/Settings/`) as a Renderer Feature
- [ ] Create a test scene: apply Flat Kit Stylized Surface shader to a primitive, apply ProPixelizer material to a character model — confirm toon shading + pixel outlines working together
- [ ] Configure Flat Kit water shader on a plane — verify it looks correct in the scene view
- [ ] Place a **Flat Kit Light Plane** in the test scene to verify god rays render correctly
- [ ] Purchase Synty POLYGON packs via subscription ($30/mo — cancel after importing what you need)
  - > **Consideration:** Minimum packs: Farm, Nature, Town/Village, Adventure, Icons, Particle FX. See `docs/asset-list.md` for full priority list.
- [ ] Import Synty packs into `Assets/ThirdParty/Synty/` — do NOT commit to git (already in .gitignore)
- [ ] Configure URP **Global Volume**: Bloom (Intensity 0.3–0.5), Color Adjustments (Saturation +25, Contrast +15, warm cream Color Filter), Vignette (0.15), Tonemapping (ACES)
- [ ] Set Flat Kit Stylized Surface as the default material so new objects use it automatically

### 0.9 Build Verification
- [ ] **File → Build Settings (or Build Profiles in Unity 6)**: add Farm scene, set target to Windows x86_64
- [ ] Build Windows executable to `C:\Builds\ChibiValley\Windows\` — confirm it runs
- [ ] Switch target to Linux, build to `C:\Builds\ChibiValley\Linux\` — confirm no compilation errors
- [ ] Switch target to Mac, build to `C:\Builds\ChibiValley\Mac\` — confirm no compilation errors
- [ ] Switch back to Windows for development
- [ ] Commit: `git commit -m "Configure URP, packages, shaders, verified builds"`

---

## Phase 1: Core Engine Systems
*The heartbeat of the game. Every other system hooks into these. Get them right.*

### 1.1 Game Data Loader
*Build this first — every other system depends on loading JSON data.*

- [ ] Create `DataLoader` static class in `Assets/_Project/Scripts/Core/`
- [ ] Implement generic `Load<T>(string path)` method that reads from `StreamingAssets/` and deserializes JSON via `JsonUtility` or `Newtonsoft.Json`
  - > **Consideration:** Unity's built-in `JsonUtility` is fast but doesn't support dictionaries or lists at root level. `Newtonsoft.Json` (available as Unity package `com.unity.nuget.newtonsoft-json`) is far more capable. Install Newtonsoft and use it.
- [ ] Create typed data model classes for each data category: `CropData`, `ItemData`, `AnimalData`, `NpcData`, `SkillNodeData`
- [ ] Write stub JSON files in each `StreamingAssets/` subdirectory with 1–2 example entries
- [ ] Write unit tests (Unity Test Framework) verifying round-trip load for each data type
- [ ] Add `DataLoader` initialization to a `GameBootstrap` MonoBehaviour that runs before any scene loads
  - > **Unity note:** Use a Bootstrap scene (Scene 0 in Build Settings) that loads once and persists via `DontDestroyOnLoad`. All managers initialize here. Every other scene assumes managers exist.

### 1.2 Time System
- [ ] Create `TimeManager` singleton MonoBehaviour in `Core/`
- [ ] Internal float clock that advances in real-time, scaled to 30 real minutes = 1 in-game day
- [ ] Expose: `CurrentHour` (0–24 float), `CurrentDay` (int), `CurrentSeason` (enum), `CurrentYear` (int)
- [ ] Implement `PauseTime()` / `ResumeTime()` — called by dialogue, menus, shops
- [ ] Fire C# events: `OnHourChanged`, `OnDayChanged`, `OnSeasonChanged` — other systems subscribe, never poll
- [ ] Implement sleep mechanic: calling `AdvanceToNextDay()` triggers end-of-day processing pipeline then loads next morning state
- [ ] Implement midnight pass-out: if `CurrentHour >= 24`, trigger pass-out sequence (fade out, penalty applied, wake up in bed)
- [ ] Wire to Unity's sky/lighting system: adjust Directional Light rotation and color temperature based on `CurrentHour`
  - > **Consideration:** Use a `Gradient` or `AnimationCurve` asset to define sky color over the day. Sample it in `Update()` based on normalized time. Store the curve in a ScriptableObject so it can be tweaked in the Inspector without code changes.
- [ ] Save/restore current time state as part of the save system

### 1.3 Season System
- [ ] Create `SeasonData` JSON schema: season name, day count (28), valid crop IDs, festival days, weather weights
- [ ] Populate `StreamingAssets/Seasons/` with 4 season files (Spring, Summer, Autumn, Winter)
- [ ] `TimeManager` reads season data, advances season when `CurrentDay > 28`
- [ ] Fire `OnSeasonChanged` event — crops, NPCs, weather all subscribe
- [ ] Implement seasonal visual changes: sky color gradient swaps, foliage color tint (shader parameter), ground texture swap
  - > **Consideration:** Don't swap Synty terrain assets per season — instead use a global shader parameter (exposed via URP Volume or a custom pass) to tint foliage. Much cheaper than asset swaps.
- [ ] Season name and icon displayed in HUD

### 1.4 Weather System
- [ ] Create `WeatherManager` singleton in `Core/`
- [ ] Each morning, roll weather for the day based on season-weighted probabilities (from `SeasonData` JSON)
- [ ] Weather states: `Sunny`, `Cloudy`, `Rainy`, `Stormy`, `Snowy` (Winter only), `Foggy`
- [ ] Visual per state: particle system for rain/snow, fog Volume override, cloud overlay sprite, ambient light adjustment
- [ ] Fire `OnWeatherChanged` event — farming (rain waters crops), NPCs (go indoors), audio (rain layer activates) all subscribe
- [ ] Implement weather forecast: tomorrow's weather deterministic and readable from a TV prop or NPC dialogue
- [ ] Rain state: broadcast `OnRainStarted` — `FarmGrid` subscribes and marks all tilled tiles as watered

### 1.5 Save / Load System
- [ ] Design save data schema as a single `SaveData` C# class with nested classes per system
  - > **Architecture note:** Each game system owns a `SaveState` nested class (e.g. `FarmSaveState`, `PlayerSaveState`). `SaveData` aggregates them. Systems implement `ISaveable` interface with `Save()` → returns their state object, `Load(state)` → restores from it.
- [ ] Implement `SaveManager` singleton: `Save(int slot)`, `Load(int slot)`, `DeleteSave(int slot)`
- [ ] Serialize `SaveData` to JSON via Newtonsoft, write to `Application.persistentDataPath/saves/slot_{n}.json`
  - > **Note:** `Application.persistentDataPath` resolves to the correct per-platform location automatically (AppData on Windows, ~/Library on Mac, ~/.config on Linux/Steam Deck)
- [ ] Add save file versioning: include `saveVersion` int in JSON, handle version mismatches gracefully
- [ ] Auto-save at end of each day (when player sleeps)
- [ ] Manual save via pause menu
- [ ] Implement save slot UI (3 slots minimum): show thumbnail of save info (day, season, playtime)
- [ ] Test save/load round-trip with all stub data before any real game data exists

### 1.6 Scene / Zone Manager
- [ ] Create `ZoneManager` singleton in `Core/`
- [ ] Define `ZoneTransition` trigger: a trigger collider in the scene with a destination zone ID and spawn point ID
- [ ] On trigger enter: fade out, unload current scene additively, load destination scene additively, position player at spawn point, fade in
  - > **Unity note:** Use **Additive scene loading** (`LoadSceneMode.Additive`) — always keep the Bootstrap scene loaded. Load/unload game zones on top of it. Never use single-mode loading or you'll destroy all your managers.
- [ ] Implement loading screen: simple fade-to-black with optional loading tip text
- [ ] Store last zone + spawn point in save data so the player returns to the correct location on load
- [ ] Create spawn point system: empty GameObjects tagged `SpawnPoint` with string IDs, registered with `ZoneManager` on scene load

### 1.7 Input System
- [ ] Create Input Actions asset (`Assets/_Project/Settings/ChibiValleyInputActions.inputactions`)
- [ ] Define Action Maps: `Gameplay` (move, interact, tool use, hotbar, sprint, map), `UI` (navigate, confirm, cancel, tab)
- [ ] Bind keyboard+mouse defaults and controller defaults (Xbox/PS layout + Steam Deck)
- [ ] Create `InputManager` singleton that wraps the generated `ChibiValleyInputActions` class and exposes clean events (`OnInteract`, `OnMovement`, etc.)
- [ ] Implement control scheme switching: auto-detect when a gamepad is connected/disconnected, swap UI prompts (keyboard icons → controller icons)
- [ ] Test on Steam Deck: verify all bindings work with Steam Input, check thumbstick dead zones
  - > **Consideration:** Steam Deck's built-in controls report as an Xbox controller in Unity. Test with the actual device or use Steam's gamepad testing tools. Dead zones should be ~0.15–0.2 for the Steam Deck thumbsticks.
- [ ] Store custom rebindings in `PlayerPrefs` or alongside save data (not in the save slot — rebindings are per-machine)

---

## Phase 2: Player
*The character the player inhabits. Movement must feel good before anything else is built.*

### 2.1 Player Model & Rig
- [ ] Define chibi character proportions spec: head-to-body ratio ~1:3, large eyes, compact limbs
- [ ] Generate base character mesh via **Meshy.ai** using a detailed text prompt referencing the spec
  - > **Asset pipeline:** Text prompt → Meshy.ai → download FBX → inspect in Blender (free, just to verify mesh) → upload to Mixamo → auto-rig → download FBX with rig → import to Unity. See `docs/ai-tools.md` for Meshy and Mixamo workflow details.
- [ ] Verify Mixamo auto-rig quality on the chibi proportions — chibi's large head may cause neck weighting issues; adjust in Blender if needed
- [ ] Import rigged FBX into `Assets/_Project/Art/Characters/Player/`
- [ ] Configure import settings: Rig = Humanoid, Animation Type = Humanoid, enable Read/Write, disable mesh compression
- [ ] Apply PS1 shader material to character mesh — set texture to a 64×64 pixel art skin
- [ ] Create `PlayerCharacter` Prefab in `Assets/_Project/Prefabs/Characters/`

### 2.2 Camera
- [ ] Create a Cinemachine Virtual Camera in the Farm scene
- [ ] Set Follow target to Player transform, Look At = none (fixed angle)
- [ ] Configure: Field of View = 30°, Body = Framing Transposer, pitch ~55°, no damping (fixed feel)
  - > **Consideration:** No camera damping at all can feel rigid. Try a very small Body Damping (0.05–0.1) — just enough to absorb teleport snaps without feeling "swimmy." Test both and pick what feels better.
- [ ] Add `CinemachineConfiner2D` or a custom boundary script to prevent camera from showing outside the zone boundary
- [ ] Implement zone-specific camera overrides: Cinemachine Blendlist Asset to define per-zone camera parameters (some zones may be tighter/wider)

### 2.3 Player Movement
- [ ] Create `PlayerMovement` MonoBehaviour — subscribe to `InputManager.OnMovement`
- [ ] Convert 2D input vector to 3D world movement relative to the fixed camera angle (rotate input vector by camera's Y euler angle)
  - > **Technical note:** Because the camera is at a fixed angle (not top-down orthographic), you need to project movement onto the XZ plane relative to the camera's forward direction. `Vector3 forward = Vector3.ProjectOnPlane(cam.transform.forward, Vector3.up).normalized`
- [ ] Use `CharacterController` component for movement (handles slopes and step height better than Rigidbody for a farming game)
- [ ] Implement movement speeds: walk (default), run (hold sprint input), and tool-use (slowed when tool is active)
- [ ] Add footstep system: raycast below player, detect ground tag, play appropriate footstep SFX category
- [ ] Implement interaction raycast: short raycast in front of player detects `IInteractable` interface, highlights target, fires on interact input
- [ ] Hook animation states: Animator Controller with blend tree for idle/walk/run driven by movement speed float parameter

### 2.4 Tool System
- [ ] Define `ITool` interface: `Use(Vector3 targetPosition)`, `GetStaminaCost()`, `GetToolType()`
- [ ] Implement tools as plain C# classes (not MonoBehaviours): `HoeTool`, `WateringCanTool`, `ScytheTool`, `AxeTool`, `FishingRodTool`, `WateringPotTool`
- [ ] Create `ToolController` MonoBehaviour on Player: holds reference to current `ITool`, calls `Use()` on tool input, deducts stamina
- [ ] Tool targeting: cast a ray from player forward, snap to nearest valid tile (farm grid) or interactable object
- [ ] Tool equip animation: swap mesh in the hand slot of the character rig when tool changes
- [ ] Implement tool range: watering can affects 1×1 (upgradeable to 3×3), hoe affects 1×1 (upgradeable), scythe affects 1×3 arc
- [ ] Visual feedback: highlight affected tiles with a tile overlay before confirming tool use

### 2.5 Stamina / Energy System
- [ ] Create `StaminaSystem` class (not MonoBehaviour, owned by a `PlayerStats` MonoBehaviour)
- [ ] Max stamina = 100 (base), increases via skill unlocks
- [ ] Each tool use costs stamina (values in `ItemData` JSON or a `ToolConfig` ScriptableObject)
- [ ] Stamina regenerates to full on sleep (calling `AdvanceToNextDay()`)
- [ ] If stamina hits 0: player can still walk but cannot use tools; slight visual indicator (desaturated player? screen edge pulse?)
- [ ] Pass-out mechanic at midnight: same result as sleeping, but small gold penalty (e.g. 10% of day's earnings, capped)
- [ ] Stamina displayed in HUD as a bar — animate bar smoothly on change

### 2.6 Hotbar & Quick Access
- [ ] `HotbarController` MonoBehaviour: 8 slots, each can hold an `ItemStack` reference
- [ ] Keyboard: 1–8 keys select slots. Controller: D-pad or shoulder buttons cycle slots
- [ ] Mouse scroll wheel cycles selected slot
- [ ] Visual: HUD bar at bottom of screen, selected slot has a highlight/scale-up animation
- [ ] Equipping a tool from hotbar calls `ToolController.EquipTool()`
- [ ] Equipping a seed/item from hotbar puts player in "placement mode" for that item

### 2.7 Inventory System
- [ ] Create `Inventory` class (plain C#): list of `ItemStack` (item ID string + quantity int), max 40 slots
- [ ] `ItemStack` operations: `Add(itemId, quantity)` → auto-stacks up to 64, returns overflow; `Remove(itemId, quantity)`; `HasItem(itemId, quantity)`
- [ ] `InventoryScreen` UI: grid of 40 slots rendered from `Inventory` data, item icon + stack count
- [ ] Item tooltip on hover/select: name, description, sell price, any special properties (loaded from `ItemData` JSON)
- [ ] Drag-and-drop on keyboard/mouse; D-pad navigation + confirm/cancel on controller
- [ ] Hotbar slots 1–8 are the top row of the inventory grid (shared, not separate) — selecting a hotbar slot IS selecting that inventory slot
- [ ] Item discard: drag to trash icon or hold discard button — confirmation prompt for stacks > 1
- [ ] Implement `IItemContainer` interface so inventory, chest storage, and shipping box all use the same slot logic

### 2.8 Character Customization
- [ ] Define outfit slot system: Hair, FaceAccessory, Top, Bottom, Shoes, BackAccessory, HeldItem
- [ ] Each slot has a `SkinnedMeshRenderer` on a child GameObject of the player rig, sharing the same skeleton
- [ ] `CustomizationController`: stores active item ID per slot, swaps meshes at runtime via `SkinnedMeshRenderer.sharedMesh`
- [ ] Customization screen UI: show character preview (render texture), slot buttons on side, item picker grid
- [ ] Generate initial outfit items via Meshy.ai using the character rig as reference — see `docs/ai-tools.md` for workflow
- [ ] Save active customization in `PlayerSaveState`
- [ ] Unlockable outfits: new outfit items found via exploration, NPC gifts, shop purchase — added to wardrobe pool

---

## Phase 3: Farm
*The core gameplay loop. The player will spend most of their time here.*

### 3.1 Tile Grid Foundation
- [ ] Create `FarmGrid` MonoBehaviour: owns a 2D array of `FarmTile` structs
- [ ] `FarmTile` struct: `TileType` enum (Dirt, Tilled, Watered, Planted, Path, Water, Decoration), `CropId` string, `GrowthStage` int, `WasWateredToday` bool, `PlantedOnDay` int, `Quality` float
- [ ] Grid dimensions: start at 15×15 (expandable via farm upgrades), configurable in a ScriptableObject
- [ ] Use Unity Tilemap for visual representation — one Tilemap layer per visual state (dirt, tilled, watered overlay, crop stage sprites)
- [ ] `FarmGrid` serializes its tile array as part of `FarmSaveState`
- [ ] Grid coordinate helper: `WorldToTile(Vector3)` → `Vector2Int`; `TileToWorld(Vector2Int)` → `Vector3 center`
  - > **Consideration:** Isometric 3D grids can have coordinate confusion. Decide early: the grid is always aligned to world XZ axes (not rotated 45°). The camera angle gives the isometric look — the grid is square in world space.

### 3.2 Tilling
- [ ] `HoeTool.Use()`: get target tile from grid, if `TileType == Dirt` → set to `Tilled`, update Tilemap visual
- [ ] Visual: tilled tile uses a different Tilemap tile asset (darker, furrow texture)
- [ ] Revert rule: if a tilled tile goes 5 days without being planted, revert to Dirt (time-based check in `OnDayChanged`)
- [ ] Expanded hoe: tool upgrade adds AoE tilling — use a preview overlay showing affected tiles before confirming
- [ ] SFX: dirt thud + scrape sound on till action
- [ ] Particle: small dirt puff particle at tile center on till

### 3.3 Planting
- [ ] Seed items are `ItemData` with a `cropId` reference field
- [ ] When player has a seed selected in hotbar and clicks/interacts on a `Tilled` tile: consume 1 seed, set `TileType = Planted`, store `CropId` and `PlantedOnDay`
- [ ] Validate: seeds can only be planted in their valid season(s) (from `CropData.validSeasons` array)
- [ ] Show the Stage 0 crop sprite on the tile immediately after planting
- [ ] `CropData` JSON schema: `id`, `name`, `validSeasons[]`, `growthDays` (per stage, array of 4 ints), `yieldItemId`, `yieldMin`, `yieldMax`, `seedBuyPrice`, `baseSellPrice`, `isMulitTile` bool, `tileSize` Vector2Int
- [ ] Create stub `CropData` for 3 starter crops (e.g. Turnip, Potato, Cabbage) to test the system end-to-end

### 3.4 Growth System
- [ ] `OnDayChanged` subscriber in `FarmGrid`: iterate all planted tiles, advance growth stage if watered
- [ ] Growth advancement logic: tile's total watered days tracked, compare against cumulative `growthDays` thresholds from `CropData`
- [ ] Update Tilemap visual sprite when stage advances (Stage 0–3 sprites defined in `CropData` or a `CropVisualData` ScriptableObject)
  - > **Consideration:** Store growth stage sprites in a ScriptableObject keyed by crop ID rather than in JSON (JSON can't reference Unity assets). `CropData` JSON holds the data; a `CropVisualRegistry` ScriptableObject maps crop IDs to their sprite sets.
- [ ] Stage 4 = fully mature: tile glows subtly or gains a "ready" visual indicator
- [ ] Un-watered tiles do not advance that day but do not lose progress either (un-watered = pause, not regression)

### 3.5 Watering
- [ ] `WateringCanTool.Use()`: target tile, if `Tilled` or `Planted` → set `WasWateredToday = true`, update Tilemap overlay
- [ ] Watered overlay: a separate Tilemap layer with a semi-transparent dark wet tile
- [ ] `OnDayChanged`: reset all `WasWateredToday` to false after processing growth (reset happens after, not before)
- [ ] `WeatherManager.OnRainStarted`: `FarmGrid.WaterAllTilled()` — marks every tilled/planted tile as watered
- [ ] Sprinkler item (mid-game): placed object on tile, auto-waters a 3×3 or 5×5 area each morning before day-tick
- [ ] Watering can capacity: finite charges (e.g. 50), refill at water source (pond, well). Track in `PlayerStats`.

### 3.6 Harvesting
- [ ] `ScytheTool.Use()` or `Interact` on mature tile: trigger harvest
- [ ] Harvest yield: `CropData.yieldMin` to `yieldMax` items of `yieldItemId`, with quality tier roll
- [ ] Quality roll: base 80% Normal, 15% Good, 5% Exceptional. Farming skill unlocks improve these odds
- [ ] Add yield items to player inventory (overflow drops on ground as world item if inventory full)
- [ ] Clear tile back to `Tilled` state after harvest (ready to replant same season)
- [ ] Some crops are "multi-harvest" (e.g. tomatoes): after harvest, reset to a late growth stage instead of clearing — defined by `isMultiHarvest` bool in `CropData`
- [ ] SFX + particle on harvest: satisfying "pluck" sound, sparkle particle burst

### 3.7 Multi-Tile Crops
- [ ] `CropData.isMulitTile` flag + `tileSize` (e.g. `{2, 2}` or `{3, 3}`)
- [ ] Planting: validate all tiles in the footprint are `Tilled`, reserve them all under the same crop instance ID
- [ ] Single `CropInstance` object tracks the master tile; satellite tiles reference master
- [ ] Harvest: interact on any tile in the footprint, process yields from master, clear all tiles
- [ ] Visual: the crop's sprite/prefab spans the full tile footprint

### 3.8 Season End
- [ ] `OnSeasonChanged` subscriber in `FarmGrid`
- [ ] Iterate all `Planted` tiles — if `CropData.validSeasons` does not include the new season → destroy crop, set tile to `Dirt`, spawn a "wilted crop" visual briefly
- [ ] Play a sad little sound + grey particle for wilted crops (gentle — not punishing)
- [ ] Perennial crops (a late-game unlock): `isPerennial` bool in `CropData` exempts them from season-end death

### 3.9 Shipping Box
- [ ] Shipping box is a placeable world object on the farm with an `IInteractable` implementation
- [ ] Opening shows a mini-inventory UI: player drags items in, committed items are queued for sale
- [ ] End-of-day processing (in `AdvanceToNextDay()` pipeline): iterate queued items, look up `ItemData.baseSellPrice`, apply seasonal multiplier, apply quality multiplier, sum total
- [ ] Seasonal multiplier: data-driven per `SeasonData` JSON — each season has a `priceMultipliers` dictionary keyed by crop category
- [ ] Next morning: gold deposited to player, shipping summary notification shown
- [ ] Shipping log: last 7 days of shipping summaries accessible from the shipping box UI

### 3.10 Farm Buildings
- [ ] Define `BuildingData` JSON: `id`, `name`, `cost`, `requiredBuildingId` (upgrade chain), `footprintSize`, `capacity` (for animal buildings), `prefabId`
- [ ] Building placement system: player opens blueprint menu, selects building, enters placement mode, previews footprint on farm grid, confirms if tiles are clear
- [ ] Carpenter NPC sells/constructs buildings: player submits order + gold, building appears after 2 in-game days
- [ ] Building upgrade chain: Coop → Big Coop → Deluxe Coop; Barn → Big Barn → Deluxe Barn; Silo (stores hay); Farmhouse upgrades (more interior space)
- [ ] Buildings reserve their footprint tiles as `BuildingTile` type — untillable
- [ ] Farm expansion: purchasable farm plot expansions that increase the usable grid area

---

## Phase 4: Animals
*The differentiating feature. These need to feel like characters, not resources.*

### 4.1 Animal Data & Species
- [ ] Create `AnimalData` JSON schema: `id`, `species`, `productItemId`, `productFrequency` (days), `baseProductQuality`, `growthDaysToAdult`, `baseCost`, `requiredBuilding`, `maxHappiness`, `personalityTraitPool[]`
- [ ] Populate starter species: Chicken (egg daily), Cow (milk daily), Pig (truffle weekly), Sheep (wool every 3 days)
- [ ] Create `AnimalInstance` save data class: `instanceId` (GUID), `speciesId`, `name`, `traits[]`, `happiness`, `age`, `bondedAnimalIds[]`, `daysWithoutCare`

### 4.2 Animal Base System
- [ ] `AnimalController` MonoBehaviour: holds `AnimalInstance` data, drives visual state, exposes interaction
- [ ] `AnimalRegistry` singleton: tracks all living animals by `instanceId`, handles spawning/despawning in scenes
- [ ] On scene load: `AnimalRegistry` spawns animal prefabs in the correct building or pasture zone
- [ ] Animal prefab structure: root GameObject with `AnimalController`, child with `SkinnedMeshRenderer` (swapped for baby/adult), `Animator`, `Collider`, `NavMeshAgent`

### 4.3 Personality System
- [ ] Define ~12 personality traits in a `PersonalityTraitData` JSON: `id`, `displayName`, `description`, and modifiers per behavior type
- [ ] Example traits: `Lazy` (lower happiness floor, slower to respond to petting), `Energetic` (higher movement speed, reacts excitedly to player), `Grumpy` (happiness drops faster, harder to max), `Affectionate` (bonds faster, higher happiness ceiling with bonds), `Curious` (wanders further from pen)
- [ ] At birth/purchase: randomly assign 2 traits from the species' `personalityTraitPool`
- [ ] Traits visible on animal's inspect UI panel
- [ ] Trait modifiers are data-driven multipliers on the happiness and behavior systems — no trait requires unique code paths

### 4.4 Happiness System
- [ ] `AnimalHappiness` class (owned by `AnimalController`): float 0–100
- [ ] Daily happiness changes (processed in `OnDayChanged`):
  - Petted today: +10
  - Fed today: +8
  - Not petted: -5
  - Not fed: -15
  - Outdoors during nice weather: +3
  - Outdoors during rain/storm: -5
  - Overcrowded pen: -3 per animal over capacity
  - Bonded animal present in same building: +5 per bond
  - Trait modifiers applied as multipliers to all deltas
- [ ] Happiness visible on inspect panel as a styled bar with a face emoji indicator
- [ ] Happiness affects product: below 30 = no product; 30–60 = Normal quality; 60–85 = Good quality; 85–100 = Exceptional quality; also affects quantity

### 4.5 Daily Care
- [ ] Petting: player interacts with animal → triggers pet animation on animal, +happiness delta queued, heart particle emits
- [ ] Feeding: hay/feed consumed from silo/inventory, marked as fed for the day
- [ ] Auto-feeder (mid-game building upgrade): if silo has hay, animals are auto-fed each morning — removes manual feeding requirement
- [ ] Water trough: animals need access to fresh water — trough on farm is auto-filled daily, player just needs to have one placed
- [ ] Pasture gate: toggle to let animals outside during the day. Animals outside during good weather get happiness bonus. Player must bring them inside before nightfall (or happiness penalty if left out)
- [ ] Hay production: scythe tall grass on the farm → hay item → stored in silo. Hay supply tracked in silo storage.

### 4.6 Animal Products
- [ ] `ProductionSystem` in `AnimalController`: checks `OnDayChanged`, if enough days have passed since last product and happiness > 30, generate product
- [ ] Product item dropped in the building as a world-pickup item (player walks over it or presses interact to collect)
- [ ] Product quality determined by happiness tier at time of generation
- [ ] Large milk, large egg, golden wool: exceptional quality variants with unique item IDs and higher sell prices
- [ ] Collection tracked in save data for achievement/statistics purposes

### 4.7 Growth Stages
- [ ] Baby animals: purchased or born at baby stage, use a smaller `babyMesh` SkinnedMeshRenderer
- [ ] Growth tracked by `AnimalInstance.age` (days), compares against `AnimalData.growthDaysToAdult`
- [ ] On reaching adult: swap `babyMesh` for `adultMesh` on `SkinnedMeshRenderer`, emit a celebratory particle, show notification
- [ ] Baby animals produce no products but still contribute to bonds and happiness ecosystem
  - > **Asset note:** Baby and adult meshes generated separately via Meshy.ai using consistent prompts. Same species, same style, smaller proportions for baby. Keep both FBXs in `Assets/_Project/Art/Animals/{Species}/`

### 4.8 Animal Bonds
- [ ] `AnimalInstance.bondedAnimalIds`: list of other animal instance IDs this animal has bonded with
- [ ] Bond formation: two animals in the same building for 7+ days and both have `Affectionate` trait or via random daily bond-formation roll (2% chance per pair per day)
- [ ] Bond UI: on animal inspect panel, show bonded animals with portrait icons
- [ ] Bond happiness buff: +5 happiness per bond per day (already included in 4.4 above)
- [ ] Breaking bonds: moving a bonded animal to a different building without the partner → -10 happiness spike for both

### 4.9 Animal AI & Navigation
- [ ] Set up NavMesh on farm pasture area (**Window → AI → Navigation** in Unity, bake NavMesh on pasture terrain)
- [ ] `AnimalAI` MonoBehaviour: roam state, idle state, flee state (if player runs at them), approach state (if player is nearby and animal is affectionate)
- [ ] Roam: pick random point within pasture bounds, navigate to it, wait 3–10 seconds, repeat
- [ ] Idle animations: driven by personality — `Lazy` animals idle more, `Energetic` animals roam more frequently
- [ ] React to petting: play personality-specific reaction animation (affectionate = nuzzle, grumpy = flinch, energetic = jump)
- [ ] Flock behavior: animals loosely cluster — if one animal moves, nearby animals have a chance to follow (simple separation/cohesion, no full boids needed)

### 4.10 Retirement
- [ ] Retirement trigger: `AnimalInstance.age >= AnimalData.maxAge` (defined per species)
- [ ] On next morning after max age: notification appears, player is informed the animal is retiring
- [ ] Retired animals move to the Sanctuary zone (a separate area of the farm, unlockable)
- [ ] Retired animals still appear, still petable, but produce no more products
- [ ] No death screen, no sad music — retirement is warm and positive

### 4.11 Cascadeur Animations
- [ ] For each species, define a list of personality animations needed: flop, nuzzle, sulk, excite, graze, sleep, play
- [ ] Source the base locomotion rig from Asset Store or AccuRIG (see `docs/animal-animation-options.md` for per-species decisions)
- [ ] Animate personality moments in Cascadeur using the sourced rig — export FBX animation clips
- [ ] Import animation clips into Unity, configure as non-looping triggers in the Animator Controller
- [ ] Wire triggers from `AnimalController` events: `OnPetted` → play nuzzle/flinch; `OnHappyThreshold` → play excite; `OnSadThreshold` → play sulk
- [ ] Review and tune each animation in Unity's Animation Preview before shipping

### 4.12 Animal Shop
- [ ] Marnie-equivalent NPC (animal vendor) with a shop UI listing purchasable animals per species
- [ ] Stock depends on player's available buildings (can't buy a cow without a barn)
- [ ] Purchase flow: select animal → pay gold → choose name → animal appears in appropriate building next morning as a baby
- [ ] Egg incubation: place an egg in a special incubator building upgrade → chick hatches after 3 days (alternative acquisition method)

---

## Phase 5: World & Exploration
*Hand-crafted zones filled with secrets, puzzles, and mythology.*

### 5.1 Zone Design & Layout
- [ ] Document all planned zones in `docs/world-zones.md` (create this file): zone name, size, key features, secrets count, connecting zones, NPC residents
- [ ] Minimum zones for v1: Farm, Farmhouse Interior, Town Square, Forest Path, Ancient Ruins, Cave System, Beach/Cliffside, Town Buildings (interiors)
- [ ] Block out each zone in ProBuilder first (grey-box) — no art yet, just walkable space and transition points
  - > **Workflow:** ProBuilder grey-box → playtest traversal feel → replace grey-box pieces with Synty assets → add secrets and props → lighting pass → audio pass. Never skip the grey-box step.
- [ ] Establish zone boundary colliders: invisible walls or natural barriers (cliffs, water) that prevent out-of-bounds movement

### 5.2 Farm Zone (Complete Scene)
- [ ] Place Synty Farm assets: farmhouse, fences, paths, trees, well, mailbox, shipping box prop
- [ ] Lay out initial farm grid area with visual borders (fences/hedges)
- [ ] Place barn and coop structures (locked/grayed out until purchased)
- [ ] Place pasture area with NavMesh baked
- [ ] Add ambient props: barrels, crates, flower patches, garden decorations
- [ ] Lighting: warm directional light, point lights on lanterns (evening), window glow
- [ ] Farmhouse interior as a separate scene (small scene, loads additively on door trigger)
- [ ] Interior: bed (sleep trigger), chest (storage), TV (weather forecast), upgrade-able furniture

### 5.3 Town Zone
- [ ] Place Synty Town assets: market stalls, notice board, fountain, NPC homes, shop buildings
- [ ] Define NPC home locations (each named NPC has a home building they return to at night)
- [ ] Notice board: interactable prop that shows available quests/events
- [ ] Market area: stalls active on market days (specific days per season), different vendors
- [ ] Place zone transition triggers to farm, forest, and other connecting zones
- [ ] Town square as gathering spot for festivals

### 5.4 Nature / Exploration Zones
- [ ] Forest Path zone: winding path through Synty Nature trees, foraging spots, 2–3 secrets
- [ ] Ancient Ruins zone: crumbling structures, mythology clue placement, environmental puzzle
- [ ] Cave System: ProBuilder + Synty Adventure assets, darker lighting, unique ambience, hidden chamber with lore
- [ ] Each zone: hand-place foliage, rocks, props using Synty assets for visual density
- [ ] Add foraging spots (interactable bushes/trees): yield items on interact, respawn after 3–5 days
  - > **Consideration:** Foraging spot state (harvested/ready) must be saved per zone. Add foraging spot IDs to save data.

### 5.5 Zone Transitions
- [ ] Place `ZoneTransition` trigger colliders at all connection points (doorways, paths between zones)
- [ ] Each transition: `destinationZoneId` string + `spawnPointId` string
- [ ] Matching `SpawnPoint` GameObjects in destination scenes with corresponding IDs
- [ ] Test every connection bidirectionally — entering and exiting should land at logical positions
- [ ] Loading screen: simple full-screen fade with optional zone name title card

### 5.6 Secret Spots
- [ ] Define `SecretSpot` component: `secretId` string, `discoveryText` string, `rewardItemId` (optional)
- [ ] On player trigger enter: if not already discovered → mark as found in `ExplorationSaveState`, show discovery notification, give reward, update map
- [ ] Secret types: hidden item caches, lore tablets with mythology text, hidden NPC conversation triggers, view points (beautiful vistas that unlock map art), mini-shrines
- [ ] Target: 5–8 secrets per explorable zone, 2–3 on the farm itself
- [ ] Secret tracker in map screen: "X/Y secrets found" per zone, no specific location hints (player must find them)

### 5.7 Environmental Puzzles
- [ ] Implement `IPuzzleElement` interface: `Activate()`, `Reset()`, `IsActive` bool
- [ ] Puzzle element types: `PressurePlate` (activates while player stands on it), `Lever` (toggle on interact), `PushBlock` (NavMesh obstacle, player can push), `TimedButton` (activates briefly), `LightBeam` (redirect with mirror objects)
- [ ] `PuzzleController` MonoBehaviour: holds a list of required `IPuzzleElement` states, fires `OnPuzzleSolved` event when all conditions met
- [ ] Puzzle outcomes: unlock a door, reveal a secret spot, spawn a reward chest, trigger a cutscene/lore moment
- [ ] Design 1 puzzle per explorable zone minimum for v1; more in later content updates
- [ ] Puzzle state (solved/unsolved) persisted in `ExplorationSaveState`

### 5.8 Mythology Thread
- [ ] Write in `docs/narrative-tbd.md`: the 10–15 key mythology beats players uncover across the world
- [ ] Lore fragment item: collectible item with flavour text describing a mythology piece, stored in a "Codex" UI section
- [ ] Ancient script tablets: placed in ruins and caves, interacting displays a translated text entry added to Codex
- [ ] NPC dialogue: certain NPCs hint at the mythology in conversation — these hints reference Yarn variables tracking which lore the player has found
- [ ] Mystery escalation: early game = vague hints; mid game = clearer connections; late game = revelatory moments in specific zones

### 5.9 Player Map
- [ ] `MapScreen` UI: top-down schematic view of the world, each zone shown as a region
- [ ] Zones fog-of-war: undiscovered zones are blacked out, discovered zones show zone name and schematic art
- [ ] Secret spot markers appear on map after discovery
- [ ] NPC location indicators: show where NPCs currently are (updates based on their schedule)
- [ ] Map art: simple stylized zone illustrations — generate via Midjourney, import as UI sprites
- [ ] Fast travel points shown on map once unlocked

### 5.10 Fast Travel
- [ ] Warp Totem: consumable item, use from inventory to teleport to farm (one-way, destroys item)
- [ ] Warp Point: permanent stone structures found in each zone — player interacts to unlock
- [ ] Once unlocked: open map, select a warp point, confirm — fade out, teleport, fade in
- [ ] Fast travel disabled during time-sensitive events (festivals, midnight warning window)

---

## Phase 6: NPCs & Relationships
*The social layer. NPCs should feel like people, not quest dispensers.*

### 6.1 NPC Data Design
- [ ] Create `NpcData` JSON schema: `id`, `name`, `age`, `occupation`, `homeZoneId`, `scheduleId`, `dialogueFileIds[]`, `giftPreferences{}` (item category → reaction tier), `relationshipThresholds{}`, `portraitSpriteId`, `personalityDescription`
- [ ] Design initial NPC roster (minimum 6–8 for v1 — enough for a town that feels populated):
  - [ ] Define name, occupation, personality, home, daily schedule, and role in mythology thread for each
  - [ ] Identify which NPCs are romanceable (minimum 2–3 options)
  - [ ] Document in `docs/npc-roster.md` (create this file)
- [ ] Generate NPC portrait art via Midjourney: chibi style, consistent framing, expression variants (happy, sad, neutral, flustered, angry)

### 6.2 NPC Base System
- [ ] `NpcController` MonoBehaviour: holds NPC identity (`NpcData`), schedule follower, dialogue trigger, relationship state
- [ ] `NpcRegistry` singleton: tracks all NPCs across zones, provides lookup by ID
- [ ] NPCs spawn in their home zone when that zone is loaded — `NpcRegistry` coordinates placement
- [ ] `IInteractable` on NPC: player interaction triggers dialogue

### 6.3 Daily Schedules
- [ ] `ScheduleData` JSON: list of `ScheduleEntry` objects — `{ "time": 8.0, "zoneId": "town", "waypointId": "market_stall_1", "activity": "tending_stall" }`
- [ ] `NpcScheduleFollower` MonoBehaviour: subscribes to `OnHourChanged`, checks if current time matches next schedule entry, moves NPC to waypoint via NavMesh
- [ ] Weather override: `ScheduleData` entries can have a `weatherOverride` — rainy days redirect outdoor NPCs to indoor waypoints
- [ ] Festival override: on festival days, all NPC schedules are replaced by a festival-specific schedule
- [ ] Schedules vary by season: some NPCs have different seasonal routines — define via separate schedule IDs per season in `NpcData`

### 6.4 Yarn Spinner Integration
- [ ] Create a `.yarn` dialogue file per NPC in `Assets/_Project/Data/NPCs/Dialogue/`
- [ ] Yarn file structure: nodes named `{NpcId}_default`, `{NpcId}_friend`, `{NpcId}_romance`, `{NpcId}_married`, `{NpcId}_gift_{reaction}`, `{NpcId}_birthday`
- [ ] Expose game state to Yarn as variables: `$player_name`, `$current_season`, `$relationship_{npcId}` (composite of all bars), `$has_item_{itemId}`, `$discovered_{secretId}`
- [ ] Implement Yarn commands for game actions: `<<give_item player "item_id" 1>>`, `<<play_animation npc "wave">>`, `<<trigger_event "festival_start">>`
- [ ] `DialogueManager` MonoBehaviour: starts/stops Yarn dialogue, feeds variables, handles commands, pauses `TimeManager`
- [ ] Portrait system: `DialogueManager` reads speaker name from Yarn, looks up portrait sprite, displays in dialogue UI with emotion variant

### 6.5 NPC Memory
- [ ] All persistent NPC memory stored as Yarn variables in a per-NPC variable storage that serializes to save data
- [ ] Track: `$last_gift_day_{npcId}` (prevent daily re-gifting), `$dialogue_choices_made_{npcId}[]`, `$events_witnessed_{npcId}[]`, `$player_gave_favorite_{npcId}` bool
- [ ] Dialogue references memory: "You brought me that [item] last week — I've been thinking about it" pulls from stored variables
- [ ] Memory decay: minor memories fade (tracked by day count), major story moments never fade

### 6.6 Relationship System
- [ ] `RelationshipState` class per NPC: multiple bars — define bar names and max values in `NpcData` JSON (e.g. `Friendship: 0–100`, `Trust: 0–100`, `Romance: 0–100` for romanceable NPCs)
- [ ] Bars increase/decrease via: daily conversation (+2–5 Friendship depending on dialogue choices), gifts (variable, based on preference tier), completing NPC quests (+10–20), witnessing negative events (-5–15)
- [ ] Dialogue choice impacts: Yarn `<<adjust_relationship {npcId} {bar} {delta}>>` command called within dialogue trees
- [ ] Relationship tiers: defined in `NpcData.relationshipThresholds` — crossing a threshold unlocks new dialogue nodes, changes NPC behavior, triggers cutscenes
- [ ] Visual: relationship screen shows all NPCs with their bars, tier label, and last interaction summary

### 6.7 Gift System
- [ ] Player can give 1 gift per NPC per day (tracked via `$last_gift_day_{npcId}` Yarn variable)
- [ ] Gift reaction tiers: `Loved` (specific favorites: +25 Friendship), `Liked` (preferred categories: +10), `Neutral` (+3), `Disliked` (-5), `Hated` (specific items: -20, unique angry dialogue)
- [ ] Gift preference data in `NpcData`: `lovedItems[]`, `likedCategories[]`, `dislikedItems[]`
- [ ] Birthday gifts: double all relationship bonuses; NPCs mention their birthday in advance via dialogue
- [ ] Gift history tracked in save data (last 10 gifts per NPC) — accessible in relationship screen

### 6.8 Seasonal Events & Festivals
- [ ] Define festival calendar in a `FestivalData` JSON: `id`, `name`, `season`, `day`, `zoneId`, `scheduleOverrideId`, `rewardItemIds[]`
- [ ] Minimum festivals for v1: Planting Festival (Spring 1), Harvest Fair (Autumn 15), Winter Solstice (Winter 28)
- [ ] Festival flow: notification the day before → NPC schedules switch to festival schedule → player can participate in festival mini-activities → evening ceremony → next morning returns to normal
- [ ] Festival mini-activities: flower arrangement, pie contest, lantern lighting — simple single-button interactions with reward items
- [ ] Festival dialogue: all NPCs have unique festival dialogue nodes in their Yarn files

### 6.9 Dating System
- [ ] Romance track unlocks when Friendship ≥ 70 AND Trust ≥ 60 for romanceable NPCs
- [ ] Date events: unique scripted outings — player receives a letter inviting them on a date, at scheduled time NPC appears at a special location
- [ ] 3 date events per romanceable NPC before confession becomes available, each with Yarn dialogue and a choice that affects Romance bar
- [ ] Confession scene: when Romance ≥ 80, player can give a Bouquet item → triggers unique cutscene/dialogue sequence → NPC becomes "dating" status
- [ ] Only one romantic partner at a time; attempting to give bouquet to a second NPC while dating triggers a polite rejection dialogue

### 6.10 Marriage System
- [ ] Marriage requires: dating status + at least 1 more post-dating date event + Mermaid's Pendant item (expensive, from a late-game vendor)
- [ ] Giving pendant triggers proposal cutscene: unique Yarn dialogue with a yes/no choice from the NPC (always yes if conditions met, with a charming scene)
- [ ] Marriage ceremony: 2 days later, town festival-style event, all NPCs attend
- [ ] Post-marriage: spouse moves to farmhouse, has a unique home schedule, has married-dialogue Yarn node set, gives the player small gifts occasionally
- [ ] Spouse room: farmhouse interior gains a shared room with spouse's personalised decor

---

## Phase 7: Economy & Progression
*The financial systems that make progression feel meaningful.*

### 7.1 Gold System
- [ ] `EconomyManager` singleton: `int Gold` property, `AddGold(int)`, `SpendGold(int)` (returns bool — insufficient funds returns false)
- [ ] Gold persisted in `EconomySaveState`
- [ ] Starting gold: 500g (enough to buy a few seed packets and get started)
- [ ] Gold change events: fire `OnGoldChanged(int newAmount, int delta)` — HUD subscribes to animate the counter

### 7.2 Shop System
- [ ] `ShopData` JSON: `id`, `npcId`, `stock[]` (each: `itemId`, `buyPrice`, `sellPrice`, `dailyRestockQuantity`, `season` optional)
- [ ] `ShopController` MonoBehaviour on vendor NPCs: opens `ShopUI` on interact
- [ ] `ShopUI`: two-panel layout — Shop Stock (left) and Player Inventory (right). Buy from left, sell from right
- [ ] Stock rotation: some items only appear on certain days or seasons (defined in `ShopData`)
- [ ] Sell any item: player can sell from inventory to any shop at that shop's `sellPrice` for that item (may be lower than shipping box seasonal price)
- [ ] Shop inventory persisted per day (purchased quantities tracked, restock overnight)

### 7.3 Seasonal Pricing
- [ ] `SeasonData` JSON gains a `sellPriceMultipliers` dictionary: `{itemCategory: multiplier}` per season
- [ ] Shipping box applies these multipliers at end-of-day calculation
- [ ] Early season bonus: crops sold in the first 7 days of their valid season get an additional 1.2× multiplier (supply is low)
- [ ] Late season penalty: crops sold in the last 7 days get a 0.85× multiplier
- [ ] Display expected sell price in inventory tooltip (shows calculated price for current date)

### 7.4 Tool Upgrades
- [ ] Blacksmith NPC shop: sells tool upgrades
- [ ] Upgrade tiers: Starter → Copper → Iron → Gold → Enchanted (5 tiers)
- [ ] Each upgrade: costs gold + resource items (ore, wood — gathered from world), takes 2 in-game days
- [ ] Upgrade effects per tier: reduced stamina cost, increased AoE (watering, tilling), increased tool durability (if durability system added)
- [ ] `ToolData` JSON: per-tool, per-tier stats — stamina cost, aoe radius, special effects

### 7.5 Quests & Notice Board
- [ ] `QuestData` JSON: `id`, `giverNpcId`, `type` (delivery, gather, kill-enemy, find-item), `requirements[]`, `rewardGold`, `rewardItemIds[]`, `rewardRelationshipDeltas{}`, `expiryDays`
- [ ] Notice board: shows 3–5 active quests at a time (randomized from available pool based on season and player progress)
- [ ] NPC quests: specific NPCs offer personal quests via dialogue — tracked separately from notice board
- [ ] Quest tracker: simple list in pause menu, shows active quests with progress

---

## Phase 8: Skill Trees
*Permanent, meaningful character progression.*

### 8.1 XP System
- [ ] `SkillManager` singleton: tracks XP per track — `Farming`, `Livestock`, `Exploration`, `Crafting`, `Social`
- [ ] XP awarded by: farming actions (till, plant, water, harvest), animal care (pet, collect products), exploration (discover secrets, enter new zones), crafting (make items), social (dialogue choices, gifts)
- [ ] XP values defined in a `SkillXpConfig` ScriptableObject (editable in Inspector without code changes)
- [ ] XP events fire `OnXpGained(SkillTrack track, int amount)` — skill tree UI subscribes for visual feedback

### 8.2 Skill Tree Data
- [ ] `SkillNodeData` JSON schema: `id`, `track`, `displayName`, `description`, `xpThreshold` (total XP to unlock), `prerequisites[]` (other node IDs), `effectType` (enum), `effectValue` (float)
- [ ] Effect types enumeration: `CropYieldBonus`, `CropQualityBonus`, `StaminaIncrease`, `ToolAoeIncrease`, `AnimalHappinessBonus`, `ProductQualityBonus`, `ExplorationRadarRange`, `ShopPriceDiscount`, `RelationshipGainBonus`, `CraftingSpeedBonus`, etc.
- [ ] Populate each track with ~15 nodes forming a branching tree — document full node designs in `docs/skill-trees.md` (create this file)
- [ ] Unlock effects applied in the relevant system when `SkillManager.IsUnlocked(nodeId)` returns true — checked at point of use, not cached (so save/load is simple)

### 8.3 Skill Tree UI
- [ ] `SkillTreeScreen`: 5 tab view (one per track), node graph rendered as connected UI elements
- [ ] Node states: Locked (greyed, shows XP requirement), Unlocked (highlighted), Available (enough XP but not yet claimed — pulsing)
- [ ] Claim mechanic: player must manually "claim" an available node to activate it — feels intentional, not automatic
- [ ] Description panel: selecting a node shows full description of its effect in a side panel
- [ ] XP progress bar per track at top of each tab

### 8.4 Unlock Effects Wiring
- [ ] `SkillManager.GetBonus(SkillTrack, EffectType)` returns float modifier — called by relevant systems
- [ ] Example: `HarvestSystem` calls `SkillManager.GetBonus(Farming, CropQualityBonus)` and adds result to quality roll
- [ ] All skill bonuses are additive multipliers — simple and predictable for balancing
- [ ] Test each effect type is actually applied — write integration tests or use a debug cheat menu

---

## Phase 9: UI & HUD
*Every screen the player interacts with. Invest in feel here — this is what players touch constantly.*

### 9.1 UI Framework Setup
- [ ] Choose UI approach: **Unity UI Toolkit** (modern, CSS-like, better performance) vs **uGUI** (older Canvas system, more tutorials)
  - > **Recommendation:** Use **uGUI** (Canvas-based) for this project. It has vastly more Unity indie game tutorials, better integration with existing Asset Store UI packs, and your pixel art UI style is well-served by sprite-based uGUI. UI Toolkit is the future but the ecosystem isn't there yet for this art style.
- [ ] Create a master UI Canvas: Screen Space - Overlay, sorted layers (HUD bottom, Menus top, Dialogue middle)
- [ ] Create a `UIManager` singleton: handles opening/closing screens, pausing time when menus open, input mode switching (gameplay → UI)
- [ ] Define a consistent pixel-art UI skin: button sprites, panel backgrounds, font (use a bitmap pixel font — Google Fonts has several free options like "Press Start 2P" or "Silkscreen")

### 9.2 HUD
- [ ] Clock: analog or digital? Decide on style — analog clock face fits the cozy aesthetic, digital is clearer
- [ ] Day/season display: "Day 5, Spring" with a seasonal icon
- [ ] Gold counter: coin icon + animated number
- [ ] Stamina bar: segmented or smooth — consider a heart motif for cozy feel
- [ ] Hotbar: 8 slots at bottom center, item icon + stack count, selected slot enlarged
- [ ] Notification area: top-right corner, slide-in notifications (new discovery, day summary, relationship milestone)
- [ ] Mini-map option: small zone map in corner (togglable) — defer to Phase 9 polish if complex

### 9.3 Dialogue UI
- [ ] Dialogue box: bottom third of screen, semi-transparent panel, speaker name tab, portrait on left
- [ ] Typewriter effect: characters appear one at a time, speed configurable, skip on interact press
- [ ] Choice buttons: appear after dialogue finishes, navigate with D-pad/arrow keys, confirm with interact
- [ ] Kawaii touch: speaker portrait has subtle idle animation (bob or blink), star eyes appear on certain emotional moments
- [ ] Yarn Spinner `DialogueRunner` wired to this UI via a custom `DialogueViewBase` implementation

### 9.4 Inventory Screen
- [ ] Full-screen overlay with dimmed background
- [ ] 5×8 grid (40 slots), each slot: item icon sprite + stack count in corner
- [ ] Selected slot: enlarged with item name, description, and action buttons (Use, Equip, Drop, Give)
- [ ] Controller: D-pad navigates, A confirms action, B closes
- [ ] Sort button: sort by category, then alphabetically within category
- [ ] Trash slot: drag/move item to trash icon → confirm dialog → removes item

### 9.5 Other Screens
- [ ] **Skill Tree Screen:** per Phase 8.3 above
- [ ] **Relationship Screen:** NPC portrait grid, click NPC → detail view with relationship bars, gift history, schedule peek
- [ ] **Map Screen:** zone schematic, fog of war, warp points, secret markers, NPC dots
- [ ] **Shop UI:** per Phase 7.2 above
- [ ] **Customization Screen:** rotating character preview (render texture), slot buttons, wardrobe grid
- [ ] **Codex/Lore Screen:** collected lore fragments listed, readable entries, mythology progress tracker
- [ ] **Pause Menu:** Resume, Save Game, Settings, Quit to Main Menu
- [ ] **Settings:** Master Volume, Music Volume, SFX Volume, Dialogue Speed, UI Scale, Controls (rebind), Colorblind Mode toggle
- [ ] **Main Menu:** title card art, New Game (name entry + character customization), Load Game (slot picker), Settings, Quit
- [ ] **Sleep/End of Day Screen:** shipping summary, tomorrow's weather forecast, "Good night" card with day count

---

## Phase 10: Audio
*FMOD adaptive audio. Audio is 50% of the feel — don't leave it until the end.*

### 10.1 FMOD Event Structure
- [ ] Plan FMOD event hierarchy in `docs/audio-design.md` (create this file):
  ```
  Music/
    Farm/Day, Farm/Dusk, Farm/Night
    Town/Day, Town/Night
    Forest, Ruins, Cave
    Festival, Combat (if added later)
  Ambience/
    Farm, Town, Forest, Cave, Rain, Wind, Night_Insects
  SFX/
    Player/Footsteps/{Surface}, Tools/{Tool}, Inventory
    Farming/Till, Water, Plant, Harvest, Wilt
    Animals/{Species}/Idle, Happy, Sad, Product
    UI/Click, Open, Close, Confirm, LevelUp, Notification
    World/Door, Chest, Puzzle_Solve, Secret_Found
  ```
- [ ] Create all event shells in FMOD Studio before adding audio content — Unity integration references events by path string

### 10.2 Music System
- [ ] Adaptive music via FMOD parameters: `TimeOfDay` (0–24 float), `Zone` (int enum), `Weather` (int enum)
- [ ] Each zone's music track: layered stems — base melody, harmony layer (adds at friendship milestones), weather layer (rain percussion), night layer (quieter, slower)
- [ ] FMOD transitions between zones: crossfade on scene load, not an abrupt cut
- [ ] Source music: generate base tracks via Suno.ai with prompts targeting cozy acoustic folk, download highest quality, import as `.ogg` to FMOD
- [ ] Supplement with royalty-free tracks from itch.io music packs where Suno output is weak
- [ ] Festival music: dedicated upbeat tracks, switch FMOD state on festival start

### 10.3 SFX Implementation
- [ ] Footsteps: FMOD event with a `Surface` parameter — different audio per surface tag (Grass, Wood, Stone, Dirt). Trigger from animation events (not timer) for sync
- [ ] Tool SFX: each `ITool.Use()` posts an FMOD event. Tool upgrade tier can vary the SFX via a `ToolTier` parameter in the FMOD event
- [ ] Animal vocals: FMOD event per species with random variation pool (5+ takes per vocal category). Triggered from `AnimalController` events
- [ ] UI SFX: `UIManager` posts FMOD events on screen open/close/confirm — pixel-game appropriate click sounds
- [ ] Harvest SFX: satisfying "pop" or "pluck" with a short reverb tail, pitched slightly by quality tier

### 10.4 Ambient Audio
- [ ] `AmbientAudioManager`: on zone load, start the zone's ambience FMOD event, set Weather parameter
- [ ] Rain ambience: crossfades in when `WeatherManager` fires `OnRainStarted`, crossfades out on `OnWeatherChanged` to non-rain
- [ ] Night insects: FMOD parameter driven by `TimeOfDay` — fades in after 19:00, fades out at 06:00
- [ ] Interior zones: muffled outdoor ambience + indoor room tone layer

---

## Phase 11: Polish & Feel
*The difference between a game that works and a game that delights.*

### 11.1 PS1 Visual Effects
- [ ] Audit all materials in all scenes — verify PS1 shader is applied everywhere (no default pink/grey Unity materials)
- [ ] Tune vertex wobble intensity per asset type: heavy wobble on terrain/environment (authentic), lighter wobble on characters (too much wobble on chibi faces looks bad), minimal on UI planes
- [ ] Affine texture warping: enable on environment, disable or reduce on character faces
- [ ] Color depth reduction: test levels (16-bit palette looks more authentic than 24-bit) — tune in shader settings
- [ ] Dithering: subtle dithering on shadow edges — gives the PS1 "rough shadow" look without harsh aliasing

### 11.2 Particle Systems
- [ ] Harvest sparkle: small star/sparkle burst, colors match crop type, 0.3s duration
- [ ] Watering splash: water droplets arc off the tile, fade quickly
- [ ] Animal happy: red heart emitter on petting, floats up and fades
- [ ] Level up / skill unlock: golden star burst + upward sweep, plays with SFX stinger
- [ ] Weather: rain (thin line particles, slightly angled), snow (fat slow flakes), falling leaves (Autumn)
- [ ] Secret discovery: a shimmer/reveal effect that radiates outward from the secret spot
- [ ] All particles use Point filtering textures consistent with the pixel art aesthetic

### 11.3 Animation Polish
- [ ] Character idle: subtle breathing animation on the player character (slight chest rise, slight head bob)
- [ ] Chibi face expressions: blend shapes (if Meshy provides them) or texture swaps for eyes — implement 4 expressions: neutral, happy, sad, surprised
- [ ] Tool use: exaggerated wind-up before tool strike (1–2 frames of anticipation) — feels snappy and expressive
- [ ] NPC greet animation: NPCs wave or nod when player enters their vicinity for the first time that day
- [ ] Screen transitions: UI screens slide in from a consistent direction, not just fade — feels more physical

### 11.4 Kawaii UI Juice
- [ ] Button hover: scale up 1.05×, slight colour lighten, satisfying click SFX
- [ ] Relationship milestone: screen flash + large heart graphic bursts from NPC portrait when a tier is reached
- [ ] Star eyes: on receiving a loved gift, NPC portrait swaps to star-eye expression variant momentarily
- [ ] Gold gain: coin counter animates number-rolling upward with a sparkle
- [ ] Day end card: illustrated "Good Night" screen with a chibi character yawning, day count prominent

### 11.5 Controller Haptics
- [ ] Tool use: short sharp rumble on tool strike impact
- [ ] Harvest: satisfying double-pulse on item collection
- [ ] Animal petting: soft gentle rumble while petting duration plays
- [ ] Dialogue advance: very subtle tick on each typewriter character (barely perceptible — cozy, not intrusive)
- [ ] Unlock / milestone: long satisfying rumble + pitch rise pattern

### 11.6 Accessibility
- [ ] Colorblind modes: Deuteranopia, Protanopia, Tritanopia — apply a colour correction LUT via URP post-process
- [ ] Text size: UI Scale slider in settings (0.8×, 1.0×, 1.2×, 1.5×)
- [ ] Input remapping: full rebind support via Unity Input System's rebinding API
- [ ] Dialogue speed: slider from slow (typewriter deliberate) to instant (full text appears at once)
- [ ] Screen shake toggle: disable any screen shake effects for motion sensitivity
- [ ] High contrast mode for UI: toggle that increases text contrast and UI element borders

---

## Phase 12: Multiplayer
*Deferred. Singleplayer ships first. Do not start this phase until Phase 11 is complete and the game is released.*

### 12.1 Pre-Multiplayer Architecture Review
- [ ] Audit all game systems for hidden local assumptions (static singletons that assume single player, non-networked state, etc.)
- [ ] Document required changes in `docs/multiplayer-design.md`

### 12.2 Network Foundation
- [ ] Integrate **Unity Netcode for GameObjects** (NGO)
- [ ] Integrate **Unity Relay** (removes need for players to open ports)
- [ ] Implement lobby system: host creates game, share code, client joins

### 12.3 State Synchronization
- [ ] Player positions and animations synchronized via `NetworkTransform` + `NetworkAnimator`
- [ ] Farm state (crops, tiles) synchronized via `NetworkVariable` on `FarmGrid`
- [ ] Animal state synchronized (tricky — animals with AI need authority management)
- [ ] Inventory: each player has their own inventory, shared shipping box synchronized
- [ ] Economy: shared gold pool or separate per-player? (Design decision TBD)
- [ ] Time system: host is authoritative, clients receive time state

### 12.4 NPC Multiplayer Rules
- [ ] NPC dialogue: only one player can be in dialogue with an NPC at a time (lock mechanism)
- [ ] Relationship bars: per-player or shared? (Design decision TBD)
- [ ] Gift system: per-player daily limit maintained

### 12.5 Co-op Balancing
- [ ] Scale farm size for 2–4 players
- [ ] Adjust economy (more players = more earning potential — tune shipping prices or costs)
- [ ] Festival participation: all players participate in festival events simultaneously
- [ ] Test full day cycle with 2–4 players end-to-end

---

## Cross-Cutting Concerns
*These apply across all phases — keep them in mind throughout.*

- **Controller support everywhere:** Every UI, every interaction must be navigable without a mouse. Test with a controller after every UI screen is built.
- **Save early, save often:** Wire new systems into the save/load cycle as they are built, not at the end. Retrofitting save support is painful.
- **JSON-first data:** Never hardcode a numeric game value. If it's a number that could be tweaked (stamina cost, crop price, relationship delta), it goes in JSON or a ScriptableObject.
- **Event-driven architecture:** Systems communicate via C# events, not direct references. `TimeManager` doesn't know about crops — crops subscribe to `TimeManager.OnDayChanged`. This prevents spaghetti dependencies.
- **PS1 aesthetic consistency:** Every new asset imported must have Point filter mode set, mipmaps disabled, and PS1 shader applied. Build a checklist for asset import.
- **No magic numbers in code:** All constants in a `GameConstants` static class or data-driven. Comments explain the "why" not the "what."
- **Branch per feature:** One Git branch per major feature, merge to main when complete and tested.
