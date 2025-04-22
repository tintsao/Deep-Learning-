# 腦瘤辨識

## 說明
資料來源：義大醫院
本資料共有 120 組腦部 3D MRI 掃描影像，分別來自：60 位正常受試者，及 60 位腦瘤病患。
目標是建立模型來預測 MRI 影像是否來自腦瘤患者。

## EDA
根據 DICOM header 的 Window Width (WW) 與 Window Center (WC) 進行轉換：
- 影像對比調整：將像素限制於 【lowest, highest】 可視範圍內，並轉換為 【 0 ~ 255 】 用於顯示與 CNN 輸入。
- 3D 體積補齊：對每位受試者進行插值處理，將 T1/T2 MRI 統一為大小 (高度 x 寬度 x 深度) = (512 x 512 x 22)。

## 模型架構

### Late Fusion
- 對所有切片個別使用 CNN 擷取特徵
- 將每張切片輸入預訓練模型（如 VGG16）後抽出 feature map
- 將 22 張切片的特徵向量拼接（concatenate）為一長向量
- 接上 Dropout、ReLU、Linear 分類層做二分類判斷
