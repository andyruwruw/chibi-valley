# ChibiValley — Project Setup Guide

Complete step-by-step setup for Windows development, targeting Windows, Mac, and Steam Deck (Linux).

**Assumed knowledge:** Comfortable with Git, GitHub, command line, and installing software.  
**Not assumed:** Any Unity experience whatsoever.

**Estimated time: 2-3 hours** (mostly downloads)

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
3. On the modules screen, check these — everything else can stay unchecked:

| Module | Why |
|--------|-----|
| ✅ Microsoft Visual Studio Community 2022 | Your IDE — skip if already installed |
| ✅ Windows Build Support (IL2CPP) | Build for Windows |
| ✅ Mac Build Support (Mono) | Build for Mac |
| ✅ Linux Build Support (IL2CPP) | Build for Steam Deck |
| ✅ Documentation | Offline docs |

4. Click **Install** — 20-60 minutes depending on your internet

> **IL2CPP vs Mono:** IL2CPP compiles C# to C++ before building — faster runtime performance. Mono is an interpreter — faster to build, slower to run. We use IL2CPP for shipping platforms.

---

## Part 3: Create the Unity Project

### Step 3.1 — Create a New Project

1. In Unity Hub → **Projects** → **New Project**
2. You'll see a grid of **templates** — these are starter configurations

   > **What is a template?** A pre-configured Unity project. The template we want sets up URP (our render pipeline) automatically so we don't have to wire it up manually.

3. Select **Universal 3D** — this is the URP template. Do NOT pick "3D (Built-in Render Pipeline)" — that's the legacy renderer.
4. Set:
   - **Project name:** `ChibiValley`
   - **Location:** `C:\Users\Andrew\Documents\Github\game`
5. Click **Create project** — Unity will open for the first time, taking 2-5 minutes to compile shaders

### Step 3.2 — Understanding the Unity Editor Layout

When Unity opens, you're looking at the Editor. Every panel has a name — you'll hear these terms constantly:

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
│  Project Panel                │  Console                    │
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
   > Project Settings is like your app's config file — one window for all engine-level settings.

2. Click **Graphics** in the left list
3. Look for **Scriptable Render Pipeline Settings** — it should show a URP Asset (not "None")
4. If it says "None", something went wrong — delete the project and recreate it with the Universal 3D template
5. Close Project Settings (X on the window, not the whole editor)

---

## Part 4: Configure Project Settings

All of these live in **Edit → Project Settings**. Open it and leave it open for this whole section.

### Step 4.1 — Player Settings

Click **Player** in the left list:

- **Company Name:** your name
- **Product Name:** `ChibiValley`
- **Version:** `0.1.0`

Scroll down to **Other Settings:**

- **Color Space:** change from Gamma to **Linear**
  > Unity will warn you this is expensive to switch later — click "Switch to Linear". Linear color space means lighting math is physically accurate. Gamma is legacy.
- **API Compatibility Level:** `.NET Standard 2.1`
- **Scripting Backend:** `IL2CPP`

### Step 4.2 — Set Your Code Editor

**Edit → Preferences** (not Project Settings — this one is per-machine, not per-project):

1. Click **External Tools**
2. **External Script Editor:** Visual Studio 2022
3. Click **Regenerate project files**
   > This creates `.csproj` files so Visual Studio understands Unity's structure and gives you autocomplete for Unity APIs.

### Step 4.3 — Configure the URP Asset

Unity 6 splits URP into two ideas — easy to mix up:

| Asset / place | What it is |
|---------------|------------|
| **`UniversalRenderPipelineGlobalSettings`** (under `Assets/Settings`) | Project-wide URP defaults. Selecting it often **sends you to Edit → Project Settings → Graphics** — that is normal. You will **not** see MSAA or HDR there. |
| **Pipeline asset** (this project: **`PC_RPAsset`**, **`Mobile_RPAsset`**) | Per-quality render settings — **this** is where **MSAA** and **HDR** live. |

**Open the pipeline asset (MSAA + HDR):**

1. In the **Project** panel: `Assets → Settings`
2. Click **`PC_RPAsset`** (desktop / default quality). *Do not use `UniversalRenderPipelineGlobalSettings` for these toggles.*
3. In the **Inspector**, expand **Quality** (section headers group the fields). Set:
   - **Anti Aliasing (MSAA):** `Disabled` — MSAA smooths edges, which fights the pixelated PS1 look
   - **HDR:** enable — required for bloom and similar effects

**Alternative:** **Edit → Project Settings → Graphics** → under **Scriptable Render Pipeline Settings**, the assigned asset is your active pipeline (often `PC_RPAsset`). Click that reference to open the same asset in the Inspector.

**Post-processing:** Unity 6’s URP Asset no longer uses a single **Post Processing: On/Off** checkbox like older tutorials. Effects run when **HDR** is on, the **Main Camera** has **Post Processing** enabled (on the **Universal Additional Camera Data** component), and you use **Volume** + **Volume Profile** overrides (bloom, etc.). The template is already set up for volumes; focus on **HDR** and camera **Post Processing** when you add effects later.

### Step 4.4 — Disable Texture Smoothing Globally

1. **Edit → Project Settings → Quality**
2. Set **Anisotropic Textures:** `Disabled`
   > Anisotropic filtering makes textures look smoother at angles. We want them to look crunchy and pixelated — disable it.

---

## Part 5: Set Up Folder Structure

In Unity, **everything your game uses must live inside the `Assets/` folder** — Unity doesn't know about files outside it. The Project panel IS your `Assets/` folder.

> **Why the underscore on `_Project`?** Unity sorts folders alphabetically. The underscore forces `_Project` to the top, keeping your work above imported third-party packages which clutter the view.

### Step 5.1 — Create Folders

In the **Project panel**, right-click on **Assets** → **Create → Folder**. Create this structure:

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

Unity created a scene called `SampleScene`. A **Scene** in Unity is like a level or screen — it contains all the GameObjects for that area. The Main Menu is a scene, the Farm is a scene, each town zone is a scene.

1. In the Project panel, find `Assets → Scenes → SampleScene`
2. Drag it into `Assets → _Project → Scenes → Farm`
3. Right-click it → **Rename** → `Farm`

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
git remote add origin https://github.com/YOUR_USERNAME/ChibiValley.git
git branch -M main
git push -u origin main
```

---

## Part 7: Configure Build Targets

### Open Build Settings

**File → Build Settings**

> **What is a build?** Compiling your Unity project into a standalone executable for a target platform. You develop on Windows but can output binaries for Mac and Linux too (cross-compilation).

### Add Your Scene

1. Click **Add Open Scenes** — `Farm` should appear in the list with a checkmark
2. Every scene you want accessible in the game must be listed here

### Windows Build

`PC, Mac & Linux Standalone` is already selected in the Platform list:
- **Target Platform:** Windows
- **Architecture:** x86_64
- Click **Switch Platform** if it isn't already active (Unity reimports assets for the target — takes a few minutes)

### Player Settings Per Platform

**Edit → Project Settings → Player**, then use the platform tabs at the top (little icons for each OS):

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

Unity has a package manager for first-party and community packages — like npm but for engine features.

### Open Package Manager

**Window → Package Manager**

> You'll see two registries: **Unity Registry** (official packages) and **In Project** (what's installed). Switch between them using the dropdown at the top left.

### Install via Package Manager (Unity Registry)

Search for each of these, click it, then click **Install**:

| Package | Purpose |
|---------|---------|
| **Input System** | Modern input handling — keyboard, controller, Steam Deck gamepad |
| **ProBuilder** | Build geometry directly in the Unity editor |
| **Animation Rigging** | Procedural animation constraints for animals |
| **Cinemachine** | Advanced camera system |

> When you install **Input System**, Unity will show a dialog: *"Do you want to enable the new input system?"* — click **Yes**. Unity restarts. After restart: **Edit → Project Settings → Player → Other Settings → Active Input Handling** → set to **Both**.

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

1. Unzip the FMOD Unity Integration
2. Inside you'll find a `.unitypackage` file
3. In Unity: **Assets → Import Package → Custom Package** → select the `.unitypackage`

   > **What is a `.unitypackage`?** A zip of Unity assets and scripts — like an npm tarball. Double-clicking one in Explorer will open it in Unity automatically once Unity is installed.

4. A window appears listing everything in the package — click **Import All**
5. A FMOD setup wizard will appear in Unity — follow it:
   - Create a new FMOD Studio project when prompted, name it `ChibiValley`
   - Link it to the FMOD Studio app you installed
   - Accept default settings for now

---

## Part 10: Verify Everything Works

### Checklist

- [ ] Unity 6 LTS opens without red errors in the Console panel
- [ ] **Edit → Project Settings → Graphics** shows a URP asset (not "None")
- [ ] **Edit → Project Settings → Player → Other Settings → Color Space** shows "Linear"
- [ ] Double-clicking a `.cs` script in the Project panel opens Visual Studio 2022
- [ ] `git lfs ls-files` runs without error in the project directory
- [ ] GitHub repo exists and has your initial commit
- [ ] **File → Build Settings** shows Windows, and Linux/Mac are available without errors
- [ ] **Window → Package Manager → In Project** shows: Input System, ProBuilder, Animation Rigging, Cinemachine, Yarn Spinner
- [ ] FMOD menu appears in Unity's menu bar

### Test Build

1. **File → Build Settings → Build** (not Build and Run)
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

## What's Next

1. **Buy and import PS1 shader pack** from Unity Asset Store — apply globally before any art work
2. **Buy Synty environment packs** — POLYGON Farm, Nature, Adventure minimum
3. **Build the Farm scene** — terrain, grid, fixed camera placement
4. **Implement the time system** — the heartbeat everything else hooks into
5. **First playable** — player movement on the farm, day advances, nothing else

---

*Last updated: 2026-04-09*
