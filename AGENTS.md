# TinySim - Agent Instructions

## Project Overview

TinySim is an **economic simulation** built in Unity that models worker behavior, production, and happiness dynamics. The simulation spawns multiple workers in a grid who perform actions (produce goods or rest) throughout simulated days.

## Project Structure

- **Assets/Scripts/**: Core simulation logic (workers, actions, products)
- **Assets/Scenes/**: Unity scenes for the simulation
- **Assets/Prefabs/**: Reusable GameObject templates (worker prefab)
- **Assets/Configs/**: Configuration files
- **ProjectSettings/**: Unity project configuration

## Core Architecture

### State Machine Pattern for Worker Actions
Workers execute a state machine of `WorkerAction` types:
- **`ProduceAction`**: Worker produces a specific product over a duration, affected by product effort and worker capacity
- **`RestAction`**: Worker rests; duration is calculated from production time
- **`WorkerAction`** (base): Abstract class defining `StartTime`, `EndTime`, and `Name`

### Key Classes

| Class | Purpose |
|-------|---------|
| `Worker` | MonoBehaviour managing individual worker state, inventory, and action transitions |
| `Playground` | Scene manager; spawns workers in a grid, manages simulation time, triggers end-of-day events |
| `Product` | ScriptableObject; defines production effort, happiness value, and associated need |
| `ProduceAction` | Action subclass; calculates production output as `(duration * capacity) / productionEffort` |
| `RestAction` | Action subclass; represents downtime after production |

### Simulation Flow

1. **Update Cycle** (`Playground.Update()`):
   - Check all workers' current action completion status
   - Transition to new actions if needed
   - Track elapsed time toward next day

2. **Day Transition** (`Worker.EndDay()`):
   - Calculate happiness based on inventory distribution
   - Happiness reward increases for products with higher quantities (diminishing returns via sorting)
   - Reset inventory for next day

## Development Conventions

- **Namespace**: All game code uses `EconomicSimulation` namespace
- **Time System**: Uses `Time.time` (elapsed seconds since play start); days tracked manually via `_nextDayTime`
- **Configuration**: Products are created as ScriptableObject assets in `Assets/Configs/` via `CreateAssetMenu`
- **TextMesh Pro**: Worker status display uses `TextMeshPro.TextMeshProUGUI` (Unity 2020+ dependency)
- **Randomization**: Uses `UnityEngine.Random` for worker capacity, product selection, and production duration variance

## Common Tasks

### Adding a New Worker Action Type
1. Create a new class inheriting from `WorkerAction`
2. Implement `Name` property
3. Add factory method or case handler in `Worker.FinishAction()` or `Worker.StartProduceAction()`

### Adding New Product Needs
1. Extend `Need` enum in [Need.cs](Assets/Scripts/Need.cs)
2. Update product assignment logic in `Playground` (if filtering by need type is needed)

### Adjusting Simulation Parameters
- Worker capacity range: `Playground.workerCapacityRange`
- Day length: `Playground.dayLength` (in seconds)
- Grid dimensions: `Playground.size` and `Playground.step`

## Build & Run

- Open project in **Unity 2020 LTS or later**
- Load the scene from **Assets/Scenes/** (main scene configuration)
- Press Play in editor or build to target platform
- Target frame rate: Fixed at 60 FPS ([Bootstrap.cs](Assets/Scripts/Bootstrap.cs))

## Key Files for Agents

- **Worker behavior logic**: [Assets/Scripts/Worker.cs](Assets/Scripts/Worker.cs)
- **Action definitions**: [Assets/Scripts/ProduceAction.cs](Assets/Scripts/ProduceAction.cs), [Assets/Scripts/RestAction.cs](Assets/Scripts/RestAction.cs)
- **Scene management**: [Assets/Scripts/Playground.cs](Assets/Scripts/Playground.cs)
- **Configuration**: [Assets/Scripts/Product.cs](Assets/Scripts/Product.cs)

## Tips for Efficient Development

- **Test production math**: Verify `ProduceAction.Finish()` calculation with edge cases (very high/low capacity and effort)
- **Debug happiness calculation**: `EndDay()` sorting logic is critical; trace through with varied inventory distributions
- **Monitor frame rate**: Spawning many workers will impact performance; check `Update()` loop scaling
- **Use Prefabs**: Modify the worker prefab template when adjusting visual representation or UI

---

Generated for agent-guided development. Last updated: May 2026
