# 🚌 Bus Buddies 3D: Escape the City

**GMT 458 – Web GIS | GeoGame Project** · Hacettepe University, Department of Geomatics Engineering

Bus Buddies 3D is a two-player, split-screen, location-based **co-op platformer** inspired by *Fireboy & Watergirl*. Two players share one keyboard: 🔥 **Fire** and 💧 **Water** must work together across two levels to escape a monster-infested city, then a dark forest.

Every mechanic is driven by location: tasks, pressure plates, monster pursuit, collisions and win conditions are all computed from the players' coordinates and their distances to each other and to targets on the map.

▶ **Play online:** https://zeynepsude53.github.io/bus-buddies/

---

## 📑 Table of Contents
1. [Game Concept](#1-game-concept)
2. [Controls](#2-controls)
3. [Levels](#3-levels)
4. [Core Mechanics](#4-core-mechanics)
5. [Map and Location System (GIS Concepts)](#5-map-and-location-system-gis-concepts)
6. [Technologies](#6-technologies)
7. [Installation and Running](#7-installation-and-running)
8. [Roadmap](#8-roadmap)
9. [Developer](#9-developer)

---

## 1. Game Concept

| | |
|---|---|
| **Genre** | 2-player local co-op 3D platformer / escape game |
| **Platform** | Web browser (HTML5 + WebGL) |
| **Screen** | Split screen: each player has their own third-person camera, HUD and minimap |
| **Goal** | Complete the co-op tasks of each level and escape together before time runs out |

**Fire & Water rule**
- 🔥 **Fire** walks through lava and fire safely but falls back to the last safe point when touching water.
- 💧 **Water** swims through water safely but falls back when touching lava or fire.
- Each player collects 💎 gems of their own colour.

## 2. Controls

| Action | 🔥 Player 1 (Fire) | 💧 Player 2 (Water) |
|---|---|---|
| Move | W A S D | Arrow keys |
| Jump | Space | `.` (key left of Right Shift on TR keyboard) / Numpad 0 |
| Sprint (uses stamina) | Left Shift | Right Shift |
| Interact / revive partner (hold) | E | Enter |
| Attack | F | `Ç` / Numpad 1 |
| Use item | Q | `Ö` / Numpad 2 |
| Next inventory slot | R | `Ş` / Numpad 3 |
| Pause | Esc | Esc |

The left half of the screen belongs to Fire, the right half to Water. A step-by-step "How to play" tutorial (with Skip) is shown before the first game.

## 3. Levels

### Level 1 – The City 🏙️ (escape by bus)
| District | Co-op puzzle |
|---|---|
| ⚓ **Port** | Water wades through the flooded channel and holds a 🟦 plate → a bridge extends; Fire climbs the containers and takes the 🎫 ticket. |
| 🏗️ **Construction site** | Fire walks through lava and holds a 🟥 plate → an elevator rises; Water jumps across pillars, rides up and starts the ⚡ generator. |
| 🏭 **Factory** + 🌿 **Rooftop garden** | Fire climbs crates and a moving platform; Water jumps across terraces. Both buttons must be pressed **at the same time**. |
| 🚌 **Terminal** | The gate opens, the bus arrives, both players board. |

### Level 2 – The Dark Forest 🌲 (escape by helicopter)
| Area | Co-op puzzle |
|---|---|
| 🌊 **Waterfall** | Fire crosses the burning brush and holds a 🟥 plate → a bridge opens for Water, who climbs the cliff and starts the ⚙️ turbine. |
| 🌉 **River** | Water swims across and holds a 🟦 plate → the drawbridge lowers so Fire can cross. |
| 🌋 **Lava pit** | Water holds a 🟦 plate → stepping stones rise out of the lava; Fire climbs to the island and lights the 🔥 signal fire. |
| 🗼 **Watchtowers** | Fire hops lily pads, Water hops tree stumps; both tower buttons pressed simultaneously. |
| 🚁 **Helipad** | The helicopter lands, both players board. |

**Lose conditions:** time runs out, or both players are down at the same time.

## 4. Core Mechanics

- **3D platforming:** every obstacle is a box with a height. Players can jump onto containers, cars, crates, rocks, logs, scaffolding and terraces; sprint-jumping reaches further. Coyote time and jump buffering make jumps forgiving.
- **Anti-stuck collision:** movement out of an overlapping obstacle is always allowed, embedded entities are pushed to the nearest free point, and a watchdog rescues a player who is trapped for 2 seconds.
- **Stamina:** sprinting drains stamina; at zero the player is exhausted and slowed for a few seconds.
- **Monsters:** they appear after the first minute and become more numerous as tasks are completed. They ambush from behind buildings, are slower than a walking player, but lunge when close. They cannot jump, so high ground is safe. In Level 2 they are 🐊 **crocodiles**: tougher (4 hits), with a longer lunge, and fast in water.
- **Combat and items:** 🏏 bat (big knockback), 🔪 knife (high damage), 👊 fist; 🥤 energy drink, 💨 smoke bomb, 🛡️ shield, ❤️ first aid.
- **Revive:** a player with no lives goes down; the partner holds the interact key nearby to revive them.
- **Guidance:** a waypoint arrow at each player's feet, a task checklist, minimap and beacons.
- **Scoring:** 1–3 stars per level based on escaping, gems collected and remaining time.

## 5. Map and Location System (GIS Concepts)

Each level is a 4000 × 2800 unit planar map. Coordinates are planar (Cartesian), as in a projected local coordinate system, so distances are computed directly. The game logic runs on the (x, z) plane; y is height only.

| Game mechanic | Spatial operation | GIS equivalent |
|---|---|---|
| Task, item, gem and boarding interactions | Euclidean distance to a point compared with a threshold | Proximity analysis / Buffer |
| Pressure plates and the simultaneous button task | Distances of two players to two separate targets checked at the same time | Multi-location condition / Spatial query |
| Water / lava pools, districts | Point inside a rectangle test | Point-in-polygon |
| Blocking movement, standing on platforms | Circle vs rectangle intersection with height | Intersection test / 2.5D analysis |
| Buildings, containers, terraces | Footprint polygons extruded to a height | Extrusion (CityGML LoD1, 2.5D) |
| Fast collision lookup | Uniform grid of 200-unit cells | Spatial index |
| Monster detection | Nearest player within a radius | Nearest neighbour analysis |
| Monster patrol | Movement along a road / trail graph | Network analysis |
| Ambush spawning, see-through buildings | Line segment vs rectangle (camera → player) | Line-of-sight / visibility analysis |
| Smoke bomb | All monsters inside a radius are affected | Buffer + selection by location |
| Split-screen cameras | World → screen coordinates | Coordinate transformation |
| Minimap | World coordinates reduced to a small scale | Scale transformation / Generalization |

> Note: The current maps use planar game coordinates rather than real latitude/longitude, so the game runs without GPS or a map service. Real geographic data is on the roadmap.

## 6. Technologies

| Technology | Purpose |
|---|---|
| HTML5 / CSS3 | Page, HUD, menus and tutorial screens |
| JavaScript (ES modules) | Game loop, physics, AI, task logic, level system |
| Three.js (WebGL) | 3D rendering, shadows, split-screen viewports |
| Web Audio API | Synthesized sound effects (no audio files) |
| GitHub Pages | Publishing the game |

The whole game is a single `index.html` file. Three.js is loaded from a CDN (unpkg), so an internet connection is needed the first time the page loads.

## 7. Installation and Running

```bash
git clone https://github.com/ZeynepSude53/bus-buddies.git
cd bus-buddies
```
Then open `index.html` in a modern browser (Chrome, Edge or Firefox). No build step is required.

## 8. Roadmap

- [x] Split screen with two independent cameras
- [x] 3D city with extruded buildings
- [x] Fire & Water rules, gems, pressure plates, bridges, elevators, moving platforms
- [x] Platforming (jump, sprint-jump, standable obstacles) with anti-stuck collision
- [x] Monster AI (ambush, patrol, chase, lunge) and crocodiles
- [x] Weapons, items, stamina, revive system
- [x] Tutorial pages, pause menu, waypoint arrow, star rating
- [x] Level system: Level 1 City, Level 2 Forest
- [ ] Real geographic data: generate a level from OpenStreetMap (roads and building polygons as GeoJSON), transforming coordinates from a real projection (e.g. UTM) into game coordinates
- [ ] More levels and difficulty settings
- [ ] Music

## 9. Developer

GitHub: [@ZeynepSude53](https://github.com/ZeynepSude53)

Developed as a GeoGame project for the GMT 458 – Web GIS course at Hacettepe University.
