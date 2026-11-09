# bus-buddies
Bus Buddies: Escape the City – GMT 458 Web GIS GeoGame project. A two-player, location-based, split-screen co-op escape game (HTML5 Canvas + JavaScript)
# 🚌 Bus Buddies: Escape the City

> **GMT 458 – Web GIS | GeoGame Project**
> Hacettepe University, Department of Geomatics Engineering

**Bus Buddies: Escape the City** is a top-down 2D **location-based co-op escape game** for two players sharing the same computer and keyboard. The city has been overrun by monsters. The two players must complete tasks at different points across the city and **board the last escape bus together** to get out alive.

Every mechanic in the game is driven by **location**: triggering tasks, monster pursuit, collisions and the win condition are all determined by the players' coordinates on the city map and their distances to each other and to targets.

---

## 📑 Table of Contents

1. [Game Concept](#1-game-concept)
2. [Controls](#2-controls)
3. [Gameplay and Tasks](#3-gameplay-and-tasks)
4. [Core Mechanics](#4-core-mechanics)
5. [Map and Location System (GIS Concepts)](#5-map-and-location-system-gis-concepts)
6. [Technologies](#6-technologies)
7. [How It Works](#7-how-it-works)
8. [Installation and Running](#8-installation-and-running)
9. [Roadmap](#9-roadmap)
10. [Developer](#10-developer)

---

## 1. Game Concept

- **Genre:** 2-player local co-op, top-down escape game
- **Platform:** Web browser (no installation or internet connection required)
- **Goal:** Share the tasks, avoid the monsters and escape the city by bus before time runs out
- **Screen:** Split screen – each player has their own camera and can move independently across the map

---

## 2. Controls

| Action | 🔴 Player 1 (Red) | 🔵 Player 2 (Blue) |
|---|---|---|
| Move | `W` `A` `S` `D` | Arrow keys `↑` `←` `↓` `→` |
| Interact | `E` | `Enter` |
| Sprint | `Left Shift` | `Right Shift` |

The **left half** of the screen belongs to Player 1 and the **right half** to Player 2.

---

## 3. Gameplay and Tasks

Tasks must be completed in order:

1. 🎫 **Ticket machine** – The red player goes to the ticket machine and activates it.
2. ⚡ **Power terminal** – The blue player switches on the power terminal.
3. 🚪 **Gate buttons** – Both players stand next to their own buttons **at the same time** to open the gate.
4. 🚌 **Escape bus** – The bus becomes ready at the central stop. Each player walks up to it and presses their interact key to board.

Once both players are on board, the bus drives to the escape point and **the team wins**.

**Lose conditions:**
- ⏱️ The 6-minute timer runs out
- ❤️ Either player loses all 3 lives

---

## 4. Core Mechanics

### Characters and camera
Characters are 2D figures with a head, face, clothing, arms and legs, and play a simple walking animation while moving. Each half of the screen has its own camera, health display and minimap.

### Monsters and survival
- Monsters **patrol** the city.
- When a player enters a monster's detection range, the monster starts **chasing** them.
- Contact costs the player a life; a short **invincibility window** prevents taking repeated hits instantly.
- Players can sprint to escape. Monsters cannot pass through buildings.

### Tasks and cooperation
Task objects sit at fixed coordinates on the map. The gate task requires both players to be at **different locations simultaneously**, which forms the cooperative core of the game.

### Timer and lives
- **6-minute** countdown
- Each player starts with **3 lives**
- When time or lives run out, a replay screen is shown

---

## 5. Map and Location System (GIS Concepts)

The game takes place on a top-down city map of roughly **3000 × 1900 units**. The map consists of roads, intersections, sidewalks, buildings, trees and task zones. Every character, monster, task point and the bus has an **`(x, y)` coordinate** on this map.

The game world is built on a **planar (Cartesian) coordinate system**, which allows distances to be computed directly, as in a projected local coordinate system. The spatial operations used in the game and their GIS equivalents:

| Game mechanic | Spatial operation | GIS equivalent |
|---|---|---|
| Task / bus interaction | Euclidean distance between player and object compared with a threshold | **Proximity analysis / Buffer** |
| Blocking movement through buildings | New position tested for intersection with building rectangles and world bounds | **Point-in-polygon / Intersection test** |
| Monster pursuit | Nearest player within the detection radius is found | **Nearest neighbor analysis** |
| Simultaneous gate task | Distances of two players to two separate targets checked at the same time | **Multi-location condition / Spatial query** |
| Split-screen camera | World coordinates → screen coordinates (translation) | **Coordinate transformation** |
| Minimap | World coordinates reduced to a smaller scale | **Scale transformation / Generalization** |

> **Note:** In the current prototype, coordinates are planar game coordinates rather than real latitude/longitude, so the game runs fully offline without GPS or a map service. The move to real geographic data is described in the [Roadmap](#9-roadmap).

---

## 6. Technologies

| Technology | Purpose |
|---|---|
| **HTML5** | Game page, start and result screens |
| **CSS3** | Page layout, task and status indicators |
| **JavaScript** | Controls, task logic, monster AI, timer, win/lose rules |
| **Canvas 2D API** | Rendering the city, characters, monsters, bus and both camera views |
| **JSON / JS objects** | Task locations, obstacles, character states and game parameters |
| **GitHub Pages** | Publishing the game on the web |

The prototype uses no external game engine; the map and characters are drawn directly on the Canvas with JavaScript.

---

## 7. How It Works

1. **The game world is built** – Road and building coordinates, task points, the bus stop, characters and monsters are defined.
2. **Keyboard input is read** – WASD and arrow keys are tracked simultaneously; each character's position is updated independently.
3. **The game loop runs** – Using `requestAnimationFrame`, movement, timer, monster behavior and animations are computed based on elapsed time (delta time).
4. **Monsters are updated** – A monster that detects a nearby player moves toward them while others keep patrolling; collision and damage checks are applied.
5. **Task conditions are checked** – The ticket, power and simultaneous button tasks are verified; once all three are complete, the bus opens for boarding.
6. **Two cameras are rendered** – The canvas is split in two; each half draws the same world offset by the corresponding player's position. Minimaps and health displays are added.
7. **The outcome is decided** – When both players board the bus, the escape animation plays and the win screen appears; if time or lives run out, the lose screen opens.

---

## 8. Installation and Running

No installation is required.

```bash
git clone https://github.com/ZeynepSude53/bus-buddies.git
cd bus-buddies
```

Then simply open `index.html` in your browser.

**Play online:** the version published on GitHub Pages:
`https://zeynepsude53.github.io/bus-buddies/`

---

## 9. Roadmap

- [x] Split screen with two independent cameras
- [x] Task chain and simultaneous gate task
- [x] Monster patrol/chase AI
- [x] Timer, lives and win/lose system
- [ ] **Real geographic data:** Generating the city map from OpenStreetMap data (e.g., roads and building polygons in GeoJSON) and transforming coordinates from a real projection (e.g., UTM) into game coordinates
- [ ] Selectable maps for different cities/regions
- [ ] Sound effects and music
- [ ] Difficulty levels (number of monsters, time limit)
- [ ] More detailed sprite animations (migration to Phaser + Tiled)

---

## 10. Developer

**GitHub:** [@ZeynepSude53](https://github.com/ZeynepSude53)

This project was developed as a GeoGame project for the **GMT 458 – Web GIS** course at Hacettepe University.
