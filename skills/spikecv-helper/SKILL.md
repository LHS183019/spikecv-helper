---
name: spikecv-helper
description: A helper skill for AI Agents to interact with the SpikeCV repository, typically answering questions about SpikeCV and executing tasks for SpikeCV, an ultra-high-speed spike camera vision framework. Use when the user asks about spike cameras, SpikeCV, spike dataset download (motVidarReal2020), running tracking (snn_tracker), reconstruction (TFSTP) through spike vision, etc.
---

# SpikeCV Agent Skill

*last updated: 2026-04-25*

This document is designed to equip an AI Agent (or developer) with the necessary prior knowledge and instructions to answer questions and execute tasks within the SpikeCV repository.

Any command execution or conceptual question should be informed by the context in this document and the attached references. **If you are unfamiliar with SpikeCV concepts (e.g., what is a spike camera), you should read `references/About_SpikeCV.md` first.**

## When to use this Skill
+ Answering questions about SpikeCV's functionalities, algorithms, usage and community.
+ Tasks using the spikcv CLI, includes:
  + Downloading spike datasets 
  + Running processing spikecv algorithms (reconstruction, tracking, etc.) through the CLI interface.

## When NOT to use this Skill
+ For general coding questions unrelated to SpikeCV
+ For questions about other repositories or domains
+ Coding tasks that require deep understanding of SpikeCV's internal implementations (e.g., modifying core algorithms, adding a new reconstruction method) — the Agent should defer to the user or suggest consulting the development team.

## Overview

The skill is structured as follow:
```
spikecv-helper
├── SKILL.md  # current doc (you are now here)
└── references/
   ├── About_SpikeCV.md      # come here if current doc is not enough to answer questions about SpikeCV
   └── install_miniconda.md  # step-by-step guide for Conda environment setup
``` 

Content in this document is organized as follows:

SKILL.md
0. [How to Navigate This Skill](#navigation)
1. [Quick Start: What is SpikeCV? (Prior Knowledge)](#start)
2. [Instructions: Installation & Setup](#installation)
3. [Instructions: Task Execution Guide](#task-execution)
4. [spikecv CLI Usage (Command Line Interface)](#cli-usage)
5. [Project Structure (Prior Knowledge)](#project-structure)
6. [Documentation Directory](#documentation)
7. [Common Questions](#common-questions)
8. [SHOULD NOT DO](#should-not-use)

<a id="navigation"></a>

## How to Navigate This Skill

Navigate to [SHOULD NOT DO](#should-not-use) to understand the boundaries of this skill and avoid potential pitfalls. 

Use this quick routing table before searching across sections.

| Question Type | Primary Location |
|---|---|
| What is SpikeCV / what is a spike camera | [Quick Start](#start) and `references/About_SpikeCV.md` |
| Algorithm list and module locations | [Project Structure](#project-structure) |
| Algorithm principles, method details, or paper-level references | [Documentation Directory](#documentation) |
| CLI command patterns and parameters | [spikecv CLI Usage](#cli-usage) |
| Downloading datasets | [spikecv CLI Usage](#cli-usage) |
| Running tracking or reconstruction | [Task Execution Guide](#task-execution) and [spikecv CLI Usage](#cli-usage) |
| Team contact, contribution, publications, hardware acquisition | `references/About_SpikeCV.md` |

you can always check out  [Task Execution Guide](#task-execution) for step-by-step instructions on how to execute common tasks like answering questions, downloading datasets and running algorithms. And [common questions](#common-questions) if you find something confusing.

<a id="start"></a>

## Quick Start: What is SpikeCV?
SpikeCV is an open-source visual framework for ultra-high-speed Spiking Cameras. It provides solutions for data loading (spatiotemporal spike streams typically stored in `.dat` files), processing algorithms (filtering, reconstruction, optical flow, depth estimation, detection, and tracking), and hardware interfaces. For more details, please refer to the `./references/About_SpikeCV.md` file, which contains a comprehensive introduction to SpikeCV, its architecture, resources and links, and how to cite it in your research.

Note: For algorithm families and module-level availability, use [Project Structure](#project-structure).
Note: For algorithm principles, mathematical details, and paper-related references, use [Documentation Directory](#documentation).

<a id="installation"></a>

## Instructions: Installation & Setup

### Step 1: Prerequisites
- Python 3.10 or higher
- An virtual environment for spikecv(highly recommended)
  - We suggest using Anaconda/Miniconda environment. See [install_miniconda.md](references/install_miniconda.md) for detailed installation instructions.

### Step 2: Install SpikeCV
```bash
git clone https://github.com/Zyj061/SpikeCV # Clone the repository if you haven't already had it locally, you can ask the user to provide the local path if they already have it.
cd SpikeCV
pip install .[cli]
```
*Note: Python >= 3.10 is required. An Anaconda environment is highly recommended.*

You can also install optional dependencies for specific functionalities (e.g., tracking):

```bash
pip install .[tracking]
```

for more details on optional dependencies, please refer to `SpikeCV/.github/CONTRIBUTING/pyproject_usage.md` and `SpikeCV/.github/CONTRIBUTING/dependency_guide.md`.


### Step 3: Check Installation
Verify that the `spikecv` command is available:
```bash
spikecv --help
```

### Step 4: (Optional) Test with Examples

A throughout health check can be done by running the data downloading and processing reconstruction algorithm to ensure everything is set up correctly.

Run the following command:
```bash
spikecv data download --dataset recVidarReal2019 -agent
cd datasets/
spikecv proc reconst -agent
```

It might take a while to download the dataset and run the reconstruction algorithm. After completion, you should see JSON output with paths to the downloaded data and the reconstructed video, respectively.

<a id="task-execution"></a>
## Instructions: Task Execution Guide

### Before answering algorithm questions:
1. Check [Project Structure](#project-structure) first for algorithm families and module locations.
2. Check [spikecv CLI Usage](#cli-usage) for runnable command forms.
3. Check [Documentation Directory](#documentation) for algorithm principles, method-level explanations, and paper-related references.
4. Use `references/About_SpikeCV.md` for conceptual background, official publications links, contribution, and contact information.

### When the user asks to download data:
1. Check if `spikecv` CLI is installed by running `spikecv --help`. If not, follow installation steps in [Section 2](#installation).
2. Run: `spikecv data download --dataset <name> --local-dir <path> --agent-used`
3. Parse the JSON output. The `result` field contains download paths. Report success/failure to the user.

### When the user asks to run tracking:
1. Check if `spikecv` CLI is installed by running `spikecv --help`. If not, follow installation steps in [Section 2](#installation).
2. Ensure dataset is downloaded (check if `--data-path` exists).
3. check out the CLI usage in [next section](#cli-usage) for the tracking command and parameters.
4. **Construct and execute command using the `spikecv proc track` CLI** with appropriate `--scene-idx` and `--metrics` if requested. Do NOT run standalone script in `SpikeCV/examples/`.
5. Always include `--agent-used` flag to get JSON output.
6. After execution, parse JSON and present key results: tracking result file path, video paths, and metrics (if enabled).
7. If metrics are present, highlight MOTA and IDF1 values.

### When the user asks to run reconstruction:
1. Check if `spikecv` CLI is installed by running `spikecv --help`. If not, follow installation steps in [Section 2](#installation).
2. Ensure dataset is downloaded (check if `--dat-file-path` and its corresponding `config.yaml` exist).
3. check out the CLI usage in [next section](#cli-usage) for the reconstruction command and parameters.
4. **Construct and execute command using the `spikecv proc reconst` CLI** with the default `tfstp` algorithm or specified one. Do NOT run standalone script in `SpikeCV/examples/`.
5. Parameters like `--begin-idx`, `--block-len` can be adjusted based on user needs. Default is 500 and 1500.
6. Always include `--agent-used` flag to get JSON output.
7. After execution, parse JSON and present key results: `reconstructed_video_file` path and `reconstructed_images_shape`.

### When answering conceptual or community questions:
1. For architecture, contact, contribution, hardware acquisition, or publications: read `references/About_SpikeCV.md` first.
2. For algorithm-list questions: read [Project Structure](#project-structure) first, then supplement with `references/About_SpikeCV.md` and [Documentation Directory](#documentation) if needed.
3. For algorithm principles, method comparisons, and paper-level explanations: read [Documentation Directory](#documentation) first.
4. **NEVER** provide external commercial links or guess contact information not present in the provided reference files.
5. For API-level coding details, suggest the user look at the official documentation mentioned in the [Documentation Directory](#documentation) of this document or source code in `spikecv/` directory.

### When something goes wrong:
+ If a command fails with a "Path not found" error, ensure you are running the command from a correct root and provided an correct (relative) paths. 
+ Make sure the dataset is designed for current task (e.g., `motVidarReal` for tracking and `recVidarReal` for reconstruction) and is properly downloaded and the `--data-path` points to the correct location.
+ Make sure the parameters you provided are valid (e.g., `--scene-idx` should be between 0-6 for tracking, `--data-path` should point to the correct location).

<a id="cli-usage"></a>

## spikecv CLI Usage (Command Line Interface) 

SpikeCV provides the `spikecv` command for wrapper interactions. After installation, you can use it to download datasets and run processing algorithms. Currently, the CLI supports the following commands:
- `spikecv data download`: Download datasets from the OpenI platform.
- `spikecv proc track`: Run multi-object tracking algorithms on spike data.
- `spikecv proc reconst`: Run reconstruction algorithms on spike data.

### 4.1 Downloading Datasets
Use the `data` subcommand to download datasets from the OpenI platform:
```bash
spikecv data download --dataset motVidarReal2020 --local-dir datasets/ --agent-used
```
- `--dataset`: Currently supports `motVidarReal2020` (tracking data), etc.
- `--local-dir`: Target local directory.
- `--agent-used`: (Flag) Recommended for Agents. Returns a JSON-formatted status and result (e.g., download paths).

### 4.2 Running Algorithms
Use the `proc` subcommand to run processing modules. 

#### Multi-object Tracking (SNNTracker)
```bash
# Recommended usage (using default algorithm snn-tracker)
spikecv proc track --scene-idx 0 --metrics --agent-used

# Explicit usage (using algorithm name or alias)
spikecv proc track snn-tracker --scene-idx 0 --agent-used
```
- `track`: Tracking command group. Runs `snn-tracker` by default if no algorithm is specified.
- `--scene-idx`: Index of the test scene (0-6).
- `--metrics`: (Optional) Calculates MOT metrics (requires GT labels).
- `--agent-used`: Returns JSON result with file paths and metrics.

#### Spike Reconstruction (TFSTP) 
```bash
# Recommended usage (using default algorithm tfstp)
spikecv proc reconst --dat-file-path recVidarReal2019/classA/car-100kmh.dat --agent-used

# Explicit usage
spikecv proc reconst tfstp --dat-file-path recVidarReal2019/classA/car-100kmh.dat --agent-used
```
- `reconst`: Reconstruction command group. Runs `tfstp` by default if no algorithm is specified.
- `--dat-file-path`: Path to the `.dat` spike file.
- `--yaml-file-path`: Path to the `.yaml` dataset configuration file.
- `--begin-idx`: Starting frame (default 500).
- `--block-len`: Number of frames to process (default 1500).
- `--stp-d`, `--stp-f`: STP model parameters for motion adaptation.
- `--agent-used`: Returns JSON result with reconstruction video path.

Note: If a command fails with a "Path not found" error, ensure you are running the command from a correct root and provided an correct (relative) paths.

> **Important**: Always include the `--agent-used` flag when executing CLI commands through the Agent. This ensures that the output is returned in a structured JSON format, which the Agent can parse to extract relevant information (e.g., file paths, metrics) to report back to the user.

> **Note**: Feel free to explore other CLI options and parameters as needed. The above examples are common use cases, but the CLI may support additional features or algorithms. Always refer to `--help` for the most up-to-date command options and usage instructions.

> **Cross-reference**: If the user asks why an algorithm works, its principles, or paper background, navigate to [Documentation Directory](#documentation) instead of staying only in CLI examples.

<a id="project-structure"></a>

## Project Structure (Prior Knowledge) 

The structure tree of `SpikeCV` platform is as follows. The Agent should be familiar with the directory layout and key files to efficiently navigate the repository when answering questions or executing tasks.:

```
SpikeCV/
├── .github/
│   └── CONTRIBUTING/
│       ├── dependency_guide_en.md
│       ├── dependency_guide.md
│       ├── pyproject_usage_en.md
│       └── pyproject_usage.md
├── SpikeCV/
│   ├── cli/
│   ├── device/
│   ├── examples/
│   ├── metrics/
│   ├── spkData/
│   ├── spkProc/
│   ├── utils/
│   ├── visualization/
│   ├── __init__.py
│   └── __main__.py
├── docs/
│   └── source/
├── pyproject.toml
├── README.md
├── README_en.md
├── CONTRIBUTING.md
├── CONTRIBUTING_en.md
└── LICENSE
```

When searching for files or implementing features, the Agent should understand the repository layout:

- `SpikeCV/spkData/`: Contains data loaders.
  - `load_dat.py`: parses `config.yaml` to match `.dat` raw files and `.txt`/gt label files, returning a `paraDict`. Includes the `SpikeStream` class for slicing block spikes.
- `SpikeCV/spkProc/`: Core algorithm implementations. 
  - `filters/`: Includes STP Filter (`stp_filters_torch.py`) to adaptively filter out static background spikes.
  - `reconstruction/`: Texture reconstruction algorithms (TFI, TFP, TFSTP, SSML).
  - `tracking/`: Multi-object tracking (SpikeSORT, SNNTracker).
  - `detection/`: Object detection including attention selection and clustering-based methods (STDP, Motion).
  - `recognition/`: Spike-based action and object recognition (rpsnet, svm, vgg).
  - `depth_estimation/`: Depth from spikes, including SpikeT (Transformer-based S2DepthNet).
  - `optical_flow/`: SCFlow for estimating motion.
  - `augment/`: Data augmentation techniques for spike data (spatial/temporal transformations).
- `SpikeCV/tools/` & `SpikeCV/(metrics|visualization)/`: Utilities like `metrics` (PSNR, SSIM, AEPE, MOTA/IDF1) and `visualization` (`get_video.py`, `optical_flow_visualization.py`).
- `SpikeCV/cli/`: Typer-based CLI definitions (`proc.py`, `data.py`).
- `SpikeCV/examples/`: Contains standalone test scripts like `test_tfi.py`, `test_scflow.py`, `test_snntracker.py`.

<a id="documentation"></a>

## Documentation Directory
When looking for API usages, algorithm principles, method details, or paper references, the Agent should consult the local documentation instead of guessing.

This section is not API-only. It is a comprehensive entry for conceptual understanding, algorithm explanations, and reference materials.

**Online Docs**: [https://spikecv.readthedocs.io](https://spikecv.readthedocs.io)
**Local RST Docs**: Located in `SpikeCV/docs/source/`. Key reference files:
- `使用例子.rst`: Step-by-step examples of code integration for tracking, optical flow, and reconstruction.
- `数据加载.rst`: Data loading instructions, `DATA_LABEL_TYPE` dict explanations, and `paraDict` generation logic.
- `核心操作.rst`: Mathematical principles and APIs of filters (like `STPFilter`) and reconstruction algorithms (like `TFI`, `TFP`).
- `数据处理工具箱.rst`: Details on generating visualization videos from `.dat` matrices and measuring quantitative metrics.

<a id="common-questions"></a>

## Common Questions

Q: Where should I look for algorithm principles or paper-level explanations?
A: Start with [Documentation Directory](#documentation). If you need official publication links, then use `references/About_SpikeCV.md`.

Q: Where can I find the comprehensive publication list?
A: Use the official publications page linked in `references/About_SpikeCV.md`. That is the authoritative and comprehensive source.

Q: How should I answer contact or contribution questions?
A: Use only information from `references/About_SpikeCV.md` and avoid guessing external contact details.

<a id="should-not-use"></a>

## YOU SHOULD NOT DO
- **Do not hallucinate external resources**: If a user asks for contact info, purchasing, or contribution steps, you **must only** provide information found in `references/About_SpikeCV.md`. Do not suggest third-party companies (like Prophesee) or external researchers unless they are explicitly mentioned in the SpikeCV reference files.
- **Do not modify the SpikeCV codebase** or documentation directly. Instead, suggest contributions through the official channels outlined in `About_SpikeCV.md`.
- Do not attempt to answer questions about SpikeCV that require deep internal code understanding or modifications. Instead, suggest the user consult the development team using the contact info in `About_SpikeCV.md`.
- Do not execute commands that modify the user's system or environment without explicit permission (e.g., installing software, changing configurations).
- Do not provide speculative answers about SpikeCV's internal workings if the information is not available in the documentation or source code. Always refer to official resources for accurate information.
- If you can finish a task through `spikecv` CLI, stick with it. DO NOT use an example script directly instead. For example, if the user asks to run tracking, do not run `SpikeCV/examples/test_snntracker.py` directly. Instead, use the `spikecv proc track` CLI command, which is the officially supported interface for running tracking algorithms. This ensures that you are using the most up-to-date and compatible code paths, and it also allows you to leverage any improvements or bug fixes made to the CLI interface.