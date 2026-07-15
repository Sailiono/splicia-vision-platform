# Architecture

> **Status: proposed Phase2 architecture.** The modules below define design responsibilities and integration boundaries; they are not implementation or performance claims.

## Design goals

The platform is organized around four properties:

1. **Portability** — camera, motion, and UI code should not be inseparable from one Linux board;
2. **Observability** — every alignment run should produce enough timing, state, and measurement evidence to explain its result;
3. **Progressive integration** — a six-axis software model can run with simulated or disabled axes while hardware is added in stages;
4. **Safety boundaries** — motion, vision, and future high-energy process functions use explicit states, limits, and interlocks.

## Modules

| Module | Responsibility |
| --- | --- |
| `capture` | Camera discovery, configuration, buffer ownership, timestamps, dropped-frame reporting, and a platform-neutral frame API |
| `vision` | ROI management, preprocessing, fiber geometry extraction, confidence calculation, and overlays |
| `estimation` | Fuse dual-view measurements into a calibrated alignment state with validity and uncertainty |
| `motion` | Axis discovery, units, direction, limits, homing, backlash, command acknowledgements, and controller adapters |
| `control` | Coarse/fine alignment states, incremental corrections, settling logic, convergence tests, and abort/fallback behavior |
| `ui` | Live operation, engineering tuning, calibration, service diagnostics, and report/replay views |
| `logging` | Configuration snapshots, frame/measurement timing, events, commands, results, and version manifests |
| `replay` | Deterministic reruns of vision and control decisions without requiring the physical machine |

## Semantic axis model

The first software model uses six logical axes:

```text
L_X  L_Y  L_Z     left fiber position
R_X  R_Y  R_Z     right fiber position
```

Each axis has an identity, role, unit, scale, direction, limits, homing method, maximum/debug speed, backlash model, controller type, calibration source, confidence, enable state, and simulation state.

This prevents application logic from depending on anonymous `axis0`/`axis1` numbering and leaves room for rotation, focus, clamp, arc-position, and other product-specific axes later.

## State flow

```text
IDLE
  ↓
SELF_CHECK → CALIBRATION_REQUIRED
  ↓
ACQUIRE
  ↓
COARSE_ALIGN
  ↓
FINE_ALIGN
  ↓
VERIFY_CONVERGENCE
  ├── success → COMPLETE
  └── invalid/timeout/limit → SAFE_STOP
```

High-voltage discharge, heating, tension testing, and other process-specific stages are deliberately outside the public case-study scope.

## Linux platform strategy

- begin with the platform that provides the most stable camera path for algorithm and control development;
- isolate platform-specific V4L2/libcamera/media-controller or vendor multimedia code behind the capture API;
- keep raw-capture, ISP, ROI, timestamp, and buffer failures observable as separate error classes;
- migrate only after the algorithm, data contract, and validation set are stable;
- optimize measured bottlenecks in C++ rather than rewriting the whole prototype prematurely.
