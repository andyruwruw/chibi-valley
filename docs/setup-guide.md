# ChibiValley — Project Setup Guide

Complete step-by-step setup for Windows development, targeting Windows, Mac, and Steam Deck (Linux).

**Engine:** **Unity 6 LTS** (version line **6000.x**, e.g. `6000.3.xf1`). This guide matches the Unity 6 editor and Hub — not Unity 2022/2023.

**Assumed knowledge:** Comfortable with Git, GitHub, command line, and installing software.  
**Not assumed:** Any Unity experience whatsoever.

**Estimated time: 2-3 hours** (mostly downloads)

### If you already know older Unity

| Older habit | In Unity 6 |
|-------------|------------|
| **File → Build Settings** | **File → Build Profiles** (scenes: **Platforms → Scene List**) |
| One URP asset in Project for everything | **Pipeline asset** (`PC_RPAsset`, …) for MSAA/HDR; **`UniversalRenderPipelineGlobalSettings`** opens **Project Settings → Graphics** |
| Flat Project Settings lists | Many sections use **foldouts** — expand **Other Settings**, **Rendering**, etc. |

---

## Part 1: Install Prerequisites

### Git + Git LFS

```bash
# Verify Git is installed
git --version

# Install Git LFS if you haven't
# Download from https://git-lfs.com, run installer, then:
git lfs install
# Expected output: "Git LFS initialized."
```

---

## Part 2: Install Unity

### Step 2.1 — Install Unity Hub

Unity Hub is a launcher that manages Unity versions and projects — think of it like nvm but for Unity engines. Every version of Unity you install sits alongside others; projects declare which version they use.

1. Go to https://unity.com/download
2. Download and install **Unity Hub**
3. Launch it

### Step 2.2 — License Unity

Unity is free for personal/indie use under $100k revenue.

1. In Unity Hub, click **Sign in** (top left) → create a Unity account if needed
2. After signing in: click your avatar (top left) → **Manage licenses**
3. Click **Add** → **Get a free personal license**
4. Done — this license is tied to your account, not your machine

### Step 2.3 — Install Unity 6 LTS

1. In Unity Hub, click **Installs** (left sidebar) → **Install Editor**
2. Select **Unity 6** with the **LTS** badge (version `6000.x.x LTS`)
3. On the **modules** screen, enable these — names can vary slightly by Hub version; match the **platform**, not every sub-checkbox:

| Module (as shown in Hub) | Why |
|--------------------------|-----|
| ✅ **Microsoft Visual Studio Community 2022** (or your IDE) | C# editing — skip if already installed |
| ✅ **Windows Build Support** — include **IL2CPP** if offered as a sub-option | Windows players + IL2CPP scripting backend |
| ✅ **Mac Build Support** — install the option Hub offers (**IL2CPP** and/or **Mono**) | Cross-compile Mac builds from Windows |
| ✅ **Linux Build Support (IL2CPP)** | Linux / Steam Deck-style targets |
| ✅ **Documentation** | Offline manual |

4. Click **Install** — 20-60 minutes depending on your internet

   To add modules later: **Unity Hub → Installs** → **⋮** (or gear) beside your Unity 6 editor → **Add modules**.

5. **IL2CPP and Visual Studio (Windows):** IL2CPP needs a C++ toolchain. If builds fail with missing compiler errors, open **Visual Studio Installer** → modify your VS install → enable workload **Desktop development with C++** (includes MSVC and Windows SDK).

> **IL2CPP vs Mono:** IL2CPP compiles C# to C++ before building — faster runtime performance. Mono is an interpreter — faster to build, slower to run. This guide standardizes on **IL2CPP** in Player Settings.

---

## Part 3: Create the Unity Project

### Step 3.1 — Create a New Project

1. In Unity Hub → **Projects** → **New Project**
2. You'll see a grid of **templates** — these are starter configurations

   > **What is a template?** A pre-configured Unity project. The template we want sets up URP (our render pipeline) automatically so we don't have to wire it up manually.

3. Select **Universal 3D** — this is the URP template for Unity 6. (Hub wording can vary; avoid **3D (Built-in Render Pipeline)** or any template that says **Built-in** only — that is the legacy renderer.)
4. Set:
   - **Project name:** `ChibiValley`
   - **Location:** `C:\Users\Andrew\Documents\Github\game`
5. Click **Create project** — Unity opens for the first time; first-time shader compile often takes **2–5 minutes**

### Step 3.2 — Understanding the Unity Editor Layout

When Unity opens, you're looking at the **Editor**. Unity 6 uses the same overall layout as recent versions; panel names below are what docs and tutorials use:

```
┌─────────────────────────────────────────────────────────────┐
│  Menu Bar                                                    │
├──────────────┬───────────────────────────┬──────────────────┤
│              │                           │                  │
│  Hierarchy   │       Scene View          │    Inspector     │
│              │                           │                  │
│  Every object│  Your 3D viewport.        │  The properties  │
│  in the      │  Click and drag to        │  panel for       │
│  current     │  navigate. This is        │  whatever you've │
│  scene is    │  NOT what the player      │  selected.       │
│  listed here │  sees — it's your         │                  │
│  like a DOM. │  editor view.             │  Think of it     │
│              │                           │  like DevTools   │
│              ├───────────────────────────┤  for a selected  │
│              │                           │  object.         │
│              │       Game View           │                  │
│              │                           │                  │
│              │  What the player          │                  │
│              │  actually sees.           │                  │
│              │  Press ▶ Play to          │                  │
│              │  run the game here.       │                  │
├──────────────┴───────────────────────────┴──────────────────┤
│  Project window               │  Console                    │
│                               │                             │
│  Your file system.            │  Errors, warnings, and      │
│  Everything under Assets/     │  Debug.Log() output.        │
│  shows here. Drag files       │  Check this constantly —    │
│  from here into the Scene.    │  red = broken, yellow =     │
│                               │  warning, white = info.     │
└───────────────────────────────┴─────────────────────────────┘
```

> **Key mental model:** Unity is component-based. Every object in your game (called a **GameObject**) is an empty container. You attach **Components** to it to give it behavior — a Renderer component makes it visible, a Collider component makes it solid, a script component makes it do things. There's no inheritance hierarchy like you'd write in code — you compose behavior by stacking components.

### Step 3.3 — Verify URP Is Active

1. In the menu bar: **Edit → Project Settings**
   > Project Settings is your project’s engine-level config. Unity 6 uses a **search box** at the top — you can type `graphics` or `urp` to jump.

2. Click **Graphics** in the left list
3. Under **Scriptable Render Pipeline Settings**, the asset field should reference a URP pipeline asset (e.g. **`PC_RPAsset`**) — not **None**
4. Optionally expand **Pipeline Specific Settings** → **URP** — that is the in-editor home for **URP Global Settings** (linked to `UniversalRenderPipelineGlobalSettings`). Seeing this section means URP is wired for Unity 6.
5. If the pipeline slot is **None**, recreate the project with the **Universal 3D** template
6. Close Project Settings (**X** on that window, not the whole editor)

---

## Part 4: Configure Project Settings

These live in **Edit → Project Settings**. Open it once and use the left categories (and the **search** field if you cannot find a name).

Unity 6 often nests options in **foldouts** — if you do not see a property, expand **Settings for …**, **Other Settings**, **Resolution and Presentation**, etc.

### Step 4.1 — Player Settings

Click **Player** in the left list. Use the **platform icons** at the top (Windows, Mac, Linux, …) when a setting is per-platform — the guide below calls out where that matters.

**Identification** (usually shared):

- **Company Name:** your name
- **Product Name:** `ChibiValley`
- **Version:** `0.1.0`

Expand **Other Settings** (and scroll within it):

- **Color Space:** **Linear** (if it still says Gamma, switch it)
  > Unity warns that switching later is expensive — confirm **Switch to Linear**. Linear color space keeps lighting math consistent; Gamma is legacy.
- **API Compatibility Level:** `.NET Standard 2.1` (or the closest option Hub offers)
- **Scripting Backend:** **IL2CPP** (per platform where the choice exists — set for **Windows**, **Mac**, and **Linux** standalone)

### Step 4.2 — Set Your Code Editor

**Edit → Preferences** (Windows) — per-machine, not stored in the project. On macOS the menu is **Unity → Settings…** or **Unity → Preferences**.

1. Open **External Tools**
2. **External Script Editor:** **Visual Studio 2022** (or **Visual Studio Code** if you use that — point to the executable)
3. Click **Regenerate project files**
   > Regenerates `.csproj` / solution files so your IDE gets Unity API completions. Your `.gitignore` may exclude `*.csproj`; that is normal — Unity recreates them locally.

### Step 4.3 — Configure the URP Asset

Unity 6 splits URP into two ideas — easy to mix up:

| Asset / place | What it is |
|---------------|------------|
| **`UniversalRenderPipelineGlobalSettings`** (under `Assets/Settings`) | Project-wide URP defaults. Selecting it often **sends you to Edit → Project Settings → Graphics** — that is normal. You will **not** see MSAA or HDR there. |
| **Pipeline asset** (this project: **`PC_RPAsset`**, **`Mobile_RPAsset`**) | Per-quality render settings — **this** is where **MSAA** and **HDR** live. |

**Open the pipeline asset (MSAA + HDR):**

1. In the **Project** window: `Assets → Settings`
2. Click **`PC_RPAsset`** (desktop / default quality). *Do not use `UniversalRenderPipelineGlobalSettings` for these toggles.*
3. In the **Inspector**, expand **Quality** (section headers group the fields). Set:
   - **Anti Aliasing (MSAA):** `Disabled` — MSAA smooths edges, which fights the pixelated PS1 look
   - **HDR:** enable — required for bloom and similar effects

**Alternative:** **Edit → Project Settings → Graphics** → under **Scriptable Render Pipeline Settings**, the assigned asset is your active pipeline (often `PC_RPAsset`). Click that reference to open the same asset in the Inspector.

**Post-processing:** Unity 6’s URP Asset no longer uses a single **Post Processing: On/Off** checkbox like older tutorials. Effects run when **HDR** is on, the **Main Camera** has **Post Processing** enabled (on the **Universal Additional Camera Data** component), and you use **Volume** + **Volume Profile** overrides (bloom, etc.). The template is already set up for volumes; focus on **HDR** and camera **Post Processing** when you add effects later.

### Step 4.4 — Disable Texture Smoothing Globally

1. **Edit → Project Settings → Quality**
2. At the top you’ll see **named quality levels** (e.g. **PC**, **Mobile**). **Select each level you plan to ship with** (at minimum the default / active one).
3. In that level’s settings, set **Anisotropic Textures** to **Disabled**
   > Anisotropic filtering makes textures look smoother at glancing angles. For a crunchy pixel look, turn it off on every quality tier you use.

---

## Part 5: Set Up Folder Structure

In Unity, **everything your game uses must live inside the `Assets/` folder** — Unity doesn't know about files outside it. The **Project** window is your live view of `Assets/`.

> **Why the underscore on `_Project`?** Unity sorts folders alphabetically. The underscore forces `_Project` to the top, keeping your work above imported third-party packages which clutter the view.

### Step 5.1 — Create Folders

In the **Project** window, right-click **Assets** → **Create → Folder**. Create this structure:

```
Assets/
├── _Project/
│   ├── Art/
│   │   ├── Characters/
│   │   │   ├── Player/
│   │   │   └── NPCs/
│   │   ├── Animals/
│   │   ├── Environment/
│   │   │   ├── Farm/
│   │   │   ├── Nature/
│   │   │   └── Buildings/
│   │   ├── UI/
│   │   ├── Textures/
│   │   ├── Materials/
│   │   └── Shaders/
│   ├── Audio/
│   │   ├── Music/
│   │   └── SFX/
│   ├── Data/
│   │   ├── Crops/
│   │   ├── Items/
│   │   ├── Animals/
│   │   ├── NPCs/
│   │   └── Skills/
│   ├── Prefabs/
│   │   ├── Characters/
│   │   ├── Animals/
│   │   ├── Environment/
│   │   ├── Items/
│   │   └── UI/
│   ├── Scenes/
│   │   ├── _Test/
│   │   └── Farm/
│   ├── Scripts/
│   │   ├── Core/
│   │   ├── Farming/
│   │   ├── Animals/
│   │   ├── Player/
│   │   ├── NPCs/
│   │   ├── UI/
│   │   ├── World/
│   │   └── Utilities/
│   └── Settings/
├── Plugins/
├── StreamingAssets/
│   ├── Crops/
│   ├── Items/
│   ├── Animals/
│   ├── NPCs/
│   └── Skills/
└── ThirdParty/
    ├── Synty/
    └── Shaders/
```

> **What is `StreamingAssets`?** This is a special Unity folder — anything inside it gets copied to the build as-is and is readable at runtime on all platforms. This is where our JSON game data files live (crop stats, NPC data, skill trees, etc.). It's Unity's equivalent of a `public/` or `static/` folder.

> **What is a Prefab?** A Prefab is a saved, reusable GameObject template. If you make a Pig GameObject with all its components and settings, you save it as a Prefab. Then you can drag 10 copies into your scene and they all share the same base — change the Prefab and all instances update. Think of it like a React component.

### Step 5.2 — Move the Default Scene

Unity created a scene called `SampleScene`. A **Scene** is a level or screen — it holds the GameObjects for that area (main menu, farm zone, etc.).

1. In the **Project** window, find **`Assets/Scenes/SampleScene`** (Universal 3D template path; if yours differs, search the Project search bar for `SampleScene`)
2. Drag the asset into **`Assets/_Project/Scenes/Farm`** (create the `Farm` folder if you have not yet)
3. Right-click the scene asset → **Rename** → `Farm`

---

## Part 6: Git Setup

```bash
cd C:\Users\Andrew\Documents\Github\game\ChibiValley
git init
git lfs install
```

### .gitignore

Create `C:\Users\Andrew\Documents\Github\game\ChibiValley\.gitignore`:

```gitignore
# Unity generated — never commit these
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
[Uu]ser[Ss]ettings/

# Unity 6 / editor captures (optional but common)
[Rr]ecordings/
[Mm]emoryCaptures/

# Crash reports
sysinfo.txt

# Builds
*.apk
*.aab
*.unitypackage
*.app

# IDEs
.idea/
.vs/
*.sln
*.csproj
*.suo
*.user

# OS
.DS_Store
Thumbs.db

# Purchased assets — reinstall from Asset Store, don't commit
Assets/ThirdParty/Synty/
```

> **Why ignore `Library/`?** The Library folder is Unity's compiled/cached version of your assets — it's regenerated automatically from source files. It's gigabytes in size and changes constantly. Committing it would make your repo enormous and cause constant merge conflicts.

### .gitattributes (Git LFS)

Create `C:\Users\Andrew\Documents\Github\game\ChibiValley\.gitattributes`:

```gitattributes
# Unity
*.unity filter=lfs diff=lfs merge=lfs -text
*.prefab filter=lfs diff=lfs merge=lfs -text
*.asset filter=lfs diff=lfs merge=lfs -text
*.meta filter=lfs diff=lfs merge=lfs -text

# 3D Models
*.fbx filter=lfs diff=lfs merge=lfs -text
*.FBX filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text
*.blend filter=lfs diff=lfs merge=lfs -text
*.glb filter=lfs diff=lfs merge=lfs -text
*.gltf filter=lfs diff=lfs merge=lfs -text

# Textures
*.png filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.hdr filter=lfs diff=lfs merge=lfs -text

# Audio
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.ogg filter=lfs diff=lfs merge=lfs -text
*.aiff filter=lfs diff=lfs merge=lfs -text
*.flac filter=lfs diff=lfs merge=lfs -text

# Video
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.mov filter=lfs diff=lfs merge=lfs -text
```

### Initial Commit and Push

```bash
git add .
git commit -m "Initial project setup — Unity 6 URP, ChibiValley"

# Create repo on GitHub (private), then:
git remote add origin https://github.com/andyruwruw/chibi-valley.git
git branch -M main
git push -u origin main
```

---

## Part 7: Configure Build Targets

### Open Build Profiles

**File → Build Profiles**

> **What is a build?** Compiling your Unity project into a standalone executable for a target platform. You develop on Windows but can output binaries for Mac and Linux too (cross-compilation).

In Unity 6, **File → Build Settings** was removed. **Build Profiles** is the replacement: it manages **platforms**, **scene lists**, **Player Settings overrides**, and **Build / Build and Run**.

The left side has **Platforms** (shared defaults per installed platform) and optional **custom Build Profile** assets for specialized configs — follow **Platforms** until you need extras.

### Add Your Scene

1. **File → Build Profiles**
2. Select **Platforms** in the left sidebar
3. Open **Scene List** (Unity’s manual path: **File → Build Profiles → Platforms → Scene List**)
4. Click **Add Open Scenes** (with `Farm` open) — `Farm` should appear with its checkbox enabled
5. Uncheck a scene to exclude it from builds without deleting it from the list; drag rows to change **load order**

### Windows Build

Still under **Platforms**, select **PC, Mac & Linux Standalone** → **Windows** (exact labels depend on Hub modules; you want the **Windows** standalone target):

- Set **Target Platform** / **Architecture** to **Windows** and **64-bit** (often shown as **x86_64** or **Intel 64-bit**)
- Use **Switch Platform** if Unity shows it and the active target is wrong — expect a **reimport** (can take minutes the first time)

Use **Build** or **Build and Run** from the same window when you are ready (see Part 10).

### Player Settings Per Platform

**Edit → Project Settings → Player** → use the **platform / OS icons** along the top to switch **Windows**, **Mac**, and **Linux** standalone. Screen size and fullscreen options sit under **Resolution and Presentation** (expand the foldout if you do not see them).

**All platforms:**
```
Scripting Backend:        IL2CPP
Api Compatibility Level:  .NET Standard 2.1
Architecture:             x86_64
```

**Windows:**
```
Default Screen Width:   1920
Default Screen Height:  1080
Fullscreen Mode:        Windowed
Resizable Window:       ✅
```

**Mac:**
```
Default Screen Width:   1920
Default Screen Height:  1080
Fullscreen Mode:        Windowed
Resizable Window:       ✅
```

> Unity 6 sometimes labels this **Full Screen Mode** instead of **Fullscreen Mode** — same setting.

> Mac builds cross-compiled from Windows work for testing but **cannot be code-signed on Windows**. For App Store distribution or Gatekeeper compliance you'll need a Mac or a GitHub Actions Mac runner. Not a problem until you're near release.

**Linux (Steam Deck):**
```
Default Screen Width:   1280
Default Screen Height:  800
Fullscreen Mode:        Windowed
Resizable Window:       ✅
```

> Steam Deck's native resolution is 1280×800. It can also run Windows builds via Proton (Valve's compatibility layer) so your Windows build often "just works" on Steam Deck — but a native Linux build gets you Steam Deck Verified status and better performance.

---

## Part 8: Install Core Packages

Unity’s **Package Manager** adds first-party and third-party engine features (like npm for the editor).

### Open Package Manager

**Window → Package Manager**

Use the **dropdown** at the top left:

- **Unity Registry** — official Unity packages
- **My Assets** — Asset Store purchases
- **In Project** — everything already installed

### Install via Package Manager (Unity Registry)

Set the dropdown to **Unity Registry**. Search each name, select the package, then **Install**:

| Package | Purpose |
|---------|---------|
| **Input System** | Modern input — keyboard, gamepad, Steam Deck controls |
| **ProBuilder** | Greybox / in-editor geometry |
| **Animation Rigging** | Procedural animation constraints |
| **Cinemachine** | Advanced cameras |

**Input System:** A dialog may ask to enable the new stack — accept and let Unity **restart** if it offers. Afterward, confirm **Edit → Project Settings → Player → Other Settings → Active Input Handling** is **Input System Package (New)** or **Both** (use **Both** if you still need legacy `Input` during migration).

### Install Yarn Spinner (via Git URL)

Yarn Spinner isn't in the Unity Registry — install it directly from GitHub:

1. In Package Manager, click **+** (top left) → **Add package from git URL**
2. Paste:
   ```
   https://github.com/YarnSpinnerTool/YarnSpinner-Unity.git#current
   ```
3. Click **Add** and wait for import

---

## Part 9: Install FMOD

FMOD is a separate application that integrates with Unity. It handles all audio — music, SFX, adaptive audio.

### Step 9.1 — Install FMOD Studio

1. Go to https://www.fmod.com/download — create a free account
2. Download **FMOD Studio** (the desktop authoring app) — install it
3. Download the **FMOD Unity Integration** `.zip`

### Step 9.2 — Import FMOD into Unity

1. Unzip the FMOD Unity Integration download
2. Locate the `.unitypackage` file inside
3. Either:
   - **Assets → Import Package → Custom Package…** and choose that file, or  
   - **Double-click** the `.unitypackage` file in File Explorer (Windows) — it should open the import dialog in the running Unity editor

   > **`.unitypackage`** = a Unity asset bundle (prefabs, scripts, plugins). Import pulls copies into your `Assets/` tree.

4. In the import window, click **Import All**
5. A FMOD setup wizard will appear in Unity — follow it:
   - Create a new FMOD Studio project when prompted, name it `ChibiValley`
   - Link it to the FMOD Studio app you installed
   - Accept default settings for now

---

## Part 10: Verify Everything Works

### Checklist

- [ ] Unity 6 LTS (`6000.x`) opens without red errors in the **Console**
- [ ] **Edit → Project Settings → Graphics → Scriptable Render Pipeline Settings** references a URP asset (e.g. `PC_RPAsset`), not **None**
- [ ] **Edit → Project Settings → Player → Other Settings → Color Space** is **Linear**
- [ ] Double-clicking a `.cs` script in the **Project** window opens your chosen IDE (Visual Studio / VS Code)
- [ ] `git lfs ls-files` runs without error in the project directory (`ChibiValley`)
- [ ] GitHub repo exists and has your initial commit
- [ ] **File → Build Profiles → Platforms** shows Windows and Mac/Linux standalone without module errors
- [ ] **Window → Package Manager → In Project** lists: Input System, ProBuilder, Animation Rigging, Cinemachine, Yarn Spinner (after Part 8)
- [ ] **FMOD** menu appears in the menu bar (after Part 9)

### Test Build

1. **File → Build Profiles** → **Platforms** → **PC, Mac & Linux Standalone** → **Windows** → **Build** (avoid **Build and Run** until you want Unity to launch the exe)
2. Choose a folder outside the project — e.g. `C:\Builds\ChibiValley\Windows\`
3. Wait — a `.exe` will appear when done
4. Run it — you should see Unity's default URP grey scene with a camera
5. If it runs, your build pipeline works

---

## Part 11: Commit Configured State

```bash
git add .
git commit -m "Configure URP settings, folder structure, core packages"
git push
```

---

## Troubleshooting (Unity 6)

| Symptom | What to check |
|--------|----------------|
| No **File → Build Settings** | Normal — use **File → Build Profiles** and **Platforms → Scene List**. |
| **`UniversalRenderPipelineGlobalSettings`** opens **Graphics** only | Expected. MSAA / HDR are on **`PC_RPAsset`** (Project → `Assets/Settings`), not on global settings. |
| IL2CPP build fails, “missing compiler” / MSVC | Install **Visual Studio 2022** with **Desktop development with C++**; confirm **Windows Build Support (IL2CPP)** in Hub. |
| Input does nothing after Input System | **Player → Other Settings → Active Input Handling** = **Both** or **New**; scene needs **Player Input** or event wiring. |
| Package install errors / Git URL | Unity needs network access; for Yarn Spinner, confirm the `#current` branch still exists or use the URL from [Yarn Spinner docs](https://docs.yarnspinner.dev/). |

---

## What's Next

1. **Buy and import PS1 shader pack** from Unity Asset Store — apply globally before any art work
2. **Buy Synty environment packs** — POLYGON Farm, Nature, Adventure minimum
3. **Build the Farm scene** — terrain, grid, fixed camera placement
4. **Implement the time system** — the heartbeat everything else hooks into
5. **First playable** — player movement on the farm, day advances, nothing else

---

*Guide version: Unity 6 LTS (`6000.x`). Last updated: 2026-04-09.*
