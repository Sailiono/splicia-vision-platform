# Validation Plan

The verification strategy treats alignment quality, timing, safety, and reproducibility as separate questions.

## Measurement contract

Every processed frame should be traceable through:

```text
frame identity + capture timestamp
→ calibration/profile version
→ detected geometry + confidence
→ estimated alignment state
→ controller decision
→ axis command/acknowledgement
→ next observed state
```

## Test layers

| Layer | Example checks |
| --- | --- |
| Capture | camera enumeration, format, exposure, ROI, timestamp monotonicity, frame loss, reconnect, dual-view skew |
| Vision | synthetic geometry, focus/contrast variation, partial occlusion, contamination, false edges, confidence rejection |
| Calibration | pixel scale, axis direction, camera-to-motion mapping, repeatability, record versioning, invalidation rules |
| Motion | homing, soft limits, backlash, command timeout, wrong direction, stalled axis, emergency stop |
| Control | convergence time, final error, overshoot, oscillation, non-convergence, confidence loss, safe fallback |
| System | long runs, restart recovery, resource pressure, storage exhaustion, service supervision, version mismatch |
| Replay | deterministic reprocessing, algorithm A/B comparison, regression thresholds, failure reproduction |

## Recommended metrics

- initial and final gap/offset/angle error;
- convergence time and number of correction cycles;
- overshoot, settling behavior, and command reversals;
- capture-to-measurement and measurement-to-command latency distributions;
- frame drop, invalid-measurement, timeout, and safe-stop rates;
- repeatability across runs, fibers, lighting conditions, and power cycles;
- CPU, memory, temperature, and storage behavior during sustained operation.

## Run package

A reproducible run package should contain:

```text
manifest.json
config_snapshot/
telemetry.jsonl
events.jsonl
selected_frames/
overlays/
summary.json
version.txt
```

The public case study describes this contract but does not publish private production captures or process parameters.

## Evidence rule

A screenshot or successful demo is useful evidence of integration, but it is not enough to claim repeatability or industrial performance. Performance claims require raw run packages, defined acceptance thresholds, repeated trials, environmental conditions, and a reviewable analysis method.
