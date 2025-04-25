訓練集影片：https://www.pexels.com/video/a-busy-city-street-at-dusk-with-cars-and-motorcycles-19781692/ 
 
① YOLO 模型準備與目標偵測 

   	•	模型格式：yolov5s.onnx 
	•	偵測目標：car、motorcycle 
	•	推論方式：使用  cv2.dnn 搭配 onnxruntime 
	•	偵測參數：score > 0.4，confidence > 0.5 
	•	裁圖方式：每 N 幀擷取一次（避免重複），過濾模糊/太小/過亮/過暗 

② 資料裁切與去重複

	•	將偵測框的車圖裁下儲存
	•	使用 imagehash 的 phash 過濾重複圖
	•	過濾規則：
	◦	圖像尺寸過小
	◦	圖像太暗/太亮
	◦	Laplacian 檢查模糊度
 
https://zx7978123.medium.com/圖像相似度算法-google以圖搜圖-2-bde3d8c9568d

●感知雜湊演算法（PHash、Perceptual Hash）

Input圖片 -> 縮放大小 -> 灰階化 -> 計算DCT  -> 計算平均值 -> 進一步縮小DCT -> 產生Hash value

③ 半自動分群 → 手動分類測試集

	•	使用 KMeans 對車圖進行分群
	•	每群視覺化 → 分析其內容
	•	將車種手動分類成：
		◦	car/
		◦	motorcycle/
 
④  CNN 分類模型訓練

增加功能：

	•	ImageDataGenerator 做圖片增強（旋轉、亮度、翻轉…）
	•	EarlyStopping（防止過擬合）
	•	ReduceLROnPlateau（自動調整學習率）
 
成果：

	•	訓練與驗證準確率可達 92%+
	•	但泛化能力差（測試集 accuracy 僅 22%）
 
⑤ 測試評估與分類報告

預測類別 → 混淆矩陣、分類報告

發現：

	•	模型極度偏向 motorcycle，誤判嚴重
	•	問題來自：資料不平衡 + 特徵分布差異

改進：

   	•	強化 motorcycle 訓練資料  
   	•	嘗試使用 Pretrained model（如 MobileNet）做遷移學習
