# HW3 Multiple Linear Regression Analysis

本專案為大學資料科學作業「HW3：多元線性回歸問題」。分析主題為使用 Social Anxiety Dataset，透過個人的生活習慣、背景資料與生理數據，建立 Multiple Linear Regression 模型來預測 `Anxiety Level (1-10)`。

## Project Overview

- 主程式：`HW3.ipynb`
- 報告草稿：`reports/HW3_report_draft.md`
- 資料集：`enhanced_anxiety_dataset.csv`
- 資料來源：Kaggle - Social Anxiety Dataset
- Kaggle 連結：https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset
- 分析流程：CRISP-DM
- 主要模型：`sklearn.linear_model.LinearRegression`
- 完整報告：https://hackmd.io/@1OvSUl48SO6eh2ByVBEn_A/rkPZ5Mjyfx

## Dataset

資料集共有 11,000 筆資料、19 個欄位，包含數值型與類別型變數，沒有缺失值。

目標變數：

- `Anxiety Level (1-10)`

主要自變數包含：

- `Age`
- `Gender`
- `Occupation`
- `Sleep Hours`
- `Physical Activity (hrs/week)`
- `Caffeine Intake (mg/day)`
- `Alcohol Consumption (drinks/week)`
- `Stress Level (1-10)`
- `Heart Rate (bpm)`
- `Breathing Rate (breaths/min)`
- `Sweating Level (1-5)`
- `Therapy Sessions (per month)`
- `Diet Quality (1-10)`

## CRISP-DM Workflow

Notebook 依照 CRISP-DM 六階段撰寫：

1. Business Understanding
2. Data Understanding
3. Data Preparation
4. Modeling
5. Evaluation
6. Deployment

## Feature Engineering

Data Preparation 階段建立三個衍生變數：

- `Sleep_Activity_Score = Sleep Hours * Physical Activity (hrs/week)`
- `Stimulant_Index = Caffeine Intake (mg/day) + Alcohol Consumption (drinks/week) * 10`
- `Diet_Stimulant_Balance = Diet Quality (1-10) - (Stimulant_Index / 100)`

類別變數處理：

- Yes/No 二元欄位轉換為 1/0
- `Gender` 與 `Occupation` 使用 `pd.get_dummies`
- 使用 `StandardScaler` 標準化
- 使用 80% / 20% train-test split，`random_state=42`

## Models

本作業依規定使用 `LinearRegression`，並建立三個模型版本：

| Model | Description |
|---|---|
| Model 1 Baseline | 使用所有數值化與轉換後的變數 |
| Model 2 Feature Selection | 依據 Model 1 係數絕對值挑選前 10 個重要變數 |
| Model 3 Interaction Terms | 加入壓力、睡眠、咖啡因、運動等交互作用項 |

## Results

| Model | R2 | MAE | RMSE |
|---|---:|---:|---:|
| Model 1 Baseline | 0.734041 | 0.880994 | 1.110120 |
| Model 2 Feature Selection | 0.727553 | 0.893405 | 1.123579 |
| Model 3 Interaction Terms | 0.744755 | 0.870597 | 1.087530 |

最佳模型為 `Model 3 Interaction Terms`：

- R2 約為 0.7448，可解釋約 74.48% 的焦慮分數變異。
- MAE 約為 0.8706，代表平均預測誤差約 0.87 分。
- RMSE 約為 1.0875，代表整體誤差約 1.09 分。

本作業是回歸問題，因此不使用分類問題的 accuracy，而是以 R2、MAE、RMSE 評估模型表現。

## Output Files

主要輸出檔案：

- `outputs/anxiety_level_distribution.png`
- `outputs/important_feature_scatterplots.png`
- `outputs/numeric_correlation_heatmap.png`
- `outputs/model_evaluation_comparison.png`
- `outputs/prediction_plot_with_ci.png`
- `outputs/model_evaluation_metrics.csv`
- `outputs/prediction_confidence_interval_summary.csv`

`prediction_plot_with_ci.png` 使用 `seaborn.regplot(ci=95)`，因此圖中的 confidence interval 為 95%。

## How to Run

使用 uv 建立環境並安裝套件：

```bash
uv sync
```

執行 notebook：

```bash
uv run jupyter notebook HW3.ipynb
```

或使用 VS Code / Jupyter Lab 開啟 `HW3.ipynb` 後依序執行所有 cell。

## Repository Structure

```text
.
├── HW3.ipynb
├── enhanced_anxiety_dataset.csv
├── outputs/
│   ├── anxiety_level_distribution.png
│   ├── important_feature_scatterplots.png
│   ├── numeric_correlation_heatmap.png
│   ├── model_evaluation_comparison.png
│   ├── prediction_plot_with_ci.png
│   ├── model_evaluation_metrics.csv
│   └── prediction_confidence_interval_summary.csv
├── reports/
│   └── HW3_report_draft.md
├── pyproject.toml
├── uv.lock
└── README.md
```

## Submission Notes

正式繳交時建議包含：

- 主程式：`HW3.ipynb`
- 報告 PDF
- GPT 對話 PDF
- NotebookLM 研究摘要
- 網路上主流或更優解法比較
- 必要圖表與資料檔

若上傳至 GitHub，請在繳交報告中附上此 repository 連結。
