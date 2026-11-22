# FDDPP: Medicine Quality Classification with Random Forest

**Fake Drug Detection in Pharmaceutical Products (FDDPP)**: a machine learning model that classifies medicine samples as *Safe* or *Not Safe* from their physicochemical and storage measurements.

Undergraduate thesis project, Department of Electrical and Electronic Engineering (Electronic and Computer Engineering option), Federal University of Technology, Owerri (FUTO), 2025. Group project (team of three). Supervisor: Prof. F. K. Opara.



> \*\*About this repository:\*\* it contains the project write-up, results, and figures only. The source code is not published here.

\---

## 

## 1\. Problem

Counterfeit and substandard medicines are a serious public-health problem, especially in countries with limited verification systems such as Nigeria. Laboratory testing is slow and costly. This project asks a simple question: **can routine quality measurements be used to flag unsafe medicines automatically?**

## 

## 2\. Dataset

|Item|Detail|
|-|-|
|Source|Kaggle, [*Medicine Quality Assessment Dataset*](https://www.kaggle.com/datasets/chaitanya205/medicine-quality-assessment-dataset) by Chaitanya Sonaje (chaitanya205)|
|Nature of data|**Synthetic**: the dataset's author states it was generated with ChatGPT and should not be used for clinical or regulatory decisions|
|Licence|CC0 1.0 (Public Domain)|
|Size|1,100 rows, 9 columns|
|Features (8)|Active Ingredient (categorical, 10 compounds incl. Aspirin, Paracetamol, Amoxicillin, Metformin, plus a missing-value category "Unknown"); Days Until Expiry; Storage Temperature (°C); Warning Labels Present (0/1); Dissolution Rate (%); Disintegration Time (minutes); Impurity Level (%); Assay Purity (%)|
|Target|Safe / Not Safe (perfectly balanced: 550 each)|
|Missing values|56 to 71 per feature column (about 5 to 6% each); none in the target|
|Duplicates|253 exact duplicate rows (847 unique rows); see [Limitations](#7-limitations-and-honest-notes)|

## 

## 3\. Method

1. **Cleaning:** missing *Active Ingredient* values set to "Unknown"; missing numeric values filled with the column median (scikit-learn `SimpleImputer`).
2. **Encoding:** *Active Ingredient* one-hot encoded; target label-encoded (Not Safe = 0, Safe = 1).
3. **Split:** 80% training (880 rows) and 20% testing (220 rows), fixed random seed (42).
4. **Model:** Random Forest classifier, tuned with 5-fold cross-validated grid search over 216 parameter combinations (1,080 model fits): number of trees, maximum depth, minimum samples to split, minimum samples per leaf, and bootstrap.
5. **Selected settings:** 100 trees, unlimited depth, minimum split 2, minimum leaf 1, bootstrap on.
6. **Deployment prototype:** the trained model and encoders were saved with Joblib and wrapped in a desktop interface (Tkinter) where a user enters a medicine's measurements and receives a Safe / Not Safe prediction with a confidence score.

Note: no feature scaling was applied. Random Forest does not require it.



!\[System flowchart](figures/system\_flowchart.png)



4\. Results

Evaluated on the 220 held-out test rows:

|Metric|Value|
|-|-|
|Accuracy|**98.18%** (216 of 220 correct)|
|Not Safe: precision / recall / F1|0.99 / 0.97 / 0.98|
|Safe: precision / recall / F1|0.97 / 0.99 / 0.98|
|ROC AUC (as reported in the thesis)|0.9948|



**Confusion matrix** (rows = actual, columns = predicted):

||Predicted Not Safe|Predicted Safe|
|-|-|-|
|**Actual Not Safe**|115|3|
|**Actual Safe**|1|101|



!\[Confusion matrix](figures/confusion\_matrix.png)



### Which measurements mattered most

Random Forest feature importance:

|Feature|Importance|
|-|-|
|Assay Purity (%)|32.8%|
|Impurity Level (%)|32.4%|
|Dissolution Rate (%)|16.6%|
|Days Until Expiry|5.0%|
|Disintegration Time (min)|4.3%|
|Storage Temperature (°C)|4.2%|
|Warning Labels Present|0.8%|
|Active Ingredient (all categories combined)|about 3.9%|

Assay purity, impurity level, and dissolution rate together account for about 82% of the model's decisions. This matches what pharmaceutical quality assurance treats as the key quality indicators.



!\[Feature importance](figures/feature\_importance.png)



### Comparison with simpler models

On the same train/test split, with standardised features \[CONFIRM after re-running]:

|Model|Test accuracy|
|-|-|
|Logistic Regression|about 72%|
|k-Nearest Neighbours|about 73%|
|Random Forest|98.18%|

The large gap suggests the relationship between the measurements and the label is non-linear (threshold-like), which tree-based models capture well.

## 

## 5\. Interface prototype

!\[GUI screenshot](figures/gui\_screenshot.png)



The prototype takes the eight measurements as input and shows the predicted status, the probability of "Safe", and simple charts of the prediction confidence and the entered values.

## 

## 6\. Tools

Python 3.10, Google Colab and Jupyter Notebook, Pandas, NumPy, scikit-learn (`RandomForestClassifier`, `GridSearchCV`, `SimpleImputer`, `OneHotEncoder`, `LabelEncoder`), Joblib, Matplotlib, Seaborn, Tkinter.

## 

## 7\. Limitations and honest notes

* **Synthetic data.** The dataset is computer-generated, so these results show that the *method works*, not how it would perform on real medicines. Real counterfeit detection would need verified, laboratory-measured samples.
* **"Safe / Not Safe" is not the same as "genuine / counterfeit".** The model classifies measured quality, which is a proxy for counterfeit detection.
* **Duplicate rows inflate the score.** 253 of the 1,100 rows are exact duplicates, and 92 of the 220 test rows have an identical copy in the training set. When duplicates were removed *before* splitting (170 test rows, default settings, not re-tuned), accuracy was about **97.7%**. The model remains strong, but the headline figure is slightly optimistic.
* **Small dataset and no external validation.** Everything comes from one 1,100-row file with a single train/test split.
* **Prototype only.** The interface is a demonstration and is not suitable for regulatory or clinical use.

## 

## 8\. Differences from the submitted thesis

This write-up re-runs the original pipeline and corrects a few items from the submitted thesis:

* the confusion matrix (the thesis figure showed 2 and 116 in the Not Safe row; the correct counts are 3 and 115);
* the grid-search size (216 combinations and 1,080 fits, not 288 and 1,440);
* the feature-importance values, now taken directly from the trained model;
* the discussion of duplicate rows and their effect on the test score.

## 

## 9\. My contribution

* Sourced and prepared the dataset (Kaggle)
* Compared candidate models to select the best-performing one
* Produced the system diagrams and flowcharts
* Wrote the introduction and literature review of the project report

## 

## 10\. Future work

Test on verified real-world samples; try gradient boosting and explainability tools (SHAP or LIME); add image, QR-code or spectral inputs; build a web or mobile version; connect the system to regulatory databases such as NAFDAC.

## 

## 11\. Credits

* Supervisor: Prof. F. K. Opara, FUTO
* Team members: Ebubechi Victor Gbule, Njoku Wisdom, Ugochukwu Emmanuel
* Technical guidance: Mr Benjamin Nwaohiri

**Contact:** [LinkedIn](https://www.linkedin.com/in/gbule-victor-9ba94a248)



© 2026 Ebubechi Victor Gbule and project team. All rights reserved.

