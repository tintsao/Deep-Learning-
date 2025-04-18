# 📍 手寫數字辨識（USPS 資料集）
使用美國郵政署（USPS）手寫數字影像資料集，進行影像辨識模型建構與預測。
---

## 資料內容

- 格式：CSV 檔案，每筆資料包含：
- image_id：影像代碼
- digit_id：真實數字（0-9）
- 256個 pixel 值：16×16 的灰階影像已經過斜率修正與大小標準化。
- 檔案：
    train.csv：訓練集（有標籤）
    test.csv：測試集（無標籤）
---

## 模型架構

使用 PyTorch 建立 前饋神經網路（SimpleNN, MLP）:
    `fc1 → BN → ReLU → Dropout → fc2 → BN → ReLU → Dropout → final_fc`

---

## 訓練流程

將訓練資料以 80:20 拆分為訓練與驗證集，使用 DataLoader 做 batch 訓練
（batch_size 自定），Loss 使用 CrossEntropyLoss，Optimizer 使用 Adam、AdamW；
學習率調整策略：ReduceLROnPlateau 或 StepLR
每一輪訓練完後會記錄 loss、accuracy、f1、misclassification_rate
---

## 視覺化與統計

畫出每個數字的樣本數分布長條圖，每個數字隨機顯示10張圖像，確認資料完整性。
訓練及驗證完會畫出 `loss`, `acc`, `f1 score` 等趨勢圖。  
---

## 測試與評估

預測 test.csv 中每筆的數字（分類）。
最後與提供的 test_ans_hw1.csv 做比對計算準確率，目前模型 accuracy 約達 94.87%。
---

## 參考資料

- LeCun, Y., et al. (1990). [USPS 數字資料集預處理原始論文]
