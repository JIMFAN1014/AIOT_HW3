# CRISP-DM 紀錄

## Business Understanding

已依 2026-05-19 新版 spec 更新並寫入 `4112064224.ipynb`。

本作業探討生活習慣、壓力狀態、個人背景與生理數據是否會影響焦慮程度。預測目標為 `Anxiety Level (1-10)`，此分數可視為連續型目標，因此適合使用 Multiple Linear Regression 進行建模與解釋。

成功指標：

- 使用 `sklearn.linear_model.LinearRegression` 建立多元線性回歸模型。
- 使用 R2、MAE、RMSE 評估模型準確度。
- 比較三個模型版本：Baseline、Feature Selection、Interaction Terms。
- 根據回歸係數說明主要變數對焦慮程度的正向或負向影響。
- 繪製預測圖並加入 95% confidence interval。

限制聲明：

- 本分析僅作為課堂作業與資料科學練習，不可作為醫療診斷或心理健康評估工具。

## Data Understanding

已依新版 spec 更新並寫入 `4112064224.ipynb`。

目前已檢查資料：

- 資料集名稱：Social Anxiety Dataset。
- 本地檔案：`enhanced_anxiety_dataset.csv`。
- Kaggle 來源：https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset
- 資料筆數：11,000 筆。
- 欄位數：19 欄。
- 缺失值：0。
- 重複資料：0。
- 目標變數：`Anxiety Level (1-10)`。

Notebook 目前包含下列資料理解輸出：

- `df.shape`
- `df.info()`
- `df.describe(include="all")`
- 缺失值檢查
- 重複資料檢查
- 目標變數分佈直方圖
- 重要特徵與焦慮等級的散佈圖
- 數值特徵相關係數熱力圖

已輸出圖檔：

- `outputs/anxiety_level_distribution.png`
- `outputs/important_feature_scatterplots.png`
- `outputs/numeric_correlation_heatmap.png`

初步觀察：

- `Stress Level (1-10)`、`Caffeine Intake (mg/day)` 等變數與焦慮程度呈正向關係。
- `Sleep Hours`、`Physical Activity (hrs/week)` 等變數可能與焦慮程度呈負向關係。

## Data Preparation

已依新版 spec 更新並寫入 `4112064224.ipynb`。

資料準備方式：

- 建立資料副本 `model_df`，避免直接修改原始 `df`。
- 建立三個衍生變數：
  - `Sleep_Activity_Score = Sleep Hours * Physical Activity (hrs/week)`
  - `Stimulant_Index = Caffeine Intake (mg/day) + Alcohol Consumption (drinks/week) * 10`
  - `Diet_Stimulant_Balance = Diet Quality (1-10) - (Stimulant_Index / 100)`
- Yes/No 二元欄位轉為 1/0：
  - `Smoking`
  - `Family History of Anxiety`
  - `Dizziness`
  - `Medication`
  - `Recent Major Life Event`
- `Gender` 與 `Occupation` 使用 `pd.get_dummies(..., drop_first=True, dtype=int)` 進行 One-Hot Encoding。
- 訓練集 / 測試集切分：80% / 20%，`random_state=42`。
- 使用 `StandardScaler` 對數值化後的模型輸入欄位進行標準化。

實際資料切分結果：

- 原始資料筆數：11,000。
- 訓練集：8,800 筆。
- 測試集：2,200 筆。
- Baseline 模型輸入欄位數：32 個。

## Modeling

已依新版 spec 更新並寫入 `4112064224.ipynb`。

本作業核心規定為使用 `sklearn.linear_model.LinearRegression`，因此目前三個模型皆使用 Linear Regression，不使用 Random Forest、Gradient Boosting 或其他非線性集成模型。

三個模型版本：

- Model 1 Baseline：使用所有數值化與轉換後欄位進行多元線性回歸。
- Model 2 Feature Selection：依據 Model 1 標準化後的係數絕對值，挑選前 10 個重要變數重新訓練。
- Model 3 Interaction Terms：加入交互作用項後重新訓練線性模型。

Model 3 加入的交互作用項：

- `Stress_x_Sleep`
- `Stress_x_Caffeine`
- `Stress_x_PhysicalActivity`
- `Sleep_x_DietQuality`

目前 Model 2 依據 Model 1 係數絕對值選出的前 10 個重要變數會在 notebook 執行時印出，並用於重新訓練模型。

## Evaluation

已依新版 spec 更新並成功執行 `4112064224.ipynb`。

評估方式：

- 使用 R2、MAE、RMSE 評估 Model 1、Model 2、Model 3。
- 將模型評估結果輸出為 `outputs/model_evaluation_metrics.csv`。
- 選擇 R2 最高的模型繪製預測圖。
- 使用 `seaborn.regplot(ci=95)` 疊加回歸線與 95% confidence interval。

目前測試集評估結果：

- Model 1 Baseline：
  - R2：0.734041
  - MAE：0.880994
  - RMSE：1.110120
- Model 2 Feature Selection：
  - R2：0.727553
  - MAE：0.893405
  - RMSE：1.123579
- Model 3 Interaction Terms：
  - R2：0.744755
  - MAE：0.870597
  - RMSE：1.087530

目前 Model 3 的 R2 最高，MAE 與 RMSE 最低，代表加入交互作用項後模型表現最佳。

2026-05-19 補強說明：

- 使用者指出 Evaluation 與 Deployment 結論不夠清楚，特別是「信賴區間是多少」與「模型準確度是多少」。
- 已在 notebook 中補充說明：本作業是回歸問題，不適合使用分類問題的 accuracy，應以 R2、MAE、RMSE 說明模型準確度與誤差。
- 最佳模型 Model 3 的解釋力為 R2 = 0.744755，可描述為約 74.48% 的焦慮分數變異可由模型解釋。
- Model 3 的 MAE = 0.870597，代表在 1 到 10 分焦慮量表中，平均預測誤差約 0.87 分。
- Model 3 的 RMSE = 1.087530，代表加重較大誤差後，整體誤差約 1.09 分。
- 預測圖使用 `seaborn.regplot(ci=95)`，因此 confidence interval 設定為 95%。
- 已新增 `outputs/prediction_confidence_interval_summary.csv`，列出 Actual Anxiety Level 1 到 10 對應的平均預測值 95% confidence interval。
- 已更新 `outputs/model_evaluation_comparison.png`，將 R2 與 MAE/RMSE 分開呈現，避免把「越高越好」與「越低越好」的指標混在同一尺度造成誤讀。

已輸出檔案：

- `outputs/model_evaluation_metrics.csv`
- `outputs/prediction_plot_with_ci.png`
- `outputs/prediction_confidence_interval_summary.csv`
- `outputs/model_evaluation_comparison.png`

## Deployment

已依新版 spec 更新並寫入 `4112064224.ipynb`。

部署與結論重點：

- 最佳模型為 Model 3 Interaction Terms。
- 模型解釋力：R2 約 0.7448，可解釋約 74.48% 的焦慮分數變異。
- 平均絕對誤差：MAE 約 0.8706，平均約差 0.87 分。
- 均方根誤差：RMSE 約 1.0875，整體誤差約 1.09 分。
- 預測圖的 confidence interval 為 95%，代表平均預測回歸線的不確定範圍，不是個別樣本的保證範圍。
- 根據回歸係數說明變數影響方向。
- 若 `Stress Level (1-10)`、`Caffeine Intake (mg/day)`、`Therapy Sessions (per month)` 等變數係數為正，表示其增加時焦慮分數也傾向提高。
- 若 `Sleep Hours`、`Physical Activity (hrs/week)`、`Diet Quality (1-10)` 等變數係數為負，表示較好的睡眠、運動與飲食品質可能與較低焦慮程度相關。
- 此模型可作為心理健康機構、校園輔導單位或健康管理系統的初步風險篩檢輔助工具。
- 本模型不能取代專業心理諮商或醫療診斷。

後續繳交規劃：

- 主程式：`4112064224.ipynb`。
- 報告需放入資料來源、CRISP-DM 流程、GPT 輔助內容、NotebookLM 摘要、模型比較與結果圖。
- 最終壓縮檔需依學號命名。
