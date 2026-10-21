# video-based biomechanical motion tracking

Goal: Develop a high-fidelity, markerless 3D human motion tracking system using basketball footage, with future applications in gait analysis, fatigue detection, and injury-risk research.

Core Approach:

1. 3D Reconstruction: Use Meta's [SAM 3D Body](https://github.com/facebookresearch/sam-3d-body) to reconstruct detailed 3D body meshes and skeletal poses from single-camera video.
2. Temporal Tracking: Track players across frames, maintaining consistent identities, body geometry, and smooth 3D motion.
3. Biomechanical Analysis: Extract structured time-series measurements, including joint angles, limb trajectories, movement asymmetry, and gait-related features.
4. Validation: Evaluate reconstruction accuracy, temporal consistency, and biomechanical reliability.

Future Extensions:

- Multi-camera fusion: Combine synchronized camera views to improve 3D reconstruction accuracy and reduce depth ambiguity.
- Sports health research: Analyze longitudinal movement patterns for potential indicators of fatigue, overtraining, and injury risk.

Initial Scope: Single-camera basketball footage using pretrained SAM 3D Body, focusing on improving temporal consistency and biomechanical fidelity rather than developing a new reconstruction model from scratch.
