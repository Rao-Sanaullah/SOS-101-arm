# Asynchronous VLM-Supervised Selective Grasping

Adding language-conditioned object selection to a **frozen** imitation policy — no retraining, no extra demonstrations. 
A vision-language model runs in parallel as a high-level supervisor and decides *whether* to act; a 20-demo ACT policy decides *how*.

<!-- optional: project page / paper / model / dataset badges -->
[![Project Page](https://img.shields.io/badge/project-page-2e8b4a)](<https://rao-sanaullah.github.io/SOS-101-arm/>)
<!--[![Paper](https://img.shields.io/badge/paper-PDF-333)](<PAPER_URL>)
[![Model](https://img.shields.io/badge/🤗-model-ffcc33)](<HF_MODEL_URL>)
[![Dataset](https://img.shields.io/badge/🤗-dataset-2f5c9e)](<HF_DATASET_URL>)-->



<table align="center">
<tr>
<td width="33%" align="center">
<video src="ATTACHMENT_URL_1" controls muted width="100%"></video>
<br><sub><b>Selective grasp</b><br>picks green among three, ignores the rest</sub>
</td>
<td width="33%" align="center">
<video src="ATTACHMENT_URL_2" controls muted width="100%"></video>
<br><sub><b>Selective hold</b><br>picks green, ignores a new non-green marker</sub>
</td>
<td width="33%" align="center">
<video src="ATTACHMENT_URL_3" controls muted width="100%"></video>
<br><sub><b>Three colors</b><br>Green, then the other two colors requested in turn</sub>
</td>
</tr>
</table>
<!--   -->
---

## Overview

An Action Chunking Transformer (ACT) policy reproduces a trained skill but has no notion of *which* object to act on — trained to pick an object, it grasps whatever is in front of it. Instead of making selection part of the policy (which needs large, instruction-annotated datasets), we keep the small policy **fixed** and delegate object selection to a VLM supervisor.

The two run **asynchronously**:

- A **30 Hz control loop** runs the ACT policy and owns the camera and arm.
- A **separate thread** queries the VLM (Cosmos3-Nano) on shared frames at ~1–2 Hz and updates a `target-present` flag.
- A **non-blocking gate** lets the policy act only when the requested target is present — so high-latency reasoning never stalls real-time control.

The same channel adds run-time retargeting, mid-task abort with return-to-home, and an optional shared-autonomy mode (human pause/take-over and online action blending).

## Key results

| Setting | Trials | Success | Transport slip | Grasp slip | Wrong color | Selection acc. |
|---|:--:|:--:|:--:|:--:|:--:|:--:|
| ACT only (baseline) | — | non-selective — grasps any color | | | | — |
| Single target | 50 | 27 (54%) | 10 (20%) | 13 (26%) | **0** | — |
| 3-color select | 25 | 13 (52%) | 7 (28%) | 5 (20%) | **0** | **100%** |

**VLM supervisor detection** (50 held-out frames, 25 present / 25 distractor-only): precision, recall, F1, accuracy all **1.00** — zero false activations on blue/pink distractor frames.

Every failure was a *physical* grasp slip on the upright markers, **not** a selection error. All selectivity comes from the supervisor at zero additional data cost — the policy is trained from only **20 demonstrations (~6 min of teleoperation)**.


## Hardware

- **Arm:** SO-ARM101 (WowRobo build of the LeRobot SO-101), 6-DoF, Feetech STS3215 servos, wrist RGB camera (640×480 @ 30 fps)
- **Compute:** single workstation with an NVIDIA RTX 5090
- **Supervisor:** NVIDIA Cosmos3-Nano

## Model & dataset

- **Trained ACT policy** — frozen policy from 20 demonstrations, with LeRobot pre/post-processors: `<HF_MODEL_URL>`
- **Demonstration dataset** — 20 episodes, 10,566 frames @ 30 fps, 640×480 wrist camera + 6-DoF state, LeRobot format: `<HF_DATASET_URL>`

<!--   
## Code

Code release coming soon. ⭐ Star/watch this repo for updates.-->

## Citation

```bibtex
@inproceedings{sanaullah2026vlmsupervised,
  title     = {Asynchronous Language-Conditioned Object Selection for
               Imitation Policies via a VLM Supervisor},
  author    = {Sanaullah},
  booktitle = {Preprint},
  year      = {2026}
}
```

## Acknowledgements

Built on [LeRobot](https://github.com/huggingface/lerobot) (ACT, SO-101) and NVIDIA Cosmos.
