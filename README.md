# Cross-Attention Fusion of Genomic and Chemical Representations for Robust Drug Sensitivity Prediction

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=flat&logo=PyTorch&logoColor=white)](https://pytorch.org/)

Official PyTorch implementation of the paper **"Cross-Attention Fusion of Genomic and Chemical Representations for Robust Drug Sensitivity Prediction"**.

This repository provides a comprehensive, uncertainty-aware Deep Learning framework designed for robust prediction of anticancer drug sensitivity ($IC_{50}$). The model leverages pharmacogenomic data from the Genomics of Drug Sensitivity in Cancer (GDSC) databases, offering a novel architectural approach to handling highly complex, non-linear interactions between tumor genomics and chemical structures.

---

## 📌 Abstract

Predicting the clinical efficacy of anti-cancer compounds remains a significant challenge due to the immense heterogeneity of tumor genomics and the vastness of chemical space. Traditional computational models frequently suffer from overfitting to chemical similarities, failing to generalize to novel molecular structures (out-of-distribution scaffolds). 

We propose the **Cross-Attention Drug-Genomic Fusion Model**, an architecture that directly mitigates these limitations through:
1. **Dynamic Cross-Attention:** Explicitly conditioning genomic mutation and expression profiles on drug identity embeddings to learn patient-specific, drug-dependent response mechanisms.
2. **Dual-Stream Processing:** Capturing global context via a multi-head **Transformer Encoder** and localized sequence dynamics via a **Bidirectional LSTM (BiLSTM)**.
3. **Rigorous Generalization:** Enforcing strict out-of-distribution evaluation through **Murcko Scaffold-blind splitting**.
4. **Epistemic Uncertainty Quantification:** Utilizing Monte Carlo (MC) Dropout to estimate predictive confidence bounds, ensuring reliability for clinical application.

---

## 🧠 Model Architecture

The proposed architecture effectively integrates heterogeneous biological and chemical data modalities:

- **Drug Embeddings:** Trainable $d$-dimensional representations capturing the chemical identity of the compounds.
- **Positional Encoding:** Standard sinusoidal encodings injected into genomic feature vectors to preserve positional dynamics.
- **Cross-Attention Fusion:** Genomic features serve as *queries* ($Q$), while the drug embedding projects into *keys* ($K$) and *values* ($V$). This mechanism modulates the genomic sequence representation dynamically based on the specific drug being evaluated.
- **Parallel Feature Extraction:** The fused representations are processed simultaneously by a Transformer Encoder (self-attention for global dependencies) and a BiLSTM (for bidirectional sequential context).
- **Attention Pooling:** A learnable weighted aggregation mechanism over the sequence dimension, identifying and up-weighting the most informative features before the final regression head.

---

## 📊 Experimental Results & Interpretability

Our framework not only achieves state-of-the-art predictive accuracy on unseen chemical scaffolds but also emphasizes model transparency and interpretability—crucial prerequisites for computational biology.

### Training Convergence and Generalization
The dual-stream architecture ensures smooth optimization without gradient explosion, demonstrating robust generalization to out-of-distribution chemical scaffolds (evaluated on the validation split).

<p align="center">
  <img src="results/plots/training_curves.png" alt="Training Convergence" width="80%">
</p>

### Epistemic Uncertainty Quantification (MC Dropout)
By retaining stochastic dropout during inference, the model generates empirical distributions for each prediction. This allows for the estimation of confidence intervals (red bounds). High predictive variance strongly correlates with structurally novel, out-of-distribution compounds.

<p align="center">
  <img src="results/plots/uncertainty_plots.png" alt="Uncertainty Quantification" width="80%">
</p>

### Global Feature Importance via SHAP
To elucidate the biological drivers of drug sensitivity, we apply SHapley Additive exPlanations (SHAP). The analysis identifies key genomic markers whose presence systematically drives IC$_{50}$ predictions higher (conferring resistance) or lower (conferring sensitivity).

<p align="center">
  <img src="results/plots/shap_bar.png" alt="SHAP Global Importance" width="45%">
  <img src="results/plots/shap_beeswarm.png" alt="SHAP Beeswarm" width="45%">
</p>

### Local Patient-Level Interpretability via LIME & SHAP
Beyond global trends, clinical utility demands patient-level explanations. Local Interpretable Model-agnostic Explanations (LIME) and SHAP Waterfall plots validate that the model's internal decision logic relies on biologically sound, non-linear feature interactions at an individual patient level.

<p align="center">
  <img src="results/plots/shap_waterfall.png" alt="SHAP Waterfall" width="80%">
</p>
<p align="center">
  <img src="results/plots/lime_comparison.png" alt="LIME Comparison" width="45%">
  <img src="results/plots/lime_sample0.png" alt="LIME Sample" width="45%">
</p>

---

## 🚀 Installation & Setup

Clone the repository and install the required dependencies. We recommend using a virtual environment (e.g., `conda` or `venv`).

```bash
git clone https://github.com/Panchadip-128/Cross-Attention-Fusion-based-Drug-Sensitivity-Detection.git
cd Cross-Attention-Fusion-based-Drug-Sensitivity-Detection
pip install -r requirements.txt
```

---

## 💻 Usage

### 1. Data Preparation
The model relies on the GDSC1 and GDSC2 datasets. Ensure the `GDSC1.csv` and `GDSC2.csv` data files are placed in the root directory (or specify their paths via command-line arguments).

### 2. Model Training
To train the model using the Murcko scaffold-blind splitting methodology with Early Stopping:

```bash
python scripts/train.py --epochs 200 --batch_size 8192 --lr 1e-3
```

*Note: The script automatically detects CUDA availability and configures PyTorch's Scaled Dot-Product Attention (SDPA) backend for optimal mathematical stability and performance.*

### 3. Evaluation and Uncertainty Estimation
To evaluate the optimal saved model and execute Monte Carlo Dropout for uncertainty bounds on the test set:

```bash
python scripts/evaluate.py --model_path results/best_model.pth
```

---

## 📂 Repository Structure

```text
├── docs/                  # Original Mathematical Theorems and Documentation (PDF/DOCX)
├── notebooks/             # Exploratory Data Analysis and Training Jupyter Notebooks
├── results/               
│   └── plots/             # Diagnostic plots, SHAP, LIME, and Uncertainty visualizations
├── scripts/               # Command-Line Interfaces (CLI) for training and evaluation
│   ├── train.py
│   └── evaluate.py
├── src/                   # Core Python Package
│   ├── data/              # Data ingestion, preprocessing, and RDKit Murcko Scaffold splitting
│   ├── models/            # Architectural components (Cross-Attention, BiLSTM, Transformer)
│   ├── training/          # AdamW optimization, Cosine Annealing, and MC Dropout evaluation
│   └── utils/             # Deterministic seed locking and visualization utilities
├── requirements.txt       # Package dependencies
└── README.md
```

---

## 📜 Citation

If you find this code or our methodology useful in your research, please consider citing our work:

```bibtex
@article{panchadip2026crossattention,
  title={Cross-Attention Fusion of Genomic and Chemical Representations for Robust Drug Sensitivity Prediction},
  author={Panchadip},
  journal={IEEE Access},
  year={2026}
}
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
