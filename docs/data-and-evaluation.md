# Data selection and evaluation

Status: proposed direction following discussion on 2026-10-21. Dataset documentation has been reviewed; archives have not been downloaded or validated, and the first benchmark has not been selected.

## Direction

Evaluate monocular 3D motion reconstruction and measurement reliability before applying the workflow to basketball games. Prioritize continuous movement sequences with synchronized, independent reference measurements. Basketball remains the intended application, but the first benchmark can use other movements.

## Inputs and evaluation references

The planned pipeline produces its own person segmentation and SAM 3D Body pose estimates. Dataset bounding boxes need not be inputs. Reference poses remain useful for evaluating those predictions, provided their definitions can be reconciled.

Before comparing outputs, establish correspondences for anatomical landmarks or joint centres, coordinate frames, units, scale, and timestamps. A matching joint name alone does not establish an equivalent measurement. SAM pose parameters also should not be assumed to equal a biomechanical model's joint angles.

Useful reference data include continuous marker or joint trajectories, documented angle conventions, camera calibration, and movement events such as foot contact or takeoff. Action categories help organize results but do not establish reconstruction accuracy. Force measurements can support event timing; evaluating forces or joint moments would require additional modeling beyond the initial pose benchmark.

## Candidate datasets

| Dataset | Proposed role | Reference strengths and limitations |
| --- | --- | --- |
| [BioCV](https://www.nature.com/articles/s41597-024-04077-3) | First benchmark candidate | Nine synchronized cameras at 200 Hz, marker-based capture, force plates, and events for walking, running, jumping, and hopping. Primary trials contain paired references; separate markerless trials have video only. Access requires a request. |
| [OpenCap laboratory validation](https://github.com/opencap-org/opencap-core#reproducing-results-from-the-paper) | First benchmark candidate | Video and laboratory measurements for walking, squats, sit-to-stand, and drop jumps. Use the laboratory reference as the evaluation target; OpenCap predictions can be a separate baseline. |
| [AthletePose3D](https://github.com/calvinyeungck/AthletePose3D) | More demanding athletic movement | Video and motion data for 12 sports motions, with published kinematic evaluation. Inspect reference-generation details and obtain the corrected release before selecting it. |
| [SportsPose](https://christianingwersen.github.io/SportsPose/) | Dynamic movement and viewpoint testing | Multiview markerless reference poses with temporal processing. Their accuracy was assessed against marker-based capture on a separate evaluation set; do not treat every released sequence as independent marker-based ground truth. Academic access request required. |
| [SportsMOT](https://github.com/MCG-NJU/SportsMOT) | Later game-footage tracking evaluation | Player boxes and identities support association and tracking evaluation, but do not provide a 3D biomechanics reference. |
| [SportCenter](https://www.epfl.ch/labs/cvlab/data/sportcenter-dataset/) | Later basketball evaluation | Calibrated basketball views and some triangulated manual pose labels. Sparse manual references and multi-player scenes make it a lower priority for the first temporal measurement benchmark. |

SportCenter's lower priority reflects reference coverage and scene complexity, rather than incompatibility with our segmentation approach. SportsMOT likewise serves a different evaluation purpose from a biomechanics dataset.

## Proposed first benchmark

1. Inspect a small BioCV or OpenCap sample for access terms, download size, video quality, calibration, synchronization, reference coverage, and landmark definitions.
2. Select a few movements and participants. Use one camera per inference run; retain the other measurements for evaluation. Keep all trials and views of a participant in the same split, respecting official splits where provided.
3. Save the unmodified SAM estimates and establish a baseline before choosing refinements. Record model/source revisions and all preprocessing.
4. Compare compatible joint positions and angle curves, peak magnitudes, and event timing. Report missing estimates and failures alongside errors, broken down by movement and viewpoint.
5. Choose a small refinement based on observed failures, then compare it against the baseline on held-out participants. Candidates include stable body shape, temporal constraints, or foot-contact constraints; none is selected yet.

Document any spatial alignment used for scoring and what errors it removes. Avoid treating frame-wise aligned pose accuracy as evidence of accurate absolute trajectories. Preserve movement peaks and timing when evaluating smoothing; reduced jitter alone is insufficient.

## Before implementation

The sample inspection must settle the reference-to-SAM mapping, synchronization procedure, first measurements, and split protocol. For BioCV, use the updated calibrations and consult the supplied missing-marker notes. Record filtering and gap filling in the reference as well as our predictions, since both affect temporal comparisons.

The first deliverable is a reproducible baseline report with overlays, measurement curves, errors, and failure cases. Basketball footage follows once that report establishes what can be measured reliably.

The [first implementation slice](first-slice.md) establishes the processing and interactive inspection workflow on development trials before expanding to this scientific benchmark. The [architecture](architecture.md) describes how benchmark execution and saved results fit into the package and viewer.
