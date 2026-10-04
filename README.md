# Security Attack Detection Using Artificial Neural Network and Random Forest

## 1. Project Overview

This project detects network security attacks using machine learning on the **NSL-KDD** dataset.

It reproduces the Artificial Neural Network (ANN) approach from the reference paper **"Using Neural Network for Security Attacks Detection"**, using the architecture and training parameters described there. As an extension, a **Random Forest** classifier is trained and compared with the reference ANN on the same dataset and evaluation metrics.

---

## 2. Objectives

- Reproduce the ANN described in the reference paper.
- Apply the same major preprocessing approach used in the paper.
- Train and evaluate the ANN on the NSL-KDD dataset.
- Compare the reproduced results with those reported in the paper.
- Implement Random Forest as an additional model.
- Compare the ANN and Random Forest using multiple evaluation metrics.

---

## 3. Dataset

The project uses the **NSL-KDD dataset**, an improved version of the KDD Cup 1999 intrusion detection dataset. It contains network connection records with **41 original features**, covering:

- Basic connection features
- Content features
- Traffic features

Records are either normal or belong to one of four attack categories:

| Category | Description |
|----------|-------------|
| DoS | Denial of Service |
| Probe | Surveillance and probing |
| R2L | Remote to Local |
| U2R | User to Root |

The training set contains the attack types used to train the models. The test set also contains **previously unseen attack types**.

---

## 4. Data Preprocessing

### 4.1 Feature and Label Separation
Input features were separated from the attack labels. The `difficulty` column provided with NSL-KDD was **not** used as an input feature.

### 4.2 One-Hot Encoding
The categorical features `protocol_type`, `service` and `flag` were one-hot encoded. This expanded the 41 original features into **122 input features**, matching the dimensionality in the reference paper.

### 4.3 Feature Scaling
Features were standardized with `StandardScaler` (approximately zero mean and unit variance).

### 4.4 Target Encoding
The target is represented by **24 output classes**:

- 1 normal class
- 22 known attack classes
- 1 additional `unknown` class

The `unknown` class covers attack types that appear in the test set but not in the training set (23 known classes + 1 unknown class = 24).

---

## 5. Reference Artificial Neural Network

### Architecture

```text
122 Input Features
        |
        v
500 Neurons (Sigmoid)
        |
        v
24 Output Neurons (Softmax)
```

### Training Parameters

| Parameter | Value |
|-----------|-------|
| Input features | 122 |
| Hidden neurons | 500 |
| Output classes | 24 |
| Hidden activation | Sigmoid |
| Output activation | Softmax |
| Learning rate | 0.5 |
| Batch size | 1000 |
| Epochs | 10 |
| Loss function | Categorical Cross-Entropy |
| Optimizer | SGD |

The reference paper notes that ANN results vary between runs because of random weight initialization. A **fixed random seed** is used here to make the experiment reproducible.

---

## 6. Additional Model: Random Forest

Random Forest was chosen because it is a fundamentally different approach from a neural network and works well on structured tabular data. It is trained on the same preprocessed data and evaluated on the same NSL-KDD test set.

| Parameter | Value |
|-----------|-------|
| Number of trees | 200 |
| Random state | 42 |

---

## 7. Evaluation Metrics

- **Test Accuracy**: percentage of test records classified into the correct class.
- **Normal/Abnormal Accuracy**: whether a record is correctly identified as normal or as an attack (the additional evaluation used in the reference paper).
- **Macro F1-Score**: F1 computed per class, then averaged, giving every class equal importance.
- **Weighted F1-Score**: F1 computed per class, averaged and weighted by class size.
- **Known-Class Accuracy**: accuracy only on test records whose attack types were present in the training set, giving a fairer view of performance on classes the models were trained to recognize.

---

## 8. Results

| Model | Test Accuracy (%) | Normal/Abnormal Accuracy (%) | Macro F1 | Weighted F1 | Known-Class Accuracy (%) |
|-------|------------------:|-----------------------------:|---------:|------------:|-------------------------:|
| Reference ANN | 68.77 | 75.47 | 0.36 | 0.59 | 82.49 |
| Random Forest | 72.21 | 74.57 | 0.44 | 0.62 | 86.61 |

### Comparison with the Reference Paper

The paper reports approximately **70%** test accuracy and **80%** Normal/Abnormal accuracy. The reproduced ANN achieved **68.77%** and **75.47%** respectively, which is reasonably close. Differences are expected because the paper itself notes run-to-run variation from random initialization, and dataset versions and class distributions may also affect results.

---

## 9. Model Comparison

Random Forest outperformed the ANN on multi-class attack classification:

- Test accuracy: 68.77% → 72.21%
- Macro F1: 0.36 → 0.44
- Weighted F1: 0.59 → 0.62
- Known-class accuracy: 82.49% → 86.61%

For Normal/Abnormal classification, the ANN was slightly better (75.47% vs 74.57%).

In short, Random Forest is better at detailed multi-class attack classification, while the ANN is slightly better at separating normal from abnormal behaviour.

---

## 10. Project Structure

```text
ML_MINI_PROJ/
│
├── data/
│   ├── KDDTrain+.txt
│   └── KDDTest+.txt
│
├── models/
│   ├── reference_ann.keras
│   └── random_forest.pkl
│
├── ML_Mini_Project.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── venv/
```

> **Note:** `venv/` is a local Python virtual environment and is excluded from Git via `.gitignore`. The trained models are stored in `models/`.

---

## 11. Installation

**1. Clone the repository**

```bash
git clone <REPOSITORY_URL>
cd ML_MINI_PROJ
```

**2. Create a virtual environment**

```bash
python -m venv venv
```

**3. Activate the virtual environment**

Windows PowerShell:
```powershell
venv\Scripts\Activate.ps1
```

Windows Command Prompt:
```cmd
venv\Scripts\activate
```

**4. Install dependencies**

```bash
pip install -r requirements.txt
```

---

## 12. Running the Project

1. Open the project directory in VS Code.
2. Start Jupyter Notebook:

   ```bash
   jupyter notebook
   ```

3. Open `ML_Mini_Project.ipynb` and run all cells from first to last.

The notebook performs:

1. Dataset loading and inspection
2. Feature preprocessing (one-hot encoding, scaling, target encoding)
3. ANN construction, training and evaluation
4. Random Forest training and evaluation
5. Model comparison and visualization
6. Model saving

---

## 13. Saved Models

After running the notebook, the trained models are saved as:

- `models/reference_ann.keras`
- `models/random_forest.pkl`

These files are overwritten on each run so the directory always holds the latest trained models.

---

## 14. Conclusion

The reference ANN was successfully reproduced on NSL-KDD using the architecture from the reference paper. It achieved 68.77% test accuracy and 75.47% Normal/Abnormal accuracy, reasonably close to the paper's approximately 70% and 80%.

The additional Random Forest model achieved 72.21% test accuracy, 0.44 macro F1, 0.62 weighted F1 and 86.61% known-class accuracy. Overall, Random Forest performed better for multi-class attack classification, while the ANN was slightly better at Normal/Abnormal classification.
