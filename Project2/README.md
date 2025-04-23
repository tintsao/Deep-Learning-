
# 帕金森氏症分期之SPECT影像分類

本專案根據帕金森氏症（Parkinson's Disease, PD）患者的 SPECT 腦部影像，預測其病情所處的三個分期（Stage 1、2、3），
同時考慮年齡與性別等流行病學資訊，此任務為多類別影像分類問題。

---

## 資料來源與前處理

- 使用 義大醫院 的病患資料進行訓練與測試。
- 每位受試者的 SPECT 為 3D 醫學影像，我們根據 `index` 欄選取其中三張 slice 作為輸入影像。
- 將選定的 2D slice 中央裁切為 50×50 圖像，以集中保留腦部區域並去除背景雜訊。
- 將灰階影像複製至三通道，以便使用 ImageNet 預訓練權重之 CNN/Transformer 架構。

---

## 使用模型與設計

### 模型架構

Backbone（特徵提取器）：
- 使用 VGG16 的卷積層（features）提取圖像特徵
- 移除 VGG16 原本的分類器（classifier），只保留 CNN 部分
- 使用 AdaptiveAvgPool2d(1) 壓縮特徵圖，得到一個長度為 512 的特徵向量
- 資料整合：額外輸入 age 和 gender 兩個數值，與 CNN 特徵 concat 起來 → 得到長度 514 的輸入向量

分類層
self.classifier = nn.Sequential(
            nn.Linear(512 + 2, num_classes)        
            )
其他效果不佳

- 性別以 0/1 編碼，年齡進行標準化處理（Z-score）
---

## 訓練設定

- Optimizer：AdamW（含 weight decay）
- Loss function：CrossEntropyLoss
- Scheduler：StepLR 每 5 個 epoch 將學習率乘上 0.5
- Epochs：共訓練 30 回合，並加入 EarlyStopping 防止 overfitting
- 影像增強：使用 `RandomCrop`, `RandomHorizontalFlip`, `ColorJitter` 等方式增加訓練多樣性

---

## 評估

- 評估指標：準確率 (accuracy)、F1 score、Loss function
- 最終模型產生 `submission.csv`，欄位為 [id, stage(predict)]
  
---
