# AAE5303 Tutorial: Part 1 (ORB-SLAM3) to Part 2 (OpenSplat)

## Tutorial Goal

This repository documents a complete course-style pipeline:

1. **Part 1**: Run ORB-SLAM3 monocular VO on AMtown02 and get `CameraTrajectory.txt`
2. **Part 2**: Use those poses + images to train OpenSplat and export `.ply` / `.splat`

It is written in tutorial format so you can follow, reproduce, and present the full handoff.

---

## Quick Start (Workflow)

### Step 1: Prepare Part 1 outputs

- VO trajectory (`CameraTrajectory.txt`)
- AMtown02 undistorted images
- Part 1 metrics and plots

### Step 2: Build OpenSplat input

- Convert TUM poses + images into COLMAP-style project layout
- Generate sparse initialization points

### Step 3: Train OpenSplat on GPU

- Run OpenSplat with stable settings
- Monitor step/loss and checkpoint generation

### Step 4: Export and visualize

- Export final `splat.ply`
- Convert to compressed `splat.splat`
- Open in [PlayCanvas Viewer](https://playcanvas.com/viewer)

---

## Repository Structure

- `README.md` — this tutorial page
- `docs/TECHNICAL_ROUTE_AND_ATTEMPTS.md` — full engineering route, all attempts, and fixes
- `output/PART1_AMtown02_results.md` — Part 1 summary
- `output/PART2_AMtown02_OpenSplat_results.md` — Part 2 summary
- `output/amtown02_step10_final.splat` — final compressed model
- `output/amtown02_step10_cameras.json` — camera metadata
- `output/amtown02_step10_train.log` — training log
- `figures/` — Part 1 context visualizations

---

## Part 1 -> Part 2 Linkage (Core)

The central dependency is:

- Part 1 provides camera poses (`CameraTrajectory.txt`)
- Part 2 uses these poses to initialize and train Gaussian splats

So Part 2 quality is tightly tied to Part 1 tracking quality and completeness.

---

## Final Run Snapshot

- Dataset scene: **AMtown02**
- Trainer: **OpenSplat (CUDA)**
- Stable training route: sampled project (`step=10`)
- Iterations: **35000**
- Completion evidence: `Step 35000 ... (100%)`

Final artifacts:

- `output/amtown02_step10_final.splat`
- `output/amtown02_step10_cameras.json`
- `output/amtown02_step10_train.log`

---

## Visualization Guide

1. Open [PlayCanvas Viewer](https://playcanvas.com/viewer)
2. Drag and drop `output/amtown02_step10_final.splat`
3. Inspect geometry continuity, floaters, and rendering stability

---

## Engineering Notes

During development, we attempted multiple routes (including heavier SfM-style preprocessing) and handled OOM/service issues under WSL.
The full route comparison and troubleshooting timeline is documented in:

- `docs/TECHNICAL_ROUTE_AND_ATTEMPTS.md`

This keeps the tutorial practical and transparent for groupwork reporting.
