# HW3 規格紀錄

## 作業摘要

- 本專案為大學資料科學作業「HW3：多元線性回歸問題」。
- 主程式使用 Python Jupyter Notebook：`4112064224.ipynb`。
- Notebook 必須嚴格遵循 CRISP-DM 六大階段進行 Markdown 標題排版與流程說明：
  - Business Understanding
  - Data Understanding
  - Data Preparation
  - Modeling
  - Evaluation
  - Deployment
- 預測任務：利用個人的生活習慣、背景與生理數據，建立 Multiple Linear Regression 模型來預測焦慮程度。

## 資料集要求

- Source: Public Kaggle dataset。
- Dataset: Social Anxiety Dataset。
- Kaggle 來源：https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset
- 本地檔案：`enhanced_anxiety_dataset.csv`。
- 資料筆數：11,000 筆。
- 欄位數：19 欄，包含數值與類別型變數，符合 10 到 20 個特徵的作業規定。
- 缺失值：無缺失值。

## 已選資料集

目前採用資料集：

- Social Anxiety Dataset
- 本地檔案：`enhanced_anxiety_dataset.csv`
- Kaggle 來源：https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset

## 目標變數

- `Anxiety Level (1-10)`。
- 可視為連續型焦慮程度分數，用於多元線性回歸預測。

## 特徵規劃

自變數包含年齡、性別、睡眠時間、運動時數、咖啡因攝取、酒精攝取、抽菸、焦慮家族史、壓力等級、心率、呼吸率、冒汗程度、暈眩、藥物使用、治療次數、近期重大生活事件、飲食品質與職業等欄位。

Data Preparation 必須建立以下衍生變數：

- `Sleep_Activity_Score = Sleep Hours * Physical Activity (hrs/week)`
- `Stimulant_Index = Caffeine Intake (mg/day) + Alcohol Consumption (drinks/week) * 10`
- `Diet_Stimulant_Balance = Diet Quality (1-10) - (Stimulant_Index / 100)`

類別處理規則：

- Yes/No 二元欄位轉換為 1/0。
- `Gender`、`Occupation` 使用 `pd.get_dummies` 進行 One-Hot Encoding。
- 使用 80% / 20% train-test split，`random_state=42`。
- 使用 `StandardScaler` 進行標準化。

## 建模要求

- 作業核心模型必須使用 `sklearn.linear_model.LinearRegression`。
- 不使用 Random Forest、Gradient Boosting 或其他非線性集成模型作為主模型。
- Notebook 必須訓練三個模型版本：
  - Model 1 Baseline：使用所有數值化與轉換後變數。
  - Model 2 Feature Selection：依據 Model 1 係數絕對值挑選前 10 個重要變數重新訓練。
  - Model 3 Interaction Terms：加入合理交互作用項，例如 `Stress_x_Sleep`、`Stress_x_Caffeine`、`Stress_x_PhysicalActivity`、`Sleep_x_DietQuality`。

## 評估要求

- 分別計算 Model 1、Model 2、Model 3 的 R2、MAE、RMSE。
- 必須繪製預測圖與 95% confidence interval。
- 建議使用 `seaborn.regplot(x=y_test, y=y_pred, ci=95)` 或等價方式呈現回歸線與信賴區間。
- 使用 `matplotlib.pyplot` 與 `seaborn` 進行圖表繪製。

## 報告要求

PDF 報告需包含：

- CRISP-DM 六階段說明。
- 資料集來源與 Kaggle 連結。
- Feature Engineering、類別編碼、標準化與資料切分說明。
- 三個 LinearRegression 模型版本的建模與比較。
- R2、MAE、RMSE 評估結果。
- 預測圖與 confidence interval。
- 變數影響方向與應用場景。
- GPT 輔助內容。
- NotebookLM 摘要，至少 100 個中文字。

## 繳交檔案

- Main program: `4112064224.ipynb`。
- Report: PDF。
- GPT conversation export: PDF 或納入報告。
- Optional README if using GitHub or Colab。
- Final zip: `4112064224.zip`。

## 決策紀錄

- 2026-05-17：建立專案專用 `.agent/skills` 與 `.agent/records`。
- 2026-05-17：`.agent/records` 內的紀錄統一使用繁體中文；專有名詞、程式檔名、資料集名稱、指標縮寫與原始英文標題可保留英文。
- 2026-05-17：選用 `enhanced_anxiety_dataset.csv` 作為 HW3 多元線性回歸資料集。
- 2026-05-17：使用 `uv` 建立 Python 環境，主程式採用 Jupyter Notebook。
- 2026-05-18：確認 Auto Regression 不適合作為本資料集主模型，因資料不是時間序列。
- 2026-05-19：使用者提供新版 spec，要求嚴格改為三模型架構：Baseline、Feature Selection top 10、Interaction Terms；特徵工程需加入三個衍生變數，預測圖需使用 95% confidence interval。

## 待確認問題

- 最終報告 PDF 檔名是否使用 `4112064224.pdf`？
