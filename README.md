# AAE5303 Tutorial: ORB-SLAM3 to OpenSplat (AMtown02)

This repository is a tutorial-style handoff from:

- **Part 1**: Monocular visual odometry with ORB-SLAM3
- **Part 2**: 3D Gaussian Splatting reconstruction with OpenSplat

The key objective is to clearly show **how Part 1 feeds Part 2**:

- Part 1 outputs `CameraTrajectory.txt` (camera poses)
- Part 2 uses those poses + images to build a 3DGS scene

---

## 1) What is included

- Part 1 summary and metric report
  - `output/PART1_AMtown02_results.md`
- Part 2 summary and final run record
  - `output/PART2_AMtown02_OpenSplat_results.md`
- Detailed engineering retrospective (all attempts and route decisions)
  - `docs/TECHNICAL_ROUTE_AND_ATTEMPTS.md`
- Visuals for Part 1 context
  - `figures/amtown02_metrics.png`
  - `figures/amtown02_pose_coverage.png`
  - `figures/trajectory_evaluation.png`
- Final Part 2 reconstruction artifacts
  - `output/amtown02_step10_final.splat`
  - `output/amtown02_step10_cameras.json`
  - `output/amtown02_step10_train.log`

---

## 2) Part 1 to Part 2 linkage

### Part 1 output

Part 1 (ORB-SLAM3 monocular VO) produced:

- TUM trajectory with dense frame-level poses (`CameraTrajectory.txt`)
- evaluation metrics (ATE, RPE trans drift, RPE rot drift, completeness)

### Why this matters for Part 2

OpenSplat needs camera poses to initialize training for 3D reconstruction.
Instead of running a full SfM pipeline from scratch, this project uses:

- undistorted AMtown02 images
- ORB-SLAM3 trajectory as pose priors

Then converts data into OpenSplat-compatible COLMAP-style inputs.

---

## 3) Final stable route used

1. Extract/prepare AMtown02 images and VO poses
2. Convert trajectory + images to COLMAP project structure
3. Generate sparse points for OpenSplat initialization
4. Train OpenSplat on GPU
5. Export `.ply`, then convert to compressed `.splat`

---

## 4) Important note on engineering trade-off

On this hardware, full-frame OpenSplat loading with dense settings caused WSL OOM kills during image loading.

To complete a full end-to-end reproducible run, we used a stable sampling setup (`step=10`) and trained to 35,000 iterations.

This preserves the tutorial value and provides a complete deliverable, while documenting all failed/high-cost alternatives in detail.

See:

- `docs/TECHNICAL_ROUTE_AND_ATTEMPTS.md`

---

## 5) Output quick view

- Final compressed model (recommended for viewer):  
  `output/amtown02_step10_final.splat`
- Viewer: [PlayCanvas Viewer](https://playcanvas.com/viewer)
  - Open viewer
  - Drag and drop `output/amtown02_step10_final.splat`

---

## 6) Suggested reading order

1. `output/PART1_AMtown02_results.md`
2. `output/PART2_AMtown02_OpenSplat_results.md`
3. `docs/TECHNICAL_ROUTE_AND_ATTEMPTS.md`

This order makes the Part 1 -> Part 2 handoff explicit and easy to present.
