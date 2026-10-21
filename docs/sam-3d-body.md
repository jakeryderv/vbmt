# SAM 3D Body and Hugging Face

## What comes from where

SAM 3D Body's weights and associated assets are hosted on Hugging Face. Inference uses Meta's source repository and its Python dependencies. Installing `huggingface-hub` provides download tooling; it does not install the reconstruction model's implementation.

| Component | Source | Role |
| --- | --- | --- |
| Model weights and assets | [DINOv3 checkpoint](https://huggingface.co/facebook/sam-3d-body-dinov3) | Initial candidate for reconstruction |
| Alternative weights | [ViT-H checkpoint](https://huggingface.co/facebook/sam-3d-body-vith) | Alternative to evaluate if needed |
| Inference code | [Meta's repository](https://github.com/facebookresearch/sam-3d-body) | Model loading, estimation, and demos |
| Download client | [Hugging Face Hub](https://huggingface.co/docs/huggingface_hub/guides/cli) | Authentication and checkpoint retrieval |

## Download tooling

The `model-download` dependency group in `pyproject.toml` contains `huggingface-hub`; its resolved version is recorded in `uv.lock`. It is optional because downloading weights is a setup task.

Run these commands from the VBMT repository root:

```bash
uv sync --locked --group model-download
uv run --locked --group model-download hf --help
```

Request access through the checkpoint's Hugging Face page and review its SAM License. Once access is approved, authenticate locally:

```bash
uv run --locked --group model-download hf auth login
```

Choose a model commit from the checkpoint repository's history. Replace `<model-commit-sha>` below with that revision to make the download reproducible:

```bash
uv run --locked --group model-download hf download \
  facebook/sam-3d-body-dinov3 \
  --revision "<model-commit-sha>" \
  --local-dir checkpoints/sam-3d-body-dinov3
```

The checkpoint directory is ignored by Git. Authentication and weight downloads are separate from dependency installation; `uv sync` does not perform either. Keep authentication tokens outside the repository.

## Inference setup still to validate

Follow the upstream [installation guide](https://github.com/facebookresearch/sam-3d-body/blob/main/INSTALL.md) when preparing the inference environment. It currently demonstrates Python 3.11, a separately selected PyTorch build, additional Python packages, and Detectron2. MoGe and SAM3 are listed as optional components; the upstream demo's defaults also need to be considered when choosing which components to install.

VBMT currently selects Python 3.14 in `.python-version` and declares Python 3.12–3.14 support. That is the scaffold's configuration, not a validated compatibility claim for SAM 3D Body. Start the upstream smoke test in a separate Python 3.11 environment, then decide whether to align VBMT's Python version or retain a separate inference environment.

The upstream repository currently provides source-based setup without a root `pyproject.toml` or `setup.py`. A direct Git dependency is therefore not yet a complete installation strategy. The full inference stack has not been added to VBMT's dependencies.

Before integrating inference:

1. Select the target machine and verify its GPU/driver setup; choose a compatible PyTorch build using the [official installation instructions](https://pytorch.org/get-started/locally/).
2. Pin the upstream source commit and record the model revision, Python version, PyTorch build, and enabled detector/camera-estimation components.
3. Run the upstream demo on one image and inspect both the overlay and returned pose data.
4. Record the working environment here, then implement the VBMT integration and video baseline.

## Current status

The locked download-tooling environment was installed successfully, and the `hf`, `hf download`, and `hf auth login` help commands were checked. Model access, checkpoint downloads, and inference have not been tested. During setup inspection, `nvidia-smi` could not communicate with the NVIDIA driver in the current environment; GPU availability must be checked on the intended inference machine.
