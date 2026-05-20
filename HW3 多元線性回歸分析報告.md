# HW3 多元線性回歸分析報告

4112064224 電資三 范詠竣  
主程式：`HW3.ipynb`  
資料集：Kaggle - Social Anxiety Dataset  
資料檔：`enhanced_anxiety_dataset.csv`

## 一、研究主題與資料集來源

本作業使用 Kaggle 上的 Social Anxiety Dataset 作為分析資料，目標是利用個人的生活習慣、背景資料與生理數據，建立多元線性回歸模型來預測焦慮程度 `Anxiety Level (1-10)`。

資料來源：

- Kaggle Dataset: Social Anxiety Dataset
- Kaggle Link: https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset
- 本地資料檔：`enhanced_anxiety_dataset.csv`

資料集共有 11,000 筆資料、19 個欄位，包含數值型與類別型變數，沒有缺失值。目標變數為 `Anxiety Level (1-10)`，可視為 1 到 10 分的連續型焦慮程度分數，因此適合使用多元線性回歸進行分析。

## 二、CRISP-DM 分析流程

### 1. Business Understanding

本分析想探討生活習慣、壓力狀態、個人背景與生理數據是否會影響焦慮程度。焦慮程度可能與睡眠、運動、咖啡因攝取、壓力程度、心率、治療次數、飲食品質等因素有關，因此本作業使用這些變數建立 Multiple Linear Regression 模型。

成功標準如下：

- 使用 `sklearn.linear_model.LinearRegression` 建立多元線性回歸模型。
- 使用 R2、MAE、RMSE 評估模型表現。
- 完成特徵選擇，並比較不同模型版本。
- 繪製預測圖與 95% confidence interval。
- 根據回歸係數說明變數對焦慮程度的正向或負向影響。

本模型僅作為課堂作業與資料科學分析練習，不可作為正式醫療診斷或心理健康判定工具。

### 2. Data Understanding

使用 `pandas` 讀取資料後，確認資料共有 11,000 筆、19 欄。資料沒有缺失值，也沒有重複資料。Notebook 中已檢查：

- `df.shape`
- `df.info()`
- `df.describe(include="all")`
- 缺失值檢查
- 重複資料檢查

主要圖表：

- 目標變數分佈圖：`outputs/anxiety_level_distribution.png`![anxiety_level_distribution](https://hackmd.io/_uploads/S1bdcMi1zx.png)

- 重要特徵散佈圖：`outputs/important_feature_scatterplots.png`
![important_feature_scatterplots](https://hackmd.io/_uploads/rkhbsfoJMe.png)


- 數值特徵相關係數熱力圖：`outputs/numeric_correlation_heatmap.png`
![numeric_correlation_heatmap](https://hackmd.io/_uploads/SJ17szjyze.png)



初步觀察：

- `Stress Level (1-10)` 與焦慮程度呈現明顯正向關係。
- `Caffeine Intake (mg/day)` 也可能與較高焦慮程度相關。
- `Sleep Hours`、`Physical Activity (hrs/week)` 可能與較低焦慮程度相關。

### 3. Data Preparation

本階段先複製原始資料為 `model_df`，避免直接修改原始資料。接著進行特徵工程、類別編碼、資料切分與標準化。

新增衍生變數：

- `Sleep_Activity_Score = Sleep Hours * Physical Activity (hrs/week)`
- `Stimulant_Index = Caffeine Intake (mg/day) + Alcohol Consumption (drinks/week) * 10`
- `Diet_Stimulant_Balance = Diet Quality (1-10) - (Stimulant_Index / 100)`

類別變數處理：

- Yes/No 二元欄位轉換為 1/0，例如 `Smoking`、`Medication`、`Dizziness`。
- `Gender` 與 `Occupation` 使用 `pd.get_dummies(..., drop_first=True)` 進行 One-Hot Encoding。

資料切分與標準化：

- 訓練集：80%，共 8,800 筆。
- 測試集：20%，共 2,200 筆。
- `random_state=42`，確保結果可重現。
- 使用 `StandardScaler` 對模型輸入欄位進行標準化。

### 4. Modeling

依作業要求，三個模型皆使用 `sklearn.linear_model.LinearRegression`，沒有使用 Random Forest 或 Gradient Boosting 作為主模型。

Model 1 Baseline：

- 使用所有數值化與轉換後的變數建立多元線性回歸模型。

Model 2 Feature Selection：

- 依據 Model 1 標準化後的係數絕對值，挑選前 10 個重要變數重新訓練模型。
- 此模型符合特徵選擇要求。

Model 3 Interaction Terms：

- 加入交互作用項，檢查變數之間是否存在共同影響。
- 加入的交互作用項包含：
  - `Stress_x_Sleep`
  - `Stress_x_Caffeine`
  - `Stress_x_PhysicalActivity`
  - `Sleep_x_DietQuality`

### 5. Evaluation

本作業是回歸問題，因此不使用分類任務的 accuracy，而是使用 R2、MAE、RMSE 評估模型表現。

模型評估結果如下：

| Model | R2 | MAE | RMSE |
|---|---:|---:|---:|
| Model 1 Baseline | 0.734041 | 0.880994 | 1.110120 |
| Model 2 Feature Selection | 0.727553 | 0.893405 | 1.123579 |
| Model 3 Interaction Terms | 0.744755 | 0.870597 | 1.087530 |

最佳模型為 Model 3 Interaction Terms：

- R2 = 0.744755，代表模型約可解釋 74.48% 的焦慮分數變異。
- MAE = 0.870597，代表在 1 到 10 分焦慮量表中，平均預測誤差約 0.87 分。
- RMSE = 1.087530，代表加重較大誤差後，整體誤差約 1.09 分。

預測圖與信賴區間：

- 預測圖檔案：`outputs/prediction_plot_with_ci.png`![prediction_plot_with_ci](https://hackmd.io/_uploads/SkULsGoyfg.png)![prediction_interval](https://hackmd.io/_uploads/B1KvsfiJzg.png)


- 圖中使用 `seaborn.regplot(ci=95)`，因此 confidence interval 設定為 95%。
- 此 confidence interval 代表平均預測回歸線的不確定範圍，不是每一筆個人樣本的保證範圍。
- 另外輸出 `outputs/prediction_confidence_interval_summary.csv`，列出 Actual Anxiety Level 1 到 10 對應的平均預測值 95% confidence interval。

模型比較圖：

- `outputs/model_evaluation_comparison.png`![model_evaluation_comparison](https://hackmd.io/_uploads/S1vqiMjyMe.png)

- R2 是越高越好，MAE 與 RMSE 是越低越好，因此圖中將 R2 與誤差指標分開呈現，避免誤讀。

### 6. Deployment

從最佳模型與回歸係數方向可以整理出以下結論：

- 壓力程度、咖啡因攝取、治療次數等變數若係數為正，表示其增加時焦慮分數也傾向提高。
- 睡眠時間、運動時數、飲食品質等變數若係數為負，表示較好的生活習慣可能與較低焦慮程度相關。
- 加入交互作用項後，模型表現提升，表示壓力與睡眠、咖啡因、運動之間可能存在共同影響。

實際應用上，此模型可作為心理健康機構、校園輔導單位或健康管理系統的初步風險篩檢輔助工具。不過，此模型不可取代專業心理諮商或醫療診斷。

## 三、GPT 輔助內容

本作業使用 GPT / Code Agent 協助完成以下內容：

- 建立 `.agent/skills` 與 `.agent/records`，紀錄作業規格、CRISP-DM 進度與對話摘要。
- 協助選定 Social Anxiety Dataset 並判斷其適合作為多元線性回歸資料集。
- 協助撰寫 Jupyter Notebook 中的 CRISP-DM 六階段內容。
- 協助完成資料前處理、特徵工程、類別編碼、標準化與 train-test split。
- 協助建立三個 LinearRegression 模型版本。
- 協助產生模型評估表、預測圖、95% confidence interval 與模型比較圖。
- 協助整理報告草稿與繳交檢查清單。

GPT 對話紀錄來源：

- `.agent/records/CHAT_LOG.md`
- 另需使用 pdfCrowd 或瀏覽器列印功能，將 GPT 對話過程匯出為 PDF，並附於報告或壓縮檔中。

## 四、NotebookLM 研究摘要

此段需放入 NotebookLM 產生的研究摘要，至少 100 個中文字。建議將 Kaggle 資料集說明、Notebook 分析流程與模型結果提供給 NotebookLM，請它整理研究摘要。

請在正式報告前貼上 NotebookLM 實際輸出的摘要：

> TODO：貼上 NotebookLM 摘要，至少 100 個中文字。

可使用的 NotebookLM 提示詞：

```text
請根據 Social Anxiety Dataset、多元線性回歸分析流程與模型結果，整理一段至少 100 個中文字的研究摘要。摘要需說明資料來源、研究目的、主要特徵、建模方法、評估指標與主要發現，語氣適合放入大學資料科學作業報告。
```

## 五、網路上主流或更優解法之比較

本作業主題要求使用 Multiple Linear Regression，因此主模型限制為 `LinearRegression`。不過，網路上常見的更強方法會使用 Ridge Regression、Lasso Regression、ElasticNet、Random Forest 或 Gradient Boosting 等模型。

先前檢查 `social-anxiety-and-lifestyle-analysis.ipynb` 時，該 notebook 採用多模型比較流程，結果大致如下：

| 方法 | R2 | RMSE | 說明 |
|---|---:|---:|---|
| Ridge Regression | 約 0.736 | 約 1.105 | 線性模型加入 L2 正則化，表現接近本作業模型 |
| Random Forest | 約 0.773 | 約 1.026 | 非線性集成模型，可捕捉更複雜關係 |
| Gradient Boosting | 約 0.778 | 約 1.014 | 該比較中表現最佳，但不符合本作業主模型限制 |

與本作業最佳模型 Model 3 Interaction Terms 相比：

- 本作業 Model 3：R2 = 0.744755，RMSE = 1.087530。
- Gradient Boosting 表現較好，可能因為它能捕捉非線性關係與複雜特徵交互作用。
- 但本作業要求多元線性回歸，因此仍以 LinearRegression 作為主模型。
- Model 3 加入交互作用項後，已在保持線性模型可解釋性的前提下提升表現。

## 六、預測結果與圖表整理



1. `outputs/anxiety_level_distribution.png`

   - 說明目標變數分布。![anxiety_level_distribution](https://hackmd.io/_uploads/BkgLf2zoJfe.png)

2. `outputs/important_feature_scatterplots.png`
   - 說明壓力、睡眠、運動、咖啡因與焦慮程度的關係。![important_feature_scatterplots](https://hackmd.io/_uploads/rkhbsfoJMe.png)


3. `outputs/numeric_correlation_heatmap.png`
   - 說明數值特徵與目標變數的相關性。![numeric_correlation_heatmap](https://hackmd.io/_uploads/SJ17szjyze.png)
4. `outputs/model_evaluation_comparison.png`
   - 比較三個 LinearRegression 模型的 R2、MAE、RMSE。![model_evaluation_comparison](https://hackmd.io/_uploads/HysonGiyGl.png)

5. `outputs/prediction_plot_with_ci.png`
   - 呈現最佳模型的預測結果與 95% confidence interval。![prediction_interval](https://hackmd.io/_uploads/HJOhnfs1fl.png)
