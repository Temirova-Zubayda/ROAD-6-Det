# ROAD-6-Det & ROAD-DETR

**Learning to Expect the Unexpected: Benchmarking and Detecting Unexpected Road Hazards**

Official code and dataset for the paper published in *Mathematics* 2026, 14(19), 3543.

[![Paper](https://img.shields.io/badge/Paper-MDPI%20Mathematics-blue)](https://doi.org/10.3390/math14193543)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

Temirova Zubayda Okmirzaevna, Shehzad Ali, Rafał Scherer, Arslan Munir, Khan Muhammad

VIS2KNOW Lab, Sungkyunkwan University · Częstochowa University of Technology · Florida Atlantic University

---

## Overview

Unexpected road hazards are irregular in appearance, vary widely in scale, and often appear in rain, fog, or low light. Most road-scene datasets focus on common traffic participants, so these hazards are underrepresented. This repository provides:

- **ROAD-6-Det**: a detection benchmark with bounding-box annotations for six unexpected road hazard categories, extending the recognition-only [ROAD-6](https://doi.org/10.1145/3731715.3733487) dataset.
- **ROAD-DETR**: a difficulty-aware, transformer-based detector built on RF-DETR, with three task-specific modules:
  - **HMSFM** (Hazard-Aware Multi-Scale Fusion Module): aggregates hazard cues across feature scales.
  - **DAFEM** (Difficulty-Aware Feature Enhancement Module): reweights channel and spatial responses for degraded or low-contrast hazards.
  - **HQRM** (Hazard Query Refinement Module): adapts transformer object queries using scene-level hazard context.
  - Plus a **confidence estimation branch** trained on IoU-based targets for better calibration.

## ROAD-6-Det Dataset

| Property | Value |
|---|---|
| Images | 33,784 |
| Bounding boxes | ~50,000 |
| Categories | 6 |
| Original / augmented images | 50.15% / 49.85% |
| Annotation | Manual, axis-aligned bounding boxes, verified in Roboflow |
| Split (train / val / test) | 70 : 20 : 10 |

**Hazard categories**

| Class | Description |
|---|---|
| Accident Vehicle | Vehicle involved in an accident that disrupts traffic flow |
| Fallen Tree | Trees or large branches blocking part or all of the road |
| Road Debris | Tires, vehicle fragments, construction materials, or other obstacles |
| Oil Spill | Liquid contamination that may reduce tire traction |
| Surface Water | Water accumulation from rain, flooding, or drainage issues |
| Icy Road | Ice-covered surfaces that raise skidding risk |

Original images and their augmented variants are kept in the same partition, so no augmented copy of a training image appears in validation or test.

### Download

- **Dataset (Google Drive):** [ROAD-6-Det download](https://drive.google.com/drive/folders/1-s2cI2SgxsLAt4tOg6iLgTW_tto8QcPS?usp=drive_link)
- Format: `<YOLO / COCO / other>` *(TODO: fill in)*

To download from the command line, you can use [`gdown`](https://github.com/wkentaro/gdown):

```bash
pip install gdown
gdown --folder "https://drive.google.com/drive/folders/1-s2cI2SgxsLAt4tOg6iLgTW_tto8QcPS"
```

### Expected structure

> **TODO:** adjust to match your actual folder layout.

```
ROAD-6-Det/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
└── data.yaml
```

## Results

All models were trained on the same ROAD-6-Det split at 640 × 640 for 100 epochs (learning rate 1e-4, batch size 16).

### Main comparison

| Model | mAP@50 | mAP@50:95 | Precision | Recall | F1 |
|---|---|---|---|---|---|
| YOLOv11-L / YOLOv12-L (best YOLO) | 75.9 | 52.4 / 52.5 | n/a | n/a | n/a |
| RF-DETR-M (best baseline) | 85.7 | 63.7 | 86.7 | 82.0 | 84.3 |
| **ROAD-DETR (ours)** | **94.2** | **72.1** | **93.2** | **91.1** | **93.5** |

See Table 3 in the paper for the full list of compared detectors.

### Efficiency and safety metrics

| Metric | RF-DETR-M | ROAD-DETR |
|---|---|---|
| Parameters | 33.7 M | 34.6 M |
| GFLOPs | 109.3 | 118.6 |
| False-positive ratio | 13.3% | 6.8% |
| False-negative rate | 18.0% | 8.9% |

ROAD-DETR runs at about 10.1 ms per image (99.0 FPS) with roughly 2.78 GB peak GPU memory on an NVIDIA RTX PRO A6000, and about 26.0 FPS (38.5 ms) on an NVIDIA Jetson Orin.

### Robustness

ROAD-DETR outperforms the compared transformer baselines under low-light, fog, rain, blur, and small-hazard conditions at every severity level. Under the most severe settings it retains 88.6% to 90.8% mAP@50, exceeding the strongest baseline by 4.2 to 6.0 percentage points. Details are in Table 5 of the paper.

## Installation

> **TODO:** update to match your repository.

Tested environment from the paper: Python 3, PyTorch 2.9.0, CUDA 12.8.

```bash
git clone https://github.com/Temirova-Zubayda/ROAD-6-Det.git
cd ROAD-6-Det

conda create -n road-detr python=3.10 -y
conda activate road-detr

pip install -r requirements.txt
```

## Usage

> **TODO:** replace the placeholder commands with your actual scripts.

**Training**

```bash
python train.py --data path/to/data.yaml --epochs 100 --batch-size 16 --imgsz 640 --lr 1e-4
```

**Evaluation**

```bash
python val.py --data path/to/data.yaml --weights path/to/road_detr.pt --imgsz 640
```

**Inference**

```bash
python predict.py --weights path/to/road_detr.pt --source path/to/images --imgsz 640
```

**Pretrained weights:** `<LINK_TO_WEIGHTS>`

## Limitations

- Evaluation is primarily on ROAD-6-Det and synthetic robustness conditions. Results should not be read as evidence of unrestricted cross-domain generalization.
- ROAD-DETR uses RGB input only, without depth, LiDAR, or radar cues.
- Axis-aligned boxes only approximate amorphous surface hazards such as oil spills, surface water, and icy roads.
- Roughly half of the dataset consists of augmented images.

## Citation

If you use this dataset or code, please cite:

```bibtex
@article{okmirzaevna2026road,
  title   = {Learning to Expect the Unexpected: Benchmarking and Detecting Unexpected Road Hazards},
  author  = {Okmirzaevna, Temirova Zubayda and Ali, Shehzad and Scherer, Rafa{\l} and Munir, Arslan and Muhammad, Khan},
  journal = {Mathematics},
  volume  = {14},
  number  = {19},
  pages   = {3543},
  year    = {2026},
  doi     = {10.3390/math14193543}
}
```

Please also cite the original ROAD-6 dataset that ROAD-6-Det extends:

```bibtex
@inproceedings{ali2025road6,
  title     = {ROAD-6: A Diverse Dataset for Unexpected Hazard Recognition in Autonomous Vehicles},
  author    = {Ali, Shehzad and Islam, Mohammad Tariqul and Dao, Minh-Son and Lee, Ik-Hyun and Liu, Sheng and Muhammad, Khan},
  booktitle = {Proceedings of the 2025 International Conference on Multimedia Retrieval},
  pages     = {1973--1977},
  year      = {2025},
  publisher = {ACM}
}
```

## License

> **TODO:** confirm the license for code and data before publishing.

The paper is released under CC BY 4.0. Specify the license for the code (for example MIT or Apache-2.0) and for the dataset (for example CC BY 4.0) here.

## Acknowledgments

This research was supported by the ANCHOR through the Seoul ANCHOR Center, funded by the Ministry of Education (MOE) and the Seoul Metropolitan Government (2026-ANCHOR-01-018-01).

## Contact

For questions, please open a GitHub issue or contact the corresponding authors, Arslan Munir and Khan Muhammad.
