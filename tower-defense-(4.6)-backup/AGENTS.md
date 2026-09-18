# Tower Defense — agent notes

## Project
- **Godot 4.6**, GDScript, Forward Plus renderer, Jolt Physics (3D)
- No test framework, no CI, no formatter/linter config
- Beginner learning project — **explain concepts, ask before generating code**
- The user saves and edits frequently — **always re-read the relevant files immediately before responding**, never rely on earlier reads

## Architecture (component-style)
Each script owns one job:

| File | Role |
|---|---|
| `towers.gd` | Tower orchestrator — asks `targeting` for target, will own aiming/shooting |
| `zone.gd` | `Area2D` detection zone — emits `enemy_entered`/`enemy_exited`, public API: `get_enemies()`/`has_enemies()`/`get_enemy_count()`/`set_range()` |
| `targeting.gd` | Targeting brain — dictionary of modes (`first`/`last`/`closest`/`farthest`) + `Callable` dispatch, stateless `get_target(enemies, source_position)` |
| `bloons_path.gd` | `Path2D` + `PathFollow2D` — moves enemy along path at `speed` |

## Scene tree (key nodes in `towers.tscn`)
```
Towers (Node2D, towers.gd)
├─ Zone (Area2D, zone.gd) — detection radius via CircleShape2D
├─ Sprite2D — tower visual
├─ TargetUi (Control) — Panel > OptionButton + Label
├─ Targeting (Node, targeting.gd) — targeting strategy brain
└─ Marker2D — muzzle / spawn point
```

## How to run
Open `project.godot` in the Godot editor — no build step.

## Current conventions (open to change)
- Scripts currently connect signals in `_ready()` rather than relying on editor connections
- Enemies currently use group `"enemies"` — zone filters by group
- Currently uses `is_instance_valid()` + `is_queued_for_deletion()` cleanup in zone
- Tower targeting is first-enemy (`[0]`) and last-enemy (`.back()`) only so far
- Targeting strategies use a uniform 2-arg signature `(enemies, source_position)`; `_source_position` = intentionally unused
- `zone.gd` uses `@export var shape_node` (inspector-wired) instead of `$` child lookup
