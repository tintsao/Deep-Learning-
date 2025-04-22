
# 帕金森氏症分期之SPECT影像分類

本專案根據帕金森氏症（Parkinson's Disease, PD）患者的 SPECT 腦部影像，預測其病情所處的三個分期（Stage 1、2、3），
同時考慮年齡與性別等流行病學資訊，此任務為多類別影像分類問題。

---

## 資料來源與前處理

- 使用 e3 平台提供之 `train.csv` 和 `test.csv` 進行訓練與測試。
- 每位受試者的 SPECT 為 3D 醫學影像，我們根據 `index` 欄選取其中三張 slice 作為輸入影像。
- 將選定的 2D slice 中央裁切為 50×50 圖像，以集中保留腦部區域並去除背景雜訊。
- 將灰階影像複製至三通道，以便使用 ImageNet 預訓練權重之 CNN/Transformer 架構。

---

## 使用模型與設計

### 模型架構

- 使用 VGG16 作為特徵提取
- 將影像特徵 (512 維) 與年齡 + 性別 (2 維) 做 early fusion，組合為 514 維向量
- 分類器採用多層感知器（MLP）：

```python
self.classifier = nn.Sequential(
    nn.Linear(514, 256),
    nn.BatchNorm1d(256),
    nn.ReLU(),
    nn.Dropout(0.3),
    nn.Linear(256, num_classes)
)
```

- 性別以 0/1 編碼，年齡進行標準化處理（Z-score）

---

## ⚙️ 訓練設定

- Optimizer：AdamW（含 weight decay）
- Loss function：CrossEntropyLoss
- Scheduler：StepLR 每 5 個 epoch 將學習率乘上 0.5
- Epochs：共訓練 30 回合，並加入 EarlyStopping 防止 overfitting
- 影像增強：使用 `RandomCrop`, `RandomHorizontalFlip`, `ColorJitter` 等方式增加訓練多樣性

---

## 🧪 評估與提交

- 評估指標：準確率 (accuracy)
- 最終模型產生 `submission.csv`，欄位為 [id, predicted_stage]
- 同步上傳 `.ipynb`、`.html`、`.pdf` 等作業文件至 e3 平台與 Kaggle

---

## 🧩 使用的架構（至少三種）

- VGGNet16（CNN）

所有模型皆融合年齡與性別資訊，並統一輸出三類別預測。

---