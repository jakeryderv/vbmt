# First implementation slice

Status: proposed plan as of 2026-10-21. This is an implementation outline, not a record of completed work. See the [architecture](architecture.md) and [data-selection rationale](data-and-evaluation.md).

## Outcome

Open one real recording in a local web app, inspect two synchronized camera views and a 3D skeleton, and compare a saved monocular SAM baseline with a compatible reference measurement on a linked timeline.

One view supplies inference. The other view is available for inspection; independent reference data supply evaluation. Start with one participant and trial for integration, then repeat on another trial to check that the workflow is not hard-coded. This proves the pipeline works, not that it generalizes across participants.

## 1. Inspect a sample and choose the environment

Inspect access and sample availability for BioCV first, with OpenCap laboratory-validation data as the alternative. Choose based on usable paired video/reference data, movement relevance, access, and download size. Do not implement both adapters initially.

Confirm timestamps, calibration, reference definitions, missing-data notes, and dataset terms. Select a short jump or squat sequence where available. Document the selected release and files, and designate these trials as development data before adjusting methods.

In parallel with data inspection, validate a SAM environment on the intended compute machine. Confirm model access and run one-image inference using pinned source and checkpoint revisions. Resolve the Python/PyTorch/GPU configuration described in [setup](sam-3d-body.md).

Decide and record how the person is selected or segmented. The upstream demo's detector/crop behavior must not be mistaken for a completed VBMT segmentation implementation. Any supplied crop or mask used for an initial smoke test must be identified as such.

Completion evidence: a sample inventory with timing/reference details, a selected environment, and an inspected real one-image inference result.

## 2. Implement the recording and run contracts

Implement one dataset adapter that preserves original data while exposing streams, timing, calibration, and reference arrays through shared types. Add a versioned saved-run manifest and numerical output files with explicit units, frames, landmark definitions, and validity.

Support writing, validating, and reopening a run. Keep the original model outputs available alongside normalized representations. A run must identify which camera was used for inference and which files are references.

Completion evidence: import and reopen the selected trial with matching source identities, timestamps, array dimensions, and missing-data indicators. Malformed or incomplete artifacts are reported clearly.

## 3. Produce one baseline and evaluate one measurement

Run frame-by-frame SAM inference on the selected view and save the unmodified predictions. Implement the minimal CLI path for importing, processing, and evaluating a trial; choose exact command names during implementation.

Choose one compatible position measure and one angle curve only after inspecting the landmark mapping. A geometric hip-knee-ankle angle is a candidate; label it as a geometric angle unless equivalence to the reference's anatomical flexion convention is established. Do not compare it directly with an incompatible OpenSim or other model parameter.

Record temporal resampling and spatial alignment. Begin with angle error and peak difference where supported, plus mapped position error and valid-sample coverage. Keep metric definitions explicit and do not hide missing estimates. Additional event/velocity metrics can follow once their reference and sampling requirements are established.

Completion evidence: saved predictions, reference mapping, metric definitions, curves, coverage, and a repeatable evaluation of those saved predictions without rerunning inference.

## 4. Build the local viewer

Use a thin local Python API and browser frontend; select frameworks after confirming the sample's playback and 3D requirements. Keep inference outside the request/render loop.

The first screen needs:

- Selection of a saved recording/run.
- Two source-video panels with a shared timeline, seek, play/pause, and frame stepping.
- An orbitable skeleton view with distinct prediction and reference display where mappings permit.
- One measurement plot and its saved error/coverage summary, linked to the same timeline.
- Clear indication of missing data, incomplete runs, and any time or spatial alignment applied.

Mesh rendering, side-by-side method comparisons, annotation editing, and launching jobs from the UI can follow. The first screen should work with saved outputs after the inference process exits.

Completion evidence: inspect the real trial at the start, middle, end, and any gaps; seek from the plot; verify displayed sample times against the recording manifest; close and reopen the viewer successfully.

## 5. Validate and record the baseline

Use focused tests with known geometry and timestamps for conversions, angle calculation, synchronization, validity handling, and saved-run compatibility. Include unequal sampling rates or offsets so synchronization tests exercise more than identical frame indices. Inspect the viewer in a browser using the actual saved sample.

Repeat the workflow on a second development trial. Record commands, versions, observed failures, and a compact baseline report. The slice is complete when real inference, reference evaluation, and interactive inspection all work together. Synthetic fixtures may support software tests but cannot substitute for that result.

## Boundaries and next step

This slice does not establish a full scientific benchmark or implement temporal refinement, model training, multi-player tracking, multi-camera fusion, or a public hosting service. These remain subsequent work.

Afterward, freeze a broader participant split and baseline protocol, then choose one refinement based on observed errors. Reuse the same run and evaluation interfaces to compare it on held-out participants.

The immediate unresolved choices are the accessible sample, the inference machine, and the first compatible measurement. Resolve them through the initial inspection and smoke test; no larger architecture decision is needed before starting.
