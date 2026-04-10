# Technical Route, Attempts, and Engineering Retrospective

This document summarizes the actual engineering effort behind Part 2 (OpenSplat), and how it inherits from Part 1 (ORB-SLAM3 VO).

## A. Context and dependency on Part 1

Part 2 is not independent: it directly depends on Part 1 outputs.

- Part 1 produced camera trajectories on AMtown02 (`CameraTrajectory.txt`)
- Part 2 used those poses as priors for OpenSplat reconstruction

Without robust Part 1 trajectory export and verification, Part 2 could not start.

## B. Major technical routes we tried

### Route 1: Full COLMAP SfM pipeline + OpenSplat

Attempt:

- `feature_extractor`
- `sequential_matcher` (GPU-enabled)
- `mapper`
- convert model for OpenSplat

Why we explored it:

- Standard robust route for sparse points and refined camera geometry

Observed issues:

- End-to-end runtime very long on the full AMtown02 sequence
- CPU-heavy stages still dominate even with GPU matching
- Workflow complexity high relative to assignment target

Decision:

- Not selected as main route due cost/benefit mismatch for this task

### Route 2: Direct VO-poses path (selected)

Attempt:

- Convert ORB-SLAM3 TUM trajectory + undistorted images to COLMAP-style project
- Generate sparse initialization points from camera rays/depth sampling
- Train OpenSplat directly

Why we selected it:

- Matches assignment requirement (images + camera poses)
- Lower pipeline complexity
- Faster to reproduce and debug

## C. Failure modes and what we fixed

### 1) OpenSplat process exits during loading stage

Symptom:

- Log repeatedly shows only `Loading ...jpg`
- Process exits before entering step/loss iteration

Diagnosis:

- Kernel OOM records indicated process kill during image-loading memory peak

Fixes attempted:

- Add large WSL swap
- Adjust downscales and resolution schedule
- Reduce memory pressure route-by-route

Outcome:

- Full-frame dense settings remained unstable on this hardware

### 2) WSL intermittently unstable (`E_UNEXPECTED`)

Symptom:

- Some WSL shell calls fail unexpectedly during long runs

Fix:

- Restart WSL service (`wsl --shutdown`)
- Re-enable swap after restart
- Resume monitoring with shorter polling loops

### 3) Overly complex preprocessing path

Symptom:

- Running a full SfM stack increased total risk and runtime

Fix:

- Pivot to VO-poses direct route with conversion scripts

## D. Final route that completed successfully

### Input preparation

1. Use AMtown02 undistorted images
2. Use ORB-SLAM3 `CameraTrajectory.txt`
3. Convert to COLMAP-style structure
4. Generate sparse points for initialization

### Stable training configuration

- Sampling: `step=10` (from 6711 matched poses to 672 training images)
- OpenSplat training:
  - CUDA enabled
  - 35,000 iterations
  - periodic checkpoint export

### Completion evidence

- Reached `Step 35000 (100%)`
- Final outputs exported:
  - `splat.ply`
  - `cameras.json`
- Converted final compressed model:
  - `amtown02_step10_final.splat`

## E. Why this route is defensible

- Preserves the Part 1 -> Part 2 dependency chain
- Provides a complete reproducible run and deliverable
- Documents failed alternatives and constraints transparently
- Keeps a clear upgrade path (move to larger-memory machine for denser full-frame runs)

## F. Upgrade path for future improvement

If better hardware is available:

1. Increase sample density (`step=5` then `step=2`)
2. Re-evaluate full-frame runs with higher memory headroom
3. Compare visual quality vs. training cost
4. Keep same conversion scripts for reproducibility

---

In short: we did not just run one command; we iterated through multiple technical routes, debugged real system constraints, and delivered a stable, explainable, and reproducible Part 2 result that clearly continues from Part 1.
