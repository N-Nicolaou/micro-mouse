# Micro-Mouse Maze-Solving Robot 🤖

**Module:** 5ELEN020W Embedded Systems Project (WBL) — University of Westminster  
**Type:** Group Project (6 members)

An autonomous maze-solving robot capable of navigating an unknown 8×8 maze from start to finish with no prior knowledge of the layout. Built on the MBED platform with stepper motors, ultrasonic sensing, and IR sensors.

---

## Demo

> 📹 Video of the robot navigating the maze available on request.

---

## How It Works

The robot uses a **Flood Fill (Lee's Algorithm)** approach to navigate:

1. On startup, flood fill assigns every cell a distance value from the goal (cell [7][7])
2. At each cell, the robot reads its sensors to detect walls and updates the maze map
3. It picks the neighbouring cell with the lowest flood fill value (closest to goal)
4. As new walls are discovered, flood fill re-runs to recalculate optimal distances
5. The robot turns and moves into the chosen cell, updating its position and orientation
6. Repeat until the goal is reached

---

## Sensor Setup

| Sensor | Type | Purpose |
|--------|------|---------|
| Ultrasonic (HC-SR04) | `DigitalIn/Out` | Front wall detection — triggers pulse, measures echo time |
| Left IR Bus | `BusIn (p6,p7,p8)` | Left wall detection — reads 3-bit value |
| Right IR Bus | `BusIn (p9,p10,p11)` | Right wall detection — reads 3-bit value |

Wall detection thresholds: ultrasonic < 80mm = wall present; IR bus value == 7 = wall present.

---

## Wall Mapping

Walls are stored in an `8×8` integer array using **bitwise encoding**:

| Bit | Direction |
|-----|-----------|
| `1` | North |
| `2` | East |
| `4` | South |
| `8` | West |

When a wall is discovered, it is automatically **shared with the neighbouring cell** — e.g. if cell [3][3] has a North wall, cell [3][4] gets a South wall. This ensures the map stays consistent.

Perimeter walls are pre-loaded before the first run so the robot never tries to leave the maze boundary.

---

## Flood Fill Algorithm

Two flood fill functions handle navigation:

### `flood_fill_setup()`
Runs at startup ignoring walls — assigns every cell a BFS distance from the goal. This gives the robot an initial path to follow before any walls are known.

### `flood_fill_update()`
Re-runs after each wall discovery — respects known walls when propagating distances. Cells blocked by walls get higher values, naturally routing the robot around obstacles.

### `choose_direction()`
Compares the flood fill values of all accessible neighbours (no wall between current cell and neighbour) and picks the lowest — always moving closer to the goal.

---

## Motor Control

Both stepper motors are controlled via Step/Direction pins:

| Function | Behaviour |
|----------|-----------|
| `forwards()` | Both motors step forward (400 steps = 1 cell) |
| `turn_left()` | Left motor reverses, right steps forward |
| `turn_right()` | Right motor reverses, left steps forward |
| `u_turn()` | 800 steps of turn_right = 180° rotation |

Orientation is tracked as an integer (North=0, East=1, South=2, West=3). The number of right turns needed to face the target direction is calculated as `(target - current + 4) % 4`.

---

## File Structure

```
├── main.cpp          # Full robot firmware
└── Report.pdf        # Full technical report
```

---

## Tech Stack

| Tool | Usage |
|------|-------|
| MBED | Embedded platform & HAL |
| C++ | Firmware language |
| Fusion 360 | PCB schematic & chassis design |
| HC-SR04 | Ultrasonic distance sensor |
| Stepper Motors | Locomotion |
| IR Sensors | Left/right wall detection |

---

## Built With
- C++ on MBED
- University of Westminster Embedded Systems Lab

