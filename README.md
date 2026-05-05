# NEEDL-Bench

**A microscopy detection benchmark for Swiss Needle Cast (SNC) on Douglas-fir.**

NEEDL-Bench is a publicly available dataset for detecting and localizing two structures in Douglas-fir (*Pseudotsuga menziesii*) needle microscopy: **stomata** (the gas-exchange pores of the needle) and **pseudothecia** (the sexual reproductive structures of *Nothophaeocryptopus gaeumannii*, the fungus responsible for Swiss Needle Cast). Manual scoring of SNC severity at the needle level is currently done by counting the percentage of stomata occluded by pseudothecia — a slow, error-prone process. NEEDL-Bench is intended to enable scalable, computer-vision-based phenotyping for breeding programs, ecological monitoring, and forest-health assessment.

The dataset accompanies the paper:

> Blake, B., McIntosh, D., Ehlting, J., Feau, N., Tanney, J. B., Branzan Albu, A. *NEEDL-Bench: Dataset for Swiss Needle Cast and Stomata Detection in Microscopy Images.* CRV 2026.

If you use NEEDL-Bench in your work, please cite the paper (BibTeX below).

---

## Highlights

- **3,250 annotated 256×256 tiles** drawn from **1,082 Douglas-fir needles**.
- **Two acquisition modalities** — high-magnification benchtop microscopy and handheld Dino-Lite microscopy with multi-focus stacking — captured under realistic laboratory and field-adjacent conditions.
- **Two annotation formats per tile**: centroid keypoints and minimum-area bounding boxes, supporting both pose/keypoint detectors and standard object detectors.
- **Two complementary evaluation protocols**: a Random split that approximates the natural distribution of features, and a Maximum-Diversity split that maximizes coverage of rare structures via farthest-point sampling in a ResNet50 feature space.
- **Realistic challenges**: blur, low contrast, occlusion, freeze-thaw artefacts, foreign objects, and visually similar non-target diseases.

---

## Dataset structure

```
NEEDL-Bench/
├── images/
│   ├── benchtop/
│   │   └── *.png                # 256x256 tiles, 360 px/mm
│   └── handheld/
│       └── *.png
├── annotations/
│   ├── keypoints/
│   │   ├── train.json           # COCO-keypoints format
│   │   ├── val.json
│   │   └── test.json
│   └── bboxes/
│       ├── train.json           # COCO-detection format
│       ├── val.json
│       └── test.json
├── splits/
│   ├── benchtop_random.csv
│   ├── benchtop_diversity.csv
│   └── handheld_random.csv
├── raw/                          # (optional) original full-resolution captures
└── LICENSE
```

### Classes

| ID  | Name          | Description                                                  |
|-----|---------------|--------------------------------------------------------------|
| 0   | stomata       | Open or non-occluded gas-exchange pore                       |
| 1   | pseudothecia  | Stoma occluded by an SNC fruiting body (*N. gaeumannii*)     |

### Splits

| Source   | Sampling Method   | Train | Validation | Test |
|----------|-------------------|-------|------------|------|
| Benchtop | Random            | 1258  | 99         | 100  |
| Benchtop | Maximum Diversity | 0     | 50         | 50   |
| Handheld | Random            | 1185  | 254        | 254  |
| Handheld | Maximum Diversity | 0     | 0          | 0    |
| **Total**|                   | **2443** | **403** | **404** |

The Maximum-Diversity test/val sets are constructed only from the larger benchtop pool by farthest-point sampling in a 256-dimensional random projection of ResNet50 features, with the first 50 selections forming the test set and the next 50 forming the validation set.

### Annotation formats

- **Keypoints** are 2D centroids (`x, y`, in pixel coordinates of the 256×256 tile) with a class label.
- **Bounding boxes** are minimum-area rectangles in `[x_min, y_min, w, h]` format, derived from SAM2 segmentations seeded by the manual keypoints (see §III.D of the paper). All boxes were manually reviewed.
- Tiles are scaled to a uniform spatial resolution of **360 pixels per millimetre** before tiling, regardless of acquisition modality.

---

## Getting started

### Download

Available on:
- **Hugging Face Datasets:** `<TODO: insert HF link>`
- **Zenodo (DOI):** `<TODO: insert DOI>`
- Direct mirror: `<TODO: insert mirror link>`

```bash
# Clone this repo (code + small annotation files only)
git clone https://github.com/<your-org>/NEEDL-Bench.git
cd NEEDL-Bench

# Download images (large)
bash scripts/download.sh         # ~<TODO size> GB
```

### Quick load (Python)

```python
from pathlib import Path
import json
from PIL import Image

root = Path("NEEDL-Bench")
split = json.load(open(root / "annotations/keypoints/test.json"))

img_id_to_file = {im["id"]: im["file_name"] for im in split["images"]}
for ann in split["annotations"][:5]:
    img = Image.open(root / "images" / img_id_to_file[ann["image_id"]])
    print(ann["category_id"], ann["keypoints"], img.size)
```

### Reproduce the paper's baselines

```bash
pip install -r requirements.txt

# YOLOv11-Detect / YOLOv11-Pose
python scripts/train_yolo.py    --task detect --split benchtop_random
python scripts/train_yolo.py    --task pose   --split benchtop_random

# RF-DETR
python scripts/train_rfdetr.py  --split handheld_random

# FCN-ResNet50 (custom keypoint head)
python scripts/train_fcn.py     --split handheld_random
```

Pretrained checkpoints for each baseline are available in the **Releases** tab.

---

## Acquisition details (summary)

- **Benchtop**: SeBaCam14C microscope, 907 needles harvested from a General Combining Ability population in three BC Ministry of Forests Douglas-fir breeding plantations near Jordan River, Lake Cowichan, and Campbell River, B.C. Samples were stored at –20 °C prior to imaging.
- **Handheld**: Dino-Lite AM8917MZT microscope, 175 needles sampled in Saanichton, B.C. and from Douglas-firs around the University of Victoria. Needles were held in a custom 3D-printed jig, captured as multi-focus stacks, and automatically focus-stitched into a single in-focus image.

See §III of the paper for the complete acquisition, preprocessing, and annotation protocol.

---

## License

The dataset is released under **CC BY 4.0** for non-commercial and academic research use. Please credit the authors and cite the paper. Code in this repository is released under the **MIT License**. See `LICENSE` for full terms.

---

## Citation

```bibtex
@inproceedings{blake2026needlbench,
  title     = {NEEDL-Bench: Dataset for Swiss Needle Cast and Stomata Detection in Microscopy Images},
  author    = {Blake, Benjamin and McIntosh, Declan and Ehlting, J{\"u}rgen and Feau, Nicolas and Tanney, Joey B. and Branzan Albu, Alexandra},
  booktitle = {Conference on Robots and Vision (CRV)},
  year      = {2026}
}
```

---

## Acknowledgements

This work was supported by the ADM Innovation Fund, Genome British Columbia, the Canadian Forest Service (Natural Resources Canada), and the Natural Sciences and Engineering Research Council of Canada (NSERC). Needle sampling was supported by a GeneSolve project funded by Genome British Columbia.

---

## Contact

Questions, bug reports, and dataset-quality issues are welcome via GitHub Issues. For research collaboration enquiries, contact:

- Benjamin Blake — `benjamincblake@uvic.ca`
- Alexandra Branzan Albu — `aalbu@uvic.ca`
