# Splicia Vision Platform

A sanitized industrial-vision engineering case study for microscopic fiber alignment, Linux camera pipelines, and precision multi-axis motion control.

Splicia demonstrates how we approach a complex equipment project across embedded Linux, imaging, algorithms, motion, user interaction, and verification. The public repository intentionally presents the architecture and validation method rather than customer material or production source code.

## Capability map

| Area | Demonstrated experience |
| --- | --- |
| Embedded Linux | Raspberry Pi/CM-class systems, RDK-class edge platforms, V4L2/libcamera-style camera paths, remote deployment, service diagnostics, and platform migration |
| Camera bring-up | Dual IMX296 global-shutter cameras, media topology, RAW capture, exposure/ROI control, synchronization tradeoffs, and frame-pipeline diagnosis |
| Industrial vision | Fiber edge, centerline, end-face, angle, gap, and offset estimation; confidence handling; overlay generation; calibration-aware measurements |
| Precision motion | Semantic six-axis model, controller abstraction, soft limits, homing, backlash, staged axis enablement, and future 12/16-axis expansion |
| Closed-loop control | Continuous observation, state estimation, incremental correction, convergence monitoring, and safe stop/fallback behavior |
| Product software | Operator/engineer/service views, profile-based configuration, state machines, calibration records, event logs, reports, and replayable run packages |
| Verification | Simulation, recorded-data replay, synthetic fault injection, convergence metrics, latency budgets, run-to-run comparison, and failure evidence |

## System concept

```text
dual microscopic cameras
          │
          ▼
Linux capture pipeline
          │ frames + timestamps
          ▼
vision measurement ──> alignment-state estimation
                              │
                              ▼
                      closed-loop controller
                              │
                              ▼
                       multi-axis motion
                              │
                 ┌────────────┴────────────┐
                 ▼                         ▼
          operator UI              telemetry / replay
```

The core idea is to move from stop-and-measure operation toward continuous observation and incremental correction:

```text
capture → measure → estimate → correct → verify convergence
```

## Engineering scope

The underlying work explored:

- dual-view microscopic imaging with global-shutter sensors;
- Linux camera and multimedia bring-up across more than one hardware platform;
- the boundary between driver/ISP failures and application-level failures;
- a six-axis semantic API that allows early demos to enable only the axes physically available;
- a modular pipeline separating capture, vision, estimation, motion, control, UI, logging, and replay;
- run packages containing configuration snapshots, telemetry, events, selected frames, overlays, and summaries;
- a migration path from Python/OpenCV prototypes toward C++ optimization where profiling justifies it.

Detailed public architecture and verification plans are available in [`docs/architecture.md`](docs/architecture.md) and [`docs/validation.md`](docs/validation.md).

## What is not claimed

This case study does not claim a completed commercial fiber splicer, production loss performance, a certified high-voltage arc subsystem, a fully validated six-axis industrial servo, or a released customer product. It separates demonstrated engineering work from planned productization.

## Disclosure boundary

The private engineering repository contains platform experiments and implementation records that are not suitable for direct publication. This public case study excludes customer/legacy product material, network addresses, SSH or deployment credentials, private binaries, captured images, commercial parameters, source packages, and site-specific logs. See [`docs/disclosure-boundary.md`](docs/disclosure-boundary.md).

No reuse license is currently granted for original Splicia documents. The repository is published for portfolio review and technical discussion.
