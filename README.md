# Representation-learning-of-Hyperspectral-data
# Spatial-Spectral Representation Learning for Ceres Hyperspectral Retrieval

## About the Project

This project investigates the spatial and spectral structure of hyperspectral observations of **Ceres** from NASA's Dawn/VIR instrument.

The project is motivated by an assumption in a planetary-surface retrieval pipeline: neighbouring pixels are grouped into small spatial blocks (currently **3 × 3 pixels**) to obtain a common emissivity estimate, which is subsequently used to initialise pixel-wise retrievals.

The validity of this assumption has not yet been quantitatively established.

The central goal of this project is therefore to determine **over what spatial scale neighbouring Ceres pixels can reasonably be considered spectrally and emissively similar**, and to investigate whether learned representations can provide useful information beyond conventional spectral-similarity measures.

---

## Research Question

### Primary Question

> **What spatial scale is appropriate for grouping neighbouring Ceres VIR pixels for joint emissivity estimation, and can learned spatial-spectral representations improve the identification of spectrally homogeneous groups compared with conventional spectral-similarity measures?**

This is divided into two questions.

### RQ1 — Spatial-Spectral Homogeneity

> How does spectral and retrieved-emissivity similarity vary with spatial separation and group size, and to what extent do 2 × 2, 3 × 3, 4 × 4, etc. neighbourhoods satisfy the assumption of approximate emissivity homogeneity?

### RQ2 — Representation Learning

> Can a learned representation of Ceres spatial-spectral observations capture scientifically relevant similarity that is not adequately described by conventional spectral-distance measures?

Representation learning is **not assumed to be necessary**. Conventional methods will first be established as baselines. Deep representation learning will only be introduced if there is a scientific motivation to go beyond those baselines.

---

# What the Project Does

The project follows a progressive approach:

```text
Ceres VIR hyperspectral data
          │
          ▼
   Data characterisation
          │
          ▼
Conventional spectral similarity
          │
          ▼
Spatial-scale / block-size analysis
          │
          ▼
Emissivity-based validation
          │
          ▼
        PCA baseline
          │
          ▼
   Learned representations
          │
          ▼
 Spatial-spectral representations
          │
          ▼
 Self-supervised learning
          │
          ▼
Comparison and scientific evaluation
```

The main spatial grouping scales investigated are:

- 1 × 1
- 2 × 2
- 3 × 3
- 4 × 4
- 5 × 5
- potentially larger neighbourhoods where appropriate

The objective is **not simply to find a single optimal block size**. The project will investigate whether the appropriate spatial scale depends on the local surface structure.

---

# Why Is This Useful?

The current retrieval workflow uses small groups of neighbouring pixels to obtain an initial emissivity estimate before performing pixel-wise retrieval.

This raises an important scientific question:

> **How large can such a group be before the assumption of similar emissivity becomes unreliable?**

A fixed 3 × 3 block may work well in a spectrally homogeneous region but may cross a compositional or morphological boundary elsewhere.

Understanding this can help:

- quantify the validity of the current 3 × 3 grouping assumption;
- compare 2 × 2, 3 × 3, 4 × 4 and larger spatial scales;
- identify regions where joint retrieval is reasonable;
- identify regions where smaller groups or pixel-wise retrieval may be preferable;
- investigate whether grouping should be spatially adaptive;
- understand the relationship between spectral similarity and retrieved emissivity similarity.

The representation-learning component asks a further question:

> Can a learned representation capture meaningful spectral and spatial structure that conventional distance measures fail to capture?

The project therefore combines a **real inverse-problem motivation** with methods from **scientific machine learning and representation learning**.

---

# Scientific Background

Each pixel contains a hyperspectral observation

\[
\mathbf{x}_i =
[I_i(\lambda_1),I_i(\lambda_2),\ldots,I_i(\lambda_B)]
\]

where \(B\) is the number of usable wavelength bands.

For neighbouring pixels \(i\) and \(j\), conventional similarity can be quantified using measures such as:

### Euclidean distance

\[
d_E(\mathbf{x}_i,\mathbf{x}_j)
=
\|\mathbf{x}_i-\mathbf{x}_j\|_2
\]

### Cosine similarity

\[
\operatorname{cos}(\mathbf{x}_i,\mathbf{x}_j)
=
\frac{\mathbf{x}_i\cdot\mathbf{x}_j}
{\|\mathbf{x}_i\|\|\mathbf{x}_j\|}
\]

Other physically motivated spectral similarity measures may also be investigated.

A learned representation instead introduces a mapping

\[
f_\theta(\mathbf{x})=\mathbf{z},
\]

where \(\mathbf{z}\) represents the original observation in a learned feature space.

For spatial-spectral models, the input can be a local patch

\[
\mathbf{X}\in\mathbb{R}^{H\times W\times B},
\]

allowing both spectral and spatial information to contribute to the representation.

---

# Methodology

## 1. Data Characterisation

The first stage establishes the properties of the Ceres VIR data.

This includes:

- spatial and spectral dimensions;
- wavelength coverage;
- invalid pixels and missing values;
- signal-to-noise ratio;
- spectral variability;
- normalization;
- noise characteristics;
- thermal-correction considerations.

Visualisations will include:

- representative spectra;
- spectral maps;
- SNR maps;
- spectral variability maps;
- spatially resolved hyperspectral products.

---

## 2. Conventional Spectral Similarity

Before using machine learning, neighbouring pixels will be compared directly.

The objective is to determine:

\[
\text{spectral similarity}
\quad\text{vs.}\quad
\text{spatial separation}.
\]

This establishes a physically interpretable baseline.

---

## 3. Spatial Block-Size Analysis

Different spatial neighbourhoods will be compared:

\[
1\times1,\quad
2\times2,\quad
3\times3,\quad
4\times4,\quad
5\times5,\ldots
\]

For each block, internal spectral variability will be quantified.

A block containing \(N\) pixels can be evaluated using:

\[
V =
\frac{1}{N}
\sum_{i=1}^{N}
d(\mathbf{x}_i,\bar{\mathbf{x}}).
\]

This allows the current 3 × 3 assumption to be tested rather than simply adopted.

---

## 4. Emissivity-Based Validation

Where pixel-wise retrieval results are available, spectral similarity will be compared against inferred emissivity similarity.

For each pixel:

\[
\mathbf{x}_i
\rightarrow
\hat{\boldsymbol{\epsilon}}_i.
\]

Within each spatial block, the variability of

\[
\hat{\boldsymbol{\epsilon}}_1,
\hat{\boldsymbol{\epsilon}}_2,\ldots,
\hat{\boldsymbol{\epsilon}}_N
\]

will be evaluated.

Possible metrics include:

- emissivity RMSE;
- mean absolute difference;
- wavelength-dependent variance;
- pairwise emissivity distance;
- uncertainty-normalised differences.

This directly connects the analysis to the downstream retrieval problem.

---

## 5. PCA Baseline

Principal Component Analysis will provide a simple linear representation:

\[
\mathbf{x}\rightarrow\mathbf{z}_{PCA}.
\]

The PCA representation will be evaluated for:

- dimensionality reduction;
- reconstruction;
- spatial coherence;
- clustering;
- preservation of spectral structure.

---

## 6. Nonlinear Representation Learning

If conventional approaches reveal limitations, nonlinear representation-learning methods will be investigated.

### Spectral Autoencoder

\[
\mathbf{x}
\rightarrow
\mathbf{z}
\rightarrow
\hat{\mathbf{x}}
\]

The model will learn a compact representation by minimising reconstruction error.

### Spatial-Spectral CNN

Local hyperspectral patches will be processed as:

\[
H\times W\times B
\rightarrow
\mathbf{z}.
\]

This allows the learned representation to incorporate both:

- spectral information;
- local spatial context.

---

## 7. Self-Supervised Learning

If appropriate, self-supervised learning will be explored using scientifically justified transformations of the observations.

The objective will be to learn representations that:

- preserve scientifically meaningful spectral structure;
- suppress appropriate nuisance variations;
- remain useful for identifying similar observations.

Transformations will be selected based on the physics and characteristics of hyperspectral observations rather than adopted blindly from natural-image computer vision.

---

# Evaluation

Models will be compared against conventional baselines.

The evaluation will consider:

### Within-group homogeneity

Are observations assigned to the same group actually similar?

### Between-group separation

Can different groups be distinguished?

### Spatial coherence

Do identified groups correspond to meaningful spatial structures?

### Spectral preservation

Does the representation retain important spectral features?

### Emissivity consistency

Do observations considered similar also have similar retrieved emissivity spectra?

### Robustness

Are the results stable under reasonable changes in preprocessing, noise, wavelength selection and model initialisation?

---

# Expected Outcomes

The project aims to determine:

1. Whether neighbouring Ceres pixels exhibit a measurable spatial scale of spectral similarity.
2. Whether the current 3 × 3 grouping assumption is supported by the data.
3. How 2 × 2, 3 × 3, 4 × 4 and larger groups compare.
4. Whether radiance/spectral similarity is a reliable proxy for emissivity similarity.
5. Whether spatial context provides additional information beyond individual spectra.
6. Whether learned representations provide advantages over conventional similarity measures.
7. Whether a globally fixed block size is appropriate or whether grouping should depend on local surface structure.

An important principle of this project is that **deep learning does not have to outperform simpler methods**.

If conventional spectral analysis provides a sufficient and physically interpretable solution, that is itself a meaningful result.

---

# Connection to the Retrieval Pipeline

The project is motivated by the following existing workflow:

```text
Ceres VIR observations
        │
        ▼
  Spatial block
     (3 × 3)
        │
        ▼
Block-level Bayesian retrieval
        │
        ▼
Initial emissivity estimate
        │
        ▼
Pixel-wise retrieval / OE
```

The proposed analysis adds a quantitative stage:

```text
Ceres VIR observations
        │
        ▼
Spatial-spectral similarity analysis
        │
        ▼
Determine appropriate grouping scale
        │
        ▼
Block-level Bayesian retrieval
        │
        ▼
Pixel-wise retrieval / OE
```

The ultimate purpose is therefore **not to replace the physical retrieval model with a neural network**.

Instead, the project investigates whether data-driven representations can help characterise the spatial-spectral structure underlying the retrieval assumptions.

---

# Getting Started

## Requirements

The initial analysis will use Python and common scientific-computing libraries.

Recommended environment:

```text
Python >= 3.10
NumPy
SciPy
pandas
matplotlib
scikit-learn
PyTorch
Jupyter
```

GPU support will be used for neural-network experiments where available.

---

## Installation

Clone the repository:

```bash
git clone <repository-url>
cd <repository-name>
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it:

### Linux/macOS

```bash
source .venv/bin/activate
```

### Windows

```bash
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Data

The Ceres VIR data are not distributed with this repository.

Place the required data in the appropriate local data directory:

```text
data/
├── raw/
├── processed/
└── metadata/
```

Large scientific datasets should **not** be committed directly to the Git repository.

The exact preprocessing and data-access instructions will be documented as the analysis develops.

---

## Running the Analysis

The project will be developed progressively.

The initial workflow will be:

```text
01_data_characterisation
        ↓
02_spectral_similarity
        ↓
03_block_size_analysis
        ↓
04_emissivity_validation
        ↓
05_pca_baseline
        ↓
06_autoencoder
        ↓
07_spatial_spectral_model
        ↓
08_self_supervised_learning
        ↓
09_comparison
```

Not every stage is required to be completed before evaluating the previous one. Each stage should produce reproducible intermediate results.

---

# Repository Structure

The repository is expected to follow a structure similar to:

```text
.
├── README.md
├── requirements.txt
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── metadata/
│
├── notebooks/
│   ├── 01_data_characterisation.ipynb
│   ├── 02_spectral_similarity.ipynb
│   ├── 03_block_size_analysis.ipynb
│   ├── 04_emissivity_validation.ipynb
│   ├── 05_pca_baseline.ipynb
│   ├── 06_autoencoder.ipynb
│   ├── 07_spatial_spectral_model.ipynb
│   └── 08_self_supervised_learning.ipynb
│
├── src/
│   ├── data/
│   ├── preprocessing/
│   ├── similarity/
│   ├── models/
│   ├── evaluation/
│   └── visualisation/
│
├── configs/
│
├── results/
│   ├── figures/
│   ├── metrics/
│   └── models/
│
└── docs/
```

---

# Reproducibility

Because this is a scientific machine-learning project, reproducibility is a central requirement.

Experiments should record:

- dataset/version;
- preprocessing steps;
- wavelength selection;
- normalization;
- spatial block size;
- model architecture;
- latent dimension;
- batch size;
- learning rate;
- optimizer;
- number of epochs;
- random seed;
- hardware/GPU;
- training time;
- evaluation metrics.

Random seeds will be controlled wherever practical.

Spatial leakage will also be considered carefully. Randomly splitting neighbouring pixels between training and validation sets can produce overly optimistic results because spatially adjacent observations are not statistically independent.

---

# Where to Get Help

For questions about the scientific methodology, implementation, or interpretation:

1. Check the relevant notebook and documentation in this repository.
2. Review existing GitHub Issues before opening a new issue.
3. Open a new **Issue** for reproducible bugs, methodological questions, or implementation problems.
4. For proposed changes, open a **Pull Request** with a description of the motivation and expected impact.

When reporting a computational problem, please include:

- Python version;
- package versions;
- operating system;
- GPU information, if relevant;
- minimal reproducible example;
- full error message;
- relevant configuration.

---

# Contributing

Contributions are welcome, particularly in:

- scientific validation;
- spectral-similarity methods;
- hyperspectral preprocessing;
- representation-learning methods;
- evaluation metrics;
- reproducibility improvements;
- documentation.

For substantial changes, please open an Issue first to discuss the proposed approach.

Contributors should ensure that proposed methods are scientifically justified for hyperspectral planetary observations.

In particular, machine-learning augmentations or objectives should not be introduced solely because they are standard in natural-image computer vision.

---

# Maintainer

**Aliyya Fathima Rinu**

Research interests:

- Bayesian inference
- Inverse problems
- Uncertainty quantification
- Scientific machine learning
- Representation learning
- Astronomy and planetary science

This repository is currently maintained as an independent research and computational-learning project.

---

# Project Status

**Status: Early Research / Development**

Current priority:

> Establish the conventional spectral-similarity baseline and quantitatively investigate the spatial scale of spectral/emissivity homogeneity before introducing deep representation-learning models.

The machine-learning components will be developed progressively based on the results of the scientific baseline.

---

# Citation

If this repository or its methodology contributes to published work, a citation entry will be added here.

---

# Acknowledgements

This project builds on hyperspectral observations obtained by NASA's **Dawn mission** and its **Visible and Infrared Spectrometer (VIR)** instrument.

The project is also motivated by ongoing work on Bayesian retrieval and optimal-estimation methods for planetary hyperspectral observations.

---

# License

License information will be added when the repository is prepared for public release.
