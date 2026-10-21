# Architecture

Status: agreed direction as of 2026-10-21; implementation has not started. The package currently contains a CLI scaffold. Frameworks and detailed file schemas remain to be selected during the [first slice](first-slice.md).

## Purpose

Build a reusable Python motion-analysis pipeline with a local web app for inspecting recordings and experiments. Saved results connect processing, evaluation, and visualization so an experiment can be reproduced and reopened without a notebook session or a running model.

## Responsibilities

| Component | Responsibility |
| --- | --- |
| Python package | Dataset adapters, model interfaces, coordinate conversions, temporal processing, measurements, and evaluation. |
| Experiment definitions | Explicit dataset selections, inference views, model revisions, parameters, and evaluation protocols. |
| Saved runs | Predictions, measurements, metrics, configuration, provenance, and execution status. |
| Web app | Recording selection, synchronized video playback, 3D inspection, measurement plots, and run comparison. |

The CLI and web app use the same package operations. Scientific calculations belong in the package; the frontend handles presentation and interaction. Initial processing runs through the CLI, while the viewer reads saved results. Interactive job launching can follow later.

Keep these components in one repository initially. Introduce modules as functionality arrives:

```text
src/vbmt/
    data/          # Dataset adapters and shared recording types
    models/        # Reconstruction model interfaces
    processing/    # Temporal refinements and related operations
    measurements/  # Joint angles, trajectories, movement events
    evaluation/    # Metrics and benchmark execution
    runs/          # Saved results and provenance
    api/           # Local interface used by the viewer
web/               # Viewer frontend
experiments/       # Versioned configurations and small entry points
tests/
docs/
```

Downloaded data, checkpoints, and generated runs live outside tracked source files. Keep paths configurable and avoid copying large source recordings into every run.

## Recordings and saved runs

A recording describes a participant's trial, its camera streams, calibration, timing, and available reference measurements. A run applies a specified method to selected inputs from that recording. Multiple runs may share the same source data.

Start with a versioned manifest plus separate numerical and media files. Select concrete formats after inspecting the first sample. The contract should capture:

- Dataset/release, participant, trial, camera identifiers, and source-file identity or checksums.
- Timestamps, time offsets, frame conventions, units, coordinate frames, and calibration.
- Inference inputs separately from evaluation references and additional inspection views.
- Model/checkpoint revision, code revision and local-change state, environment details, configuration, and preprocessing.
- Landmark definitions, reference mappings, validity masks, missing estimates, and any available model confidence values. Confidence does not imply calibrated uncertainty.
- Predictions, derived measurements, evaluation settings, metrics, and contributing sample counts.
- Run identity, status, and failure details. Partial or failed results must not appear complete.

Preserve original predictions. Refinements produce separate runs linked to their parents. Reference measurements, model estimates, manual annotations, and corrections remain distinguishable, with their origins recorded. Completed results should not change silently when reopened or rescored; a new evaluation records its own settings and identity.

Heavy inference may use a separate environment or process. Exchange configuration and saved artifacts across that boundary so the viewer can operate without importing the SAM stack. The [SAM setup note](sam-3d-body.md) records the current environment uncertainty.

## Interactive inspection

The viewer should eventually provide synchronized camera panels, a freely orbitable 3D skeleton or mesh, reference/prediction overlays, measurement curves, and comparisons between runs. Clicking a curve or error peak should seek to the corresponding instant.

Use a shared time axis with explicit stream offsets. Frame stepping follows a selected source stream and selects corresponding samples in the others. Record sampling/interpolation rules and show unavailable intervals rather than silently substituting data. Browser playback is an inspection aid; evaluation uses the original numerical timestamps.

Recorded camera views and virtual viewpoints of the reconstruction should be clearly identified. A body-relative overlay must also be distinguishable from a world-space reconstruction. If playback proxies are needed, preserve their mapping to source timestamps and pixels, including any resize or crop transformations.

## Testing and benchmarks

| Layer | Purpose |
| --- | --- |
| Software tests | Verify transformations, units, timing, landmark mapping, angle calculations, missing-data behavior, and artifact compatibility using known expected results. |
| Small pipeline checks | Exercise loading, reconstruction, saving, reopening, and evaluation on a known sample. GPU checks are explicit rather than part of every lightweight test run. |
| Scientific benchmarks | Measure method performance across held-out participants, movements, views, and difficult cases. Preserve coverage and failures alongside accuracy. |

Benchmark execution and metrics belong in the package. Experiment configurations select the protocol; saved runs retain its results. The viewer presents those results without recomputing a different version of the metric. Follow the split and reference requirements in [data and evaluation](data-and-evaluation.md).

## Potential research outputs

Outputs may include reconstructed motion, derived measurements, curated benchmark subsets, reviewed annotations, and refinement-model checkpoints. Every export should retain source lineage, measurement definitions, version information, split membership, and applicable source terms. Predictions remain predictions when exported; they do not automatically become ground truth.

Add dataset or model release tooling when a concrete output warrants it. A model registry, remote job service, hosted multi-user app, and training framework are not prerequisites for the first experiment.
