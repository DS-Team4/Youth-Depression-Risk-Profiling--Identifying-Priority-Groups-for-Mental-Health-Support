# Youth Depression Risk Profiling

## Identifying Priority Groups for Mental Health Support

This project analyzes youth depression risk using the **2024 Youth Life Survey** dataset.
The main goal is not only to predict whether young adults show depressive symptoms, but also to identify which groups are relatively more vulnerable and may require prioritized mental health support.

The project combines **machine learning-based classification** and **K-means clustering** to analyze high-risk youth groups from lifestyle, economic, health, and social relationship perspectives.

---

## Project Overview

Depressive symptoms among young adults are not only individual psychological issues, but may also be related to broader social and economic factors such as employment instability, social isolation, living patterns, and economic conditions.

This project aims to answer the following question:

> Which groups of young adults should mental health support prioritize?

To address this question, we first build classification models to predict depressive symptoms and extract important features. Then, using the selected features, we perform K-means clustering to identify youth groups with relatively higher depression risk.

---

## Dataset

* **Dataset**: 2024 Youth Life Survey
* **Rows**: 15,098
* **Columns**: 50
* **Target variable**: Presence of depressive symptoms

The dataset includes variables related to:

* Demographic characteristics
* Health and lifestyle patterns
* Employment and economic activity
* Social relationships
* Income, debt, assets, and living expenses

The target variable showed class imbalance:

* No depressive symptoms: 90.7%
* Depressive symptoms: 9.3%

Because the minority class was relatively small, the project focused more on **Recall** and **F1-score** than Accuracy.

---

## Academic Use Notice

This project was conducted as part of the **Data Science** team project at **Gachon University**.

This repository is intended for academic and educational purposes only.  
The analysis, source code, and documentation were created to study machine learning-based classification and clustering methods using youth survey data.

All rights to the original dataset and related survey materials belong to their respective data providers.

---

## Methodology

### 1. Data Exploration

The dataset was first analyzed to understand:

* Feature types
* Target distribution
* Missing value patterns
* Categorical variable distributions
* Numerical variable distributions
* Outliers and skewness

A key observation was that many employment-related variables had missing values due to conditional survey questions. These were treated as **structural missing values**, not simple random missing values.

---

### 2. Data Preprocessing

The preprocessing process included:

* Binary target conversion

  * No depressive symptoms → 0
  * Depressive symptoms → 1

* Removal of the burnout variable
  The burnout variable was excluded because it is directly related to depressive symptoms and may reduce the policy interpretability of the analysis.

* Structural missing value handling

  * Employment-related ordinal variables: non-working status mapped to 0
  * Employment-related nominal variables: missing values replaced with a “Not Applicable” category
  * Additional feature created: current working status

* Encoding

  * Ordinal variables were remapped while preserving order
  * Nominal variables were encoded using One-Hot Encoding

* Transformation and scaling

  * log1p transformation was applied to skewed economic variables
  * RobustScaler was applied to variables with strong outlier effects
  * StandardScaler was applied to general ordinal and continuous variables
  * Random Forest was trained without scaling due to its tree-based structure

---

### 3. Classification Modeling

Two classification models were used:

* Logistic Regression
* Random Forest

The models were trained to predict whether a respondent showed depressive symptoms.

Because detecting people with depressive symptoms was more important than simply maximizing overall accuracy, the evaluation focused on:

* Recall
* F1-score
* AUC

Class imbalance was handled using:

* `class_weight="balanced"`
* stratified split
* hyperparameter tuning

---

### 4. Logistic Regression

Two Logistic Regression models were compared:

* **LR_FULL**: Uses all features
* **LR_WORK**: Removes detailed employment-related features and keeps current working status

The LR_WORK model showed better Recall and AUC:

* Recall: 0.765
* AUC: 0.821

Grid Search was performed for the regularization parameter `C`, and the best value was:

* `C = 0.01`

The final Logistic Regression model detected about 7.7 out of 10 respondents with depressive symptoms.

---

### 5. Random Forest

The initial Random Forest model showed high Accuracy but very low Recall:

* Accuracy: 0.9093
* Recall: 0.0412

This showed that the model was biased toward the majority class.

After applying class imbalance handling and hyperparameter tuning, the Random Forest model improved:

* Recall: 0.5153
* F1-score: 0.3961

Although Random Forest improved significantly after tuning, Logistic Regression showed higher Recall overall.

---

### 6. Feature Selection for Clustering

Feature importance from Logistic Regression and Random Forest was used to select the final 10 features for clustering.

The selected features were:

1. Perceived health status
2. Frequency of going out
3. Total debt
4. Interest in politics
5. Number of household members
6. Subjective class perception
7. Frequency of eating out
8. Gender
9. Whether the respondent has non-family social contacts
10. People the respondent ate with during the past month

These features were selected because they were important for predicting depressive symptoms and were useful for interpreting youth group characteristics.

---

### 7. K-means Clustering

K-means clustering was performed using the selected 10 features.

The number of clusters was determined by considering:

* Elbow Point
* Silhouette Score

The final number of clusters was:

* `K = 3`

The Silhouette Score was 0.136, which is not high. Therefore, the clustering result should not be interpreted as strongly separated natural clusters. Instead, the clustering was used as an exploratory method to identify meaningful youth group patterns.

---

### 8. High-Risk Cluster Analysis

After clustering, the actual depressive symptom target was combined with the cluster labels.

Cluster 0 showed the highest depressive symptom rate:

* Cluster 0 depressive symptom rate: 16.24%
* Overall depressive symptom rate: 9.3%

Therefore, Cluster 0 was defined as the high-risk group.

The high-risk group showed the following characteristics:

* Lower frequency of going out
* Lower level of social activity
* Lower economic activity participation
* Lower income level
* Possible vulnerability in employment stability

---

## Conclusion

The analysis suggests that the youth depression high-risk group tends to have lower levels of social and economic activity.

This does not prove a causal relationship. Lower economic activity may contribute to depressive symptoms, or depressive symptoms may reduce social and economic participation. Both factors may also influence each other.

However, the results suggest that youth depressive symptoms may be closely related to social isolation and economic participation levels.

Therefore, mental health support for young adults should not be limited to psychological counseling alone. It should also consider:

* Expanding social participation opportunities
* Supporting entry into stable economic activity
* Improving employment stability
* Providing integrated social and economic support

---

## Project Structure

```text
youth-depression-risk/
│
├── code/
│   ├── data_preprocessing.ipynb 
│   ├── model_logistic.ipynb
│   ├── model_randomforest.ipynb
│   ├── model_kmeans.ipynb
│   └── open_source_contribution_pipeline.ipynb
│
├── dataset/
│   └── README.md
│
├── project_guide/
│   ├── project_guide_korean.zip
│   └── project_guide_english.docx
│
│
│
└── README.md
```

---

## File Description

### `code/`

This folder contains the main analysis notebooks used in the project.

* `data_preprocessing.ipynb`
  Handles data cleaning and preprocessing, including target conversion, missing value handling, encoding, feature transformation, and scaling.

* `model_logistic.ipynb`
  Builds and evaluates the Logistic Regression models. It compares different feature settings, performs Grid Search for the regularization parameter, and extracts feature importance from model coefficients.

* `model_randomforest.ipynb`
  Builds and evaluates the Random Forest models. It includes class imbalance handling, hyperparameter tuning, threshold adjustment, and feature importance analysis.

* `open_source_contribution_pipeline.ipynb`  
  Provides a reusable top-level pipeline that combines preprocessing, model training/testing, hyperparameter comparison, evaluation, feature selection, and K-means clustering. It returns the top-performing model combinations and analyzes depression rates by cluster.
  

---

### `dataset/`

 Includes the dataset download link.

---

### `project_guide/`

This folder contains the project documentation.

* `project_guide_korean.zip`
  Korean project guide exported from Notion. It explains the full project process, including the business objective, data exploration, preprocessing, modeling, clustering, and conclusion.

* `project_guide_english.docx`
  English version of the project guide. It summarizes the overall methodology and findings in English.

---

## Main Tools

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Seaborn
* Google Colab

---

## Team

DS-Team4
