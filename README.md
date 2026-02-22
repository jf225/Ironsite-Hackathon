# 3D Construction Progress Intelligence System  
**UMD × Ironside Hackathon**

A software-only system for tracking construction progress using standard video footage. The pipeline converts video into geometric representations (point clouds or depth maps) and compares time-separated states to identify structural change—without relying on LiDAR or object detection.

---

## Motivation

Construction progress tracking is still largely manual, subjective, and expensive. While LiDAR-based solutions exist, they are costly and impractical for continuous deployment on active job sites.

This project explores whether **geometry-first analysis from ordinary video** can provide meaningful progress intelligence, even in messy real-world environments.

---

## Core Idea

Instead of asking *“What objects are present?”*, this system asks:

> **How has the geometry of the environment changed over time?**

The pipeline compares spatial structure across time using:
- 3D point cloud reconstruction (Structure-from-Motion), and
- Task-specific depth-based modeling for wall construction.

---

## System Overview

The project evaluates **three complementary approaches**, each with different trade-offs.

---

## Phase 1 — Real-Site 3D Reconstruction (Exploratory)

**Input:**  
Handheld construction site footage

**Method:**  
- Structure-from-Motion (SfM)
- Sparse and dense point cloud reconstruction
- Filtering and normalization

**Goal:**  
Reconstruct construction geometry directly from live site footage.

**Outcome:**  
Partial structural reconstruction is achievable, but unstable camera motion, worker interference, occlusion, and uneven sampling significantly degrade reconstruction quality.

This phase establishes **why naïve SfM struggles in live construction environments**.

### Example Outputs

- Full 3D Point Cloud – Before Model  
- Zoomed Masonry MVP – Before Model  
- After Model Snapshot (501 Points)  
- Masonry Middle-Stage (5–9 min) – 2445 Points  
- Masonry Overlap (Before vs Middle)

**Why these results are decent:**
- Large structural regions are captured
- Vertical stacking and horizontal layering emerge
- Dominant geometric axes are preserved

**Why they are not perfect:**
- Inconsistent camera baselines weaken depth triangulation
- Dynamic elements introduce feature mismatch
- Uneven sampling produces fragmented surfaces

---

## Phase 2 — Controlled Environment Validation (Proof of Concept)

**Motivation:**  
To isolate the reconstruction and comparison pipeline from environmental noise and validate its correctness.

**Setup:**
- Stabilized orbital camera motion
- Target always in frame

**Scenes:**
- **Baseline State (A):** Table in empty room  
- **Progress State (B):** Same table with six added chairs  

### Results

- **Baseline Model (Blue):**  
  Represents the table and floor plane. Serves as the zero-state anchor.

- **Progress Model (Red):**  
  Contains the same table plus six newly added chairs.

- **Layered Progress Analysis:**  
  When overlaid, the table aligns perfectly across states, while the chairs appear as new geometry.

**Why this works much better:**
- The target remains consistently in frame
- Feature overlap is maximized
- Camera motion is smooth and predictable
- The spatial coordinate system remains stable

This confirms that the **geometric comparison engine is valid** when acquisition conditions are controlled.

---

## Phase 2B — Wall-Specific AI-Guided Depth Modeling

**Motivation:**  
Full 3D reconstruction is computationally expensive and fragile in live construction environments. Wall construction offers a unique opportunity: progress manifests primarily as **forward depth accumulation**.

### Method

1. **AI Frame Filtering**
   - A machine learning model isolates frames containing active wall construction
   - Removes irrelevant motion (workers walking, ceiling scans, floor pans)

2. **Monocular Depth Estimation**
   - Each frame is converted into a depth map
   - A depth-based point cloud is generated per frame

3. **Depth Difference Analysis**
   - Depth statistics are aggregated over time
   - Average depth change is tracked as a proxy for wall growth

### Results

- Early, middle, and late wall states show consistent forward depth displacement
- Average depth decreases as bricks block background regions
- A measurable ~22.4% depth change is observed from start to finish

**Why this is comparable to Phase 2:**
- The subject of interest (the wall) stays in frame
- The comparison variable (depth) is consistent
- Aggregation over many frames smooths noise

**Why this model is unique:**
- No full 3D reconstruction required
- Significantly lower computational cost
- Robust to messy, real-world footage
- Specifically optimized for wall construction progress

---

## Why Multiple Approaches?

Each approach answers a different question:

| Approach | Strength | Limitation |
|--------|---------|------------|
| Full SfM (Phase 1) | Rich geometric detail | Fragile in live environments |
| Controlled SfM (Phase 2) | Accurate change detection | Requires controlled capture |
| Depth Modeling (Phase 2B) | Robust, efficient | Task-specific (walls only) |

Rather than forcing a single method, the system adapts to **what the environment allows**.

---

## Suggested Repository Structure

.
├── data/
│   ├── raw_videos/
│   ├── frames/
│   └── point_clouds/
│
├── reconstruction/
│   ├── sfm_pipeline.py
│   ├── dense_refinement.py
│   └── alignment.py
│
├── depth_model/
│   ├── frame_filter.py
│   ├── midas_depth.py
│   └── depth_analysis.py
│
├── visualization/
│   └── render_pointclouds.py
│
├── results/
│   └── figures/
│
├── README.md
└── requirements.txt

---

## Key Technologies

- COLMAP — Structure-from-Motion & Multi-View Stereo  
- Open3D — Point cloud processing, alignment, clustering  
- DBSCAN — Density-based spatial clustering  
- MiDaS — Monocular depth estimation  
- OpenCV — Video and frame processing  

---

## Limitations

- Live construction footage introduces unavoidable noise
- Full volumetric reconstruction is computationally expensive
- Depth-based modeling is specialized and not universal

These constraints informed the system’s multi-strategy design.

---

## Future Work

- Dense multi-view stereo (MVS) integration
- ICP-based volumetric change measurement
- Capture guidance tools for construction crews
- Real-time progress dashboards

---

## TL;DR

**You don’t need LiDAR to track construction progress.**  
You need geometry, consistency, and models designed for the environment you’re actually in.
