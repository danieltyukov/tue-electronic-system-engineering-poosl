# Electronic Systems Engineering: POOSL Model

POOSL model of the xCPS production line, built for the TU/e design-based-learning
project in Electronic Systems Engineering (course 5XIC0). This repository holds the
POOSL part of the project. The system was also captured as a SysML model and driven
by a set of Python analysis scripts, kept in the sibling repositories linked below.

POOSL (Parallel Object-Oriented Specification Language) describes a system as a set of
concurrent processes that exchange messages over channels and advance a shared model
clock. Here it is used to model the xCPS platform, a small assembly line that transports
work pieces over belts, routes and stops them, and assembles two piece types (an inverted
top and a bottom) into a finished product. The model is split into a physical platform
layer and a control application layer, which lets the timing behaviour of the machine be
simulated and traced before any control code runs on hardware.

## What the model does

The model separates the hardware from the control logic and connects the two over a single
`platform` channel:

- The platform layer models the physical line: belts move pieces, stoppers gate them,
  switches route them, gantries and a pick-and-place arm move pieces between belts and the
  storage area, and a turner and index table reposition pieces for assembly. Each component
  reports sensor events to the application layer and emits trace events with timestamps.
- The application layer models the controller. A dispatcher issues the load and store
  sequence for a batch, and a product process tracks each piece through the line.

Component travel distances, belt speed, gantry arm speeds in x, y and z, and the timing of
the index table, switches and pick-and-place are all parameters, so the same structure can
be simulated under different machine configurations. The top-level system instantiates the
model with one parameter set (`setDefaults addSlowBelts addFastIndex addNormalArm1
addNormalArm2`), which fixes the speeds used for a simulation run.

## Contents

| File | Role |
| --- | --- |
| `xcps-model.poosl` | Top-level model. Defines the `Platform` cluster (piece flow, five belts, two gantries, six stoppers, turner, index table, two switches, pick-and-place, storage area), the `Application` cluster (dispatcher and ten products), and the `system` entry point that wires them together and sets the parameters. |
| `platform.poosl` | Process classes for the physical layer: `PieceFlow`, `Belt`, `StorageArea`, `Gantry`, `Stopper`, `Turner`, `IndexTable`, `Switch` and `PickAndPlace`. Encodes the belt transport trajectories, sensor activations and the interactions between components. |
| `parameters.poosl` | Data classes holding the model parameters: `GantryPosition` (3D arm positions and a move-time calculation), `PlatformParameters` (travel distances and speeds), `ApplicationParameters` and `ModelParameters` with the named configuration presets. |
| `empty-application.poosl` | Application layer used by the model: the `Dispatcher` process that drives platform initialization and the load and store batch sequence, and the `Product` process that follows a single piece. |
| `empty.poosl` | Working copy of the application layer kept alongside the main version. |
| `thing.txt` | Scratch fragment of the load and store batch sequence. |
| `trace.ini` | Trace configuration read by the simulator: trace type, time unit (seconds) and whether tracing is enabled. |
| `simulator/trace.etf` | Execution trace recorded from a simulation run, in the TRACE format (resources, claims, events and signals for each component over time). |
| `simulator/trace.view` | View settings for inspecting the trace as a Gantt-style activity chart. |

## Running the model

The `.poosl` files are simulated with the Eclipse-based POOSL IDE, which runs the model on
the Rotalumis execution engine. Opening `xcps-model.poosl` and starting a simulation steps
the concurrent processes forward on the model clock and writes an execution trace configured
by `trace.ini`.

The resulting `simulator/trace.etf` is an execution trace in the TRACE format. It can be
opened in TRACE4CPS to view component activity, sensor events and dependencies over time as
a Gantt-style chart; `simulator/trace.view` holds a saved view configuration for that
inspection.

## Related repositories

This is one part of the Electronic Systems Engineering design-based-learning project. The
other parts:

- [tue-electronic-system-engineering-sysml](https://github.com/danieltyukov/tue-electronic-system-engineering-sysml): SysML model of the same system.
- [tue-electronic-system-engineering-script](https://github.com/danieltyukov/tue-electronic-system-engineering-script): Python scripts used in the project.

## Technologies

- POOSL (Parallel Object-Oriented Specification Language)
- POOSL IDE (Eclipse-based) with the Rotalumis simulation engine
- TRACE4CPS for trace visualization
