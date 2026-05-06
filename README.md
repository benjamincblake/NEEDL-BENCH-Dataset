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
- **Hugging Face Datasets:** `https://huggingface.co/datasets/benjamincblake/Needl-Bench`

```bash
# Clone this repo (code + small annotation files only)
git clone https://github.com/<your-org>/NEEDL-Bench.git
cd NEEDL-Bench


```


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
  author    = {Blake, Benjamin and McIntosh, Declan and Ehlting, Jürgen and Feau, Nicolas and Tanney, Joey B. and Branzan Albu, Alexandra},
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
- Declan McIntosh — `declanmcintosh@gmail.com`
