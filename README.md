# UMDSite
This tool processes construction site videos through the COLMAP Structure-from-Motion and Multi-View Stereo pipeline.

## The Problem

Construction progress is inherently spatial and temporal: structures are built incrementally in 3D space over time.

Yet the primary record of that progress is unstructured video. From this data alone, there is no direct way to:

- Quantify geometric change
- Compare site state across time
- Measure physical progress objectively
- Reason about volumetric or structural differences

Without an explicit spatial representation, progress assessment remains visual and subjective rather than geometric and measurable.

The core challenge is enabling reliable spatial reasoning about how a 3D environment evolves over time using only passive visual data.

## Our Solution
1. Accepts construction site video footage
2. Extracts frames from the start and end of the video
3. Uses COLMAP to reconstruct two 3D point clouds
4. Aligns the reconstructions
5. Computes geometric differences
6. Visualizes measurable construction progress

## How It Works

We leverage COLMAP’s Structure-from-Motion pipeline to:

- Extract 3D reconstructions
- Generate sparse point clouds
- Perform geometric alignment between time snapshots
- Compute spatial deltas to quantify progress

This transforms unstructured video into measurable 3D construction analytics.

## Demo

### Input
Construction site walkthrough video

### Output
- 3D reconstruction at T1
- 3D reconstruction at T2
- Highlighted geometry changes
- **Report with summary of changes and other important information**
