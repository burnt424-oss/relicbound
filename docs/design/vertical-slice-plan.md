# First Playable Vertical Slice Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an Android-playable vertical slice proving top-down exploration, seamless transition into one tactical battle, initiative-based Move + Action combat, touch controls, victory, and return to exploration.

**Architecture:** Use Godot 4.7.2 with GDScript. Keep game rules in pure/resource-style data and small testable services, while scenes handle presentation and input. Build only the minimum systems required for one exploration map and one 3-vs-3 battle; architecture must leave clean seams for later jobs, equipment, statuses, Resonance, story, and content without implementing them now.

**Tech Stack:** Godot 4.7.2 stable, GDScript 2.0, GUT (Godot Unit Test) for deterministic script tests, Android export templates, OpenJDK 17 and Android SDK for Windows-hosted Android export.

**Spec:** `docs/superpowers/specs/2026-09-09-sci-fantasy-tactical-jrpg-design.md`

## Global Constraints

- Android-first, landscape orientation.
- Exploration is top-down; combat uses a slightly angled tactical presentation.
- Combat grid is square and visually subtle except during movement/targeting.
- Initiative is individual and visible.
- Default turn economy is Move + Action.
- No attacks of opportunity.
- Tap once previews movement; tapping the same destination again confirms.
- Movement may be undone until another action is committed.
- First slice must be playable without production art, audio, crafting, full jobs, faction systems, bonds, save systems, vehicles, world map, random encounters, or NG+.
- Do not implement systems merely because they exist in the full design; YAGNI applies.
- Use placeholder geometric/pixel-style assets for the vertical slice.
- Pure gameplay rules must be testable without loading the full game scene.

---

## File Structure

```text
project.godot                         # Project config, landscape viewport, startup scene
export_presets.cfg                    # Android debug export preset (no signing secrets)
addons/gut/                           # GUT test framework

src/app/game_root.gd                  # Owns exploration/combat state transition
src/app/game_root.tscn

src/exploration/exploration_map.gd    # Player movement and encounter trigger
src/exploration/exploration_map.tscn
src/exploration/touch_stick.gd        # Virtual joystick input abstraction
src/exploration/touch_stick.tscn

src/combat/battle_state.gd            # Pure battle state and turn-phase data
src/combat/combat_unit.gd             # Pure unit stats/state
src/combat/grid_pos.gd                # Integer grid coordinate value object
src/combat/grid_map.gd                 # Bounds, walkability, neighbors, path queries
src/combat/pathfinder.gd              # Deterministic shortest-path movement
src/combat/initiative_queue.gd        # Individual turn ordering
src/combat/battle_rules.gd            # Move/attack/undo/victory rule service
src/combat/battle_controller.gd       # Scene-facing orchestration
src/combat/battle_scene.tscn
src/combat/unit_view.gd               # Visual unit representation
src/combat/unit_view.tscn
src/combat/grid_overlay.gd            # Movement tiles/path preview
src/combat/grid_overlay.tscn

src/ui/battle_command_bar.gd          # Move/Attack/Wait commands
src/ui/battle_command_bar.tscn
src/ui/initiative_timeline.gd         # Visible upcoming turns
src/ui/initiative_timeline.tscn
src/ui/battle_result.gd               # Simple victory panel
src/ui/battle_result.tscn

assets/placeholders/                  # Temporary tiles/icons/sprites only

tests/test_grid_map.gd
tests/test_pathfinder.gd
tests/test_initiative_queue.gd
tests/test_battle_rules.gd
tests/test_vertical_slice_smoke.gd
```

---

### Task 1: Project Shell + Android-Safe Configuration

**Files:**
- Create: `project.godot`
- Create: `src/app/game_root.tscn`
- Create: `src/app/game_root.gd`
- Create: `tests/test_vertical_slice_smoke.gd`
- Create: `.gitignore`

**Interfaces:**
- Produces: `GameRoot` scene with `enum GameMode { EXPLORATION, COMBAT }`
- Produces: `func enter_exploration() -> void`
- Produces: `func enter_combat() -> void`

- [ ] **Step 1: Create the Godot project configured for landscape 1280×720 logical resolution, stretch mode `canvas_items`, and a mobile-friendly renderer.**

`project.godot` must set the startup scene to `res://src/app/game_root.tscn`, landscape orientation, and touch emulation for desktop testing.

- [ ] **Step 2: Add a failing smoke test for the initial game mode.**

```gdscript
extends GutTest

func test_game_starts_in_exploration() -> void:
    var root := preload("res://src/app/game_root.gd").new()
    assert_eq(root.mode, root.GameMode.EXPLORATION)
```

- [ ] **Step 3: Run the test and verify failure because `game_root.gd` does not yet exist.**

Run: `godot --headless -s addons/gut/gut_cmdln.gd -gtest=res://tests/test_vertical_slice_smoke.gd`

Expected: FAIL due to missing preload/script.

- [ ] **Step 4: Implement the minimum `GameRoot` state.**

```gdscript
extends Node
class_name GameRoot

enum GameMode { EXPLORATION, COMBAT }
var mode: GameMode = GameMode.EXPLORATION

func enter_exploration() -> void:
    mode = GameMode.EXPLORATION

func enter_combat() -> void:
    mode = GameMode.COMBAT
```

- [ ] **Step 5: Run the smoke test and verify PASS.**

- [ ] **Step 6: Open the project in Godot and verify the empty root scene launches at the intended landscape aspect ratio.**

- [ ] **Step 7: Commit.**

```bash
git add project.godot .gitignore src/app tests/test_vertical_slice_smoke.gd
git commit -m "feat: create Android-first Godot project shell"
```

---

### Task 2: Grid Coordinates + Walkable Battlefield

**Files:**
- Create: `src/combat/grid_pos.gd`
- Create: `src/combat/grid_map.gd`
- Create: `tests/test_grid_map.gd`

**Interfaces:**
- Produces: `GridPos.new(x: int, y: int)`
- Produces: `GridMap.new(width: int, height: int, blocked: Array[Vector2i])`
- Produces: `func is_walkable(pos: Vector2i) -> bool`
- Produces: `func neighbors(pos: Vector2i) -> Array[Vector2i]`

- [ ] **Step 1: Write tests proving bounds, blocked tiles, and four-directional neighbors.**

```gdscript
extends GutTest

func test_blocked_and_out_of_bounds_tiles_are_not_walkable() -> void:
    var grid := GridMap.new(5, 4, [Vector2i(2, 1)])
    assert_true(grid.is_walkable(Vector2i(1, 1)))
    assert_false(grid.is_walkable(Vector2i(2, 1)))
    assert_false(grid.is_walkable(Vector2i(-1, 0)))
    assert_false(grid.is_walkable(Vector2i(5, 0)))

func test_neighbors_are_cardinal_and_walkable_only() -> void:
    var grid := GridMap.new(3, 3, [Vector2i(1, 0)])
    var result := grid.neighbors(Vector2i(1, 1))
    assert_has(result, Vector2i(0, 1))
    assert_has(result, Vector2i(2, 1))
    assert_has(result, Vector2i(1, 2))
    assert_does_not_have(result, Vector2i(1, 0))
```

- [ ] **Step 2: Run tests and verify failure because `GridMap` is undefined.**

- [ ] **Step 3: Implement `GridMap` with explicit width, height, and blocked-tile lookup.**

- [ ] **Step 4: Run `test_grid_map.gd`; expected PASS.**

- [ ] **Step 5: Commit.**

```bash
git add src/combat/grid_pos.gd src/combat/grid_map.gd tests/test_grid_map.gd
git commit -m "feat: add tactical battlefield grid rules"
```

---

### Task 3: Deterministic Movement Pathfinder

**Files:**
- Create: `src/combat/pathfinder.gd`
- Create: `tests/test_pathfinder.gd`

**Interfaces:**
- Consumes: `GridMap.neighbors(pos)`
- Produces: `Pathfinder.find_path(grid: GridMap, start: Vector2i, goal: Vector2i, occupied: Dictionary) -> Array[Vector2i]`
- Produces: `Pathfinder.reachable(grid: GridMap, start: Vector2i, movement: int, occupied: Dictionary) -> Dictionary`

- [ ] **Step 1: Write failing tests proving shortest path, movement range, blocked terrain, and occupied-unit avoidance.**

```gdscript
func test_find_path_routes_around_blocked_tile() -> void:
    var grid := GridMap.new(4, 3, [Vector2i(1, 1)])
    var path := Pathfinder.find_path(grid, Vector2i(0, 1), Vector2i(2, 1), {})
    assert_eq(path.front(), Vector2i(0, 1))
    assert_eq(path.back(), Vector2i(2, 1))
    assert_eq(path.size(), 5)
```

- [ ] **Step 2: Run test; expected FAIL because `Pathfinder` is undefined.**

- [ ] **Step 3: Implement breadth-first search; do not introduce terrain costs yet.**

- [ ] **Step 4: Run pathfinder and grid tests; expected PASS.**

- [ ] **Step 5: Commit.**

```bash
git add src/combat/pathfinder.gd tests/test_pathfinder.gd
git commit -m "feat: add deterministic grid movement pathfinding"
```

---

### Task 4: Combat Unit + Initiative Timeline

**Files:**
- Create: `src/combat/combat_unit.gd`
- Create: `src/combat/initiative_queue.gd`
- Create: `tests/test_initiative_queue.gd`

**Interfaces:**
- Produces: `CombatUnit.create(id: StringName, team: int, pos: Vector2i, max_hp: int, speed: int, move_range: int, attack: int) -> CombatUnit`
- Produces fields: `id`, `team`, `pos`, `max_hp`, `hp`, `speed`, `move_range`, `attack`, `has_moved`, `has_acted`
- Produces: `InitiativeQueue.setup(units: Array[CombatUnit]) -> void`
- Produces: `func current() -> CombatUnit`
- Produces: `func advance() -> CombatUnit`
- Produces: `func preview(count: int) -> Array[CombatUnit]`

- [ ] **Step 1: Write failing initiative tests.**

```gdscript
func test_fastest_unit_acts_first_and_queue_repeats() -> void:
    var fast := CombatUnit.create(&"fast", 0, Vector2i.ZERO, 10, 12, 4, 3)
    var slow := CombatUnit.create(&"slow", 1, Vector2i.ONE, 10, 6, 4, 3)
    var queue := InitiativeQueue.new()
    queue.setup([slow, fast])
    assert_eq(queue.current().id, &"fast")
    assert_eq(queue.advance().id, &"slow")
```

- [ ] **Step 2: Run; expected FAIL for undefined classes.**

- [ ] **Step 3: Implement a deterministic speed-sorted looping initiative queue.**

For the vertical slice, ties are resolved by stable `id` ordering. Do not implement timeline-delay skills yet.

- [ ] **Step 4: Run test; expected PASS.**

- [ ] **Step 5: Commit.**

```bash
git add src/combat/combat_unit.gd src/combat/initiative_queue.gd tests/test_initiative_queue.gd
git commit -m "feat: add combat units and initiative timeline"
```

---

### Task 5: Move + Action Battle Rules

**Files:**
- Create: `src/combat/battle_state.gd`
- Create: `src/combat/battle_rules.gd`
- Create: `tests/test_battle_rules.gd`

**Interfaces:**
- Produces: `BattleState.grid: GridMap`
- Produces: `BattleState.units: Array[CombatUnit]`
- Produces: `BattleState.initiative: InitiativeQueue`
- Produces: `BattleRules.preview_move(state: BattleState, unit: CombatUnit, destination: Vector2i) -> Array[Vector2i]`
- Produces: `BattleRules.commit_move(state, unit, destination) -> bool`
- Produces: `BattleRules.undo_move(state, unit) -> bool`
- Produces: `BattleRules.attack(state, attacker, target) -> int`
- Produces: `BattleRules.wait(unit) -> void`
- Produces: `BattleRules.end_turn(state) -> CombatUnit`
- Produces: `BattleRules.winner(state) -> int` where `-1` means unresolved.

- [ ] **Step 1: Write failing tests for move preview vs commit.**

- [ ] **Step 2: Write failing test proving movement can be undone before acting.**

```gdscript
func test_move_can_be_undone_before_action() -> void:
    var state := make_basic_state()
    var unit := state.units[0]
    var original := unit.pos
    assert_true(BattleRules.commit_move(state, unit, Vector2i(1, 0)))
    assert_true(BattleRules.undo_move(state, unit))
    assert_eq(unit.pos, original)
```

- [ ] **Step 3: Write failing test proving movement cannot be undone after attacking.**

- [ ] **Step 4: Write failing tests for attack damage, KO at 0 HP, wait, turn reset, and victory when one team has no living units.**

- [ ] **Step 5: Run; expected FAIL because state/rules are undefined.**

- [ ] **Step 6: Implement the smallest ruleset that satisfies these tests.**

Attack range in the slice is cardinal adjacency only. Damage is `max(1, attacker.attack)`; defense, crits, facing bonuses, elements, statuses, skills, MP, and equipment are explicitly deferred.

- [ ] **Step 7: Run all deterministic combat tests; expected PASS.**

- [ ] **Step 8: Commit.**

```bash
git add src/combat/battle_state.gd src/combat/battle_rules.gd tests/test_battle_rules.gd
git commit -m "feat: implement move action and victory battle rules"
```

---

### Task 6: Tactical Battle Scene + Unit Views

**Files:**
- Create: `src/combat/battle_scene.tscn`
- Create: `src/combat/battle_controller.gd`
- Create: `src/combat/unit_view.tscn`
- Create: `src/combat/unit_view.gd`
- Create: `src/combat/grid_overlay.tscn`
- Create: `src/combat/grid_overlay.gd`
- Create: placeholder assets under `assets/placeholders/`

**Interfaces:**
- Consumes: `BattleState`, `BattleRules`, `Pathfinder`
- Produces: `BattleController.start_battle(state: BattleState) -> void`
- Produces signal: `battle_finished(winning_team: int)`
- Produces: `GridOverlay.show_reachable(tiles: Array[Vector2i])`
- Produces: `GridOverlay.show_path(path: Array[Vector2i])`
- Produces: `GridOverlay.clear()`

- [ ] **Step 1: Build a graybox 8×6 battlefield with several blocked tiles and six placeholder units (3 player, 3 enemy).**

- [ ] **Step 2: Connect visual unit positions to `CombatUnit.pos`.**

- [ ] **Step 3: On selecting Move, render reachable tiles subtly rather than leaving a permanent grid visible.**

- [ ] **Step 4: Implement tap-once movement preview; show the computed path but do not mutate battle state.**

- [ ] **Step 5: Implement second tap on the same destination to call `commit_move`.**

- [ ] **Step 6: Implement visual undo while `has_acted == false`.**

- [ ] **Step 7: Add a temporary desktop input adapter so mouse clicks execute the same selection APIs as touch.**

- [ ] **Step 8: Run existing headless tests plus manually verify preview does not move the model until confirmation.**

- [ ] **Step 9: Commit.**

```bash
git add src/combat assets/placeholders
git commit -m "feat: add playable tactical battle scene"
```

---

### Task 7: Android Battle Command Bar + Timeline UI

**Files:**
- Create: `src/ui/battle_command_bar.tscn`
- Create: `src/ui/battle_command_bar.gd`
- Create: `src/ui/initiative_timeline.tscn`
- Create: `src/ui/initiative_timeline.gd`
- Modify: `src/combat/battle_scene.tscn`
- Modify: `src/combat/battle_controller.gd`

**Interfaces:**
- Produces signals: `move_requested`, `attack_requested`, `wait_requested`, `undo_requested`
- Produces: `InitiativeTimeline.set_units(units: Array[CombatUnit]) -> void`

- [ ] **Step 1: Add large bottom-bar touch buttons for Move, Attack, and Wait; do not add Skill or Item functionality yet.**

The final design includes Skill and Item, but the vertical slice must not add empty systems. Buttons can be introduced only when their behavior exists.

- [ ] **Step 2: Ensure controls remain usable at 1280×720 and on a tall/narrow Android landscape viewport using anchors/containers rather than fixed positions.**

- [ ] **Step 3: Add initiative timeline showing at least the next eight turns.**

- [ ] **Step 4: Wire Attack to adjacent-enemy selection and damage.**

- [ ] **Step 5: Wire Wait to end the turn.**

- [ ] **Step 6: Disable Undo after an attack or Wait commits the turn.**

- [ ] **Step 7: Run deterministic tests and manually verify UI states for Move, Attack, Wait, Undo.**

- [ ] **Step 8: Commit.**

```bash
git add src/ui src/combat/battle_scene.tscn src/combat/battle_controller.gd
git commit -m "feat: add touch battle commands and initiative UI"
```

---

### Task 8: Minimal Enemy AI

**Files:**
- Create: `src/combat/enemy_ai.gd`
- Create: `tests/test_enemy_ai.gd`
- Modify: `src/combat/battle_controller.gd`

**Interfaces:**
- Produces: `EnemyAI.choose_turn(state: BattleState, unit: CombatUnit) -> Dictionary`
- Return shape: `{ "move_to": Vector2i, "target_id": StringName }`, where `target_id == &""` means Wait.

- [ ] **Step 1: Write failing tests: attack adjacent target; otherwise move toward nearest living player; never choose blocked/occupied destination.**

- [ ] **Step 2: Run; expected FAIL because `EnemyAI` is undefined.**

- [ ] **Step 3: Implement deterministic nearest-target AI using Manhattan distance and existing pathfinder.**

- [ ] **Step 4: Run AI and combat tests; expected PASS.**

- [ ] **Step 5: Connect enemy turns to the battle controller with a short presentation delay but no artificial thinking loop.**

- [ ] **Step 6: Manually complete one full 3-vs-3 battle.**

- [ ] **Step 7: Commit.**

```bash
git add src/combat/enemy_ai.gd src/combat/battle_controller.gd tests/test_enemy_ai.gd
git commit -m "feat: add deterministic enemy battle turns"
```

---

### Task 9: Top-Down Exploration + Touch Stick

**Files:**
- Create: `src/exploration/exploration_map.tscn`
- Create: `src/exploration/exploration_map.gd`
- Create: `src/exploration/touch_stick.tscn`
- Create: `src/exploration/touch_stick.gd`
- Create: placeholder exploration tiles/assets

**Interfaces:**
- Produces signal: `encounter_requested(encounter_id: StringName)`
- Produces: `TouchStick.direction() -> Vector2`

- [ ] **Step 1: Build a small coastal-enclave graybox map containing walkable space, walls, one door/archway, and one visible hostile encounter marker.**

- [ ] **Step 2: Implement top-down character movement driven by an input vector, with keyboard input using the same path as touch-stick input.**

- [ ] **Step 3: Implement a thumb-friendly virtual stick with dead zone and normalized output.**

- [ ] **Step 4: Add an encounter trigger that emits `encounter_requested(&"enclave_test")` when the player reaches the hostile marker.**

- [ ] **Step 5: Manually verify exploration movement works with mouse-emulated touch and keyboard without changing game rules.**

- [ ] **Step 6: Commit.**

```bash
git add src/exploration assets/placeholders
git commit -m "feat: add touch-driven exploration graybox"
```

---

### Task 10: Seamless Exploration → Combat → Exploration Loop

**Files:**
- Modify: `src/app/game_root.gd`
- Modify: `src/app/game_root.tscn`
- Modify: `src/exploration/exploration_map.gd`
- Modify: `src/combat/battle_controller.gd`
- Create: `src/ui/battle_result.tscn`
- Create: `src/ui/battle_result.gd`
- Modify: `tests/test_vertical_slice_smoke.gd`

**Interfaces:**
- Consumes: `encounter_requested`, `battle_finished`
- Produces: full mode transition preserving exploration scene state.

- [ ] **Step 1: Add a failing state-transition test.**

```gdscript
func test_game_can_enter_combat_and_return_to_exploration() -> void:
    var root := GameRoot.new()
    root.enter_combat()
    assert_eq(root.mode, root.GameMode.COMBAT)
    root.enter_exploration()
    assert_eq(root.mode, root.GameMode.EXPLORATION)
```

- [ ] **Step 2: Implement scene orchestration so the exploration map remains loaded/paused while the battle presentation overlays or replaces its active presentation without discarding exploration state.**

- [ ] **Step 3: Trigger `enclave_test` battle from exploration.**

- [ ] **Step 4: On player victory, show a simple Victory panel and return to the same exploration location.**

- [ ] **Step 5: On player defeat, show Retry; Retry reconstructs only the battle state and does not restart the application.**

- [ ] **Step 6: Complete the loop manually three times to verify no duplicate signals, retained dead units, or stale UI remain between battles.**

- [ ] **Step 7: Run all headless tests; expected PASS.**

- [ ] **Step 8: Commit.**

```bash
git add src/app src/exploration src/combat src/ui tests/test_vertical_slice_smoke.gd
git commit -m "feat: complete exploration combat vertical slice loop"
```

---

### Task 11: Android Export + Real-Device Acceptance

**Files:**
- Create/Modify: `export_presets.cfg`
- Create: `docs/android-development.md`
- Create: `docs/vertical-slice-acceptance.md`

**Interfaces:**
- Produces: debug APK for local device testing.
- Does not contain release keystore credentials or signing secrets.

- [ ] **Step 1: Configure the Android debug export preset for landscape orientation and ARM64 support.**

- [ ] **Step 2: Document Windows setup: OpenJDK 17, Android SDK path, Godot Android export templates, and USB-debugging deployment.**

- [ ] **Step 3: Export a debug APK.**

Run from configured Windows/Godot environment:

```text
Project > Export > Android > Export Project
```

Expected: debug APK created with no release signing requirement.

- [ ] **Step 4: Install the debug build on the target Android device and launch it.**

- [ ] **Step 5: Acceptance-test exploration controls.**

Pass if:
- virtual stick movement is responsive;
- UI does not overlap critical gameplay;
- orientation remains landscape;
- game remains readable at native device resolution.

- [ ] **Step 6: Acceptance-test battle controls.**

Pass if:
- tapping Move shows reachable cells;
- first destination tap previews only;
- second tap commits;
- Undo works before acting;
- Attack can target adjacent enemy;
- Wait ends the turn;
- initiative timeline updates;
- enemy turns execute;
- victory returns to exploration.

- [ ] **Step 7: Record device model, Android version, render backend, average observed frame behavior, control issues, and any crashes in `docs/vertical-slice-acceptance.md`. Do not claim performance metrics that were not measured.**

- [ ] **Step 8: Fix only acceptance-blocking defects found in this slice, rerun the affected deterministic tests, and repeat only the failed acceptance cases.**

- [ ] **Step 9: Commit.**

```bash
git add export_presets.cfg docs
git commit -m "test: validate first playable slice on Android"
```

---

## Deferred Until After Vertical Slice Acceptance

The following are explicitly *not* part of this plan:

- full 16–20 job system
- class mastery / cross-class skills
- character personal grids
- statuses and elemental reactions
- facing bonuses and elevation mechanics
- MP, healing, items, skills, ultimates
- Resonance and combo attacks
- boss Break system
- equipment, modules, relics, crafting
- XP/level progression
- bonds, recruitment, dialogue, factions
- save/load
- world map and random encounters
- vehicles/mobile base
- production HD-2D art, animation, VFX, music, audio
- endgame / NG+

Each becomes a separate implementation plan after the vertical slice proves the core touch + tactical loop.

## Acceptance Gate

The slice is accepted only when all of the following are true:

1. All deterministic headless tests pass.
2. Exploration works with virtual-stick touch input on an Android device.
3. A visible encounter transitions into tactical combat without losing exploration state.
4. Six units participate in an initiative-ordered 3-vs-3 battle.
5. Player can Move, preview/confirm movement, Undo before acting, Attack, and Wait.
6. Enemy AI can complete its turns without manual intervention.
7. Battle ends correctly when one team is defeated.
8. Victory returns the player to the exploration map at the prior location.
9. The vertical slice completes repeatedly without stale state or duplicate signals.
10. Android acceptance evidence records the actual tested device/environment and any known defects.

## Plan Self-Review

- Spec coverage for the first-playable scope: complete.
- Full-game features not required to prove the vertical slice are deliberately deferred.
- No release signing secrets are required or stored.
- Rule classes and UI/controller classes are separated so deterministic combat logic can be tested headlessly.
- Interface names used by later tasks are defined by earlier tasks.
- No placeholder implementation steps remain inside the vertical-slice scope.
