# Video-based biomechanical motion tracking

A research project exploring markerless 3D motion measurement from single-camera basketball footage. The initial goal is to establish which movement measurements are reliable, under which recording conditions, and whether temporal constraints improve them.

## Research question

How reliably can pretrained monocular body reconstruction recover basketball movement measurements, and can temporal constraints improve that reliability?

## Initial scope

Start by evaluating reconstruction on short movement sequences with synchronized reference measurements, using one camera view as input. Use pretrained [SAM 3D Body](https://github.com/facebookresearch/sam-3d-body) for single-image body reconstruction, then investigate consistency across frames. Apply the validated workflow to controlled basketball footage afterward.

The initial work focuses on reconstruction evaluation, temporal processing, and measurement definitions. Multi-player tracking, moving cameras, broadcast footage, and training a new reconstruction model are outside the first experiment.

This repository is currently a project scaffold; the workflow below is planned.

See the [documentation index](docs/README.md) for [SAM 3D Body setup](docs/sam-3d-body.md) and the [data-selection and evaluation direction](docs/data-and-evaluation.md).

The planned tooling combines a reusable Python pipeline with a local web app for synchronized video, 3D motion, and measurement inspection. See the [architecture](docs/architecture.md) and [first implementation slice](docs/first-slice.md) for the proposed structure and starting plan.

## Proposed approach

1. **Frame-by-frame reconstruction:** Estimate body meshes and skeletal poses with SAM 3D Body. Preserve the original estimates as a baseline.
2. **Temporal consistency:** Associate the player across frames and evaluate a simple temporal baseline, such as smoothing, before adding more complex constraints. Track changes in body geometry and missing detections explicitly.
3. **Movement measurements:** Extract a small set of time series, initially candidate measures such as knee flexion and trunk inclination. Define anatomical landmarks, angle conventions, coordinate frames, units, and handling of missing or occluded observations before interpreting results.
4. **Evaluation:** Compare original and temporally processed estimates for consistency, measurement error where reference data exist, and preservation of movement timing and peaks.

## First experiment

- Inspect a small BioCV or OpenCap laboratory-validation subset before selecting the first benchmark. Confirm video/reference synchronization and compatible measurement definitions.
- Select a few movements and participants, document recording conditions, and reserve participants for evaluation before tuning refinements.
- Save per-frame reconstruction outputs and inspect overlays against the source video.
- Apply one simple temporal method and compare it with the unprocessed baseline.
- Plot the selected measurements alongside missing detections and visible reconstruction failures.
- Inspect whether reduced jitter comes at the cost of delayed events or flattened movement peaks.

The first deliverable is a reproducible comparison of the baseline and temporal method, including failure cases and limits on what the measurements support.

## Validation

A plausible mesh or smooth trajectory does not establish biomechanical accuracy. Evaluate three distinct properties:

- **Temporal consistency:** Jitter, body-shape variation, and gaps in tracking.
- **Measurement accuracy:** Agreement with an independent reference, such as synchronized calibrated views or motion capture, using compatible measurement definitions.
- **Movement preservation:** Whether processing retains event timing and peak values during actions such as takeoff and landing.

Choose the reference data and evaluation protocol early. Without an independent 3D reference, overlays and consistency checks support an exploratory assessment, not a claim of validated 3D accuracy. Absolute distances and court-relative trajectories also require explicit treatment of scale and camera geometry.

## Future directions

- Extend to multiple players, occlusions, and moving-camera footage.
- Explore synchronized multi-camera fusion to constrain reconstruction.
- Expand to limb trajectories, movement asymmetry, and gait-related features once the relevant measurements are validated.
- Investigate longitudinal movement patterns related to fatigue, overtraining, and injury risk as separate research questions requiring appropriate data and validation.
