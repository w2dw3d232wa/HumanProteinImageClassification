# 人類蛋白質圖譜分類 (Human Protein Atlas Image Classification)

## 專案簡介
本專案為深度學習基礎課程 (Lab 1) 的進階延伸實作。從基礎的單通道手寫數字辨識 (MNIST)，跨足至真實世界的生醫影像分析。專案目標是透過分析四通道 (Red, Green, Blue, Yellow) 螢光顯微影像，預測未知蛋白質在細胞內的精確分布位置 (Subcellular Localization)。這是一個高度資料不平衡的「多重標籤分類 (Multi-label Classification)」任務。

## 開發環境與工具
* **語言:** Python 3
* **核心框架:** TensorFlow, Keras
* **影像與資料處理:** OpenCV, Pandas, NumPy, Matplotlib
* **開發環境:** Google Colab / Kaggle Notebook

## 📁 資料集來源 (Dataset)
本專案使用的人類蛋白質圖譜 (Human Protein Atlas) 顯微影像資料集極為龐大（達數十 GB），因此未包含在本 GitHub 儲存庫中。

若您希望在本地端或雲端環境重現此專案的訓練結果，請依循以下步驟獲取資料：
1. 前往 Kaggle 官方競賽頁面：[Human Protein Atlas Image Classification](https://www.kaggle.com/c/human-protein-atlas-image-classification)
2. 閱讀並同意比賽的資料使用條款。
3. 將資料集下載並解壓縮，確保 `train.csv` 與訓練圖片資料夾放置於專案程式碼能讀取的相對應路徑中。
*(註：若您直接將本專案的 Notebook 匯入 Kaggle 雲端環境執行，龐大的影像資料集將會由系統自動掛載，無須手動下載。)*

## 模型架構 (CNN)
本專案建構了客製化的卷積神經網路 (Convolutional Neural Network) 來萃取影像特徵：
1. **Input Layer**: 接收 `(256, 256, 4)` 尺寸的四通道生醫影像張量（分別代表微管、標靶蛋白質、細胞核、內質網）。
2. **Convolutional Blocks**: 包含多層 `Conv2D` (32, 64, 128 個濾波器) 搭配 `ReLU` 激勵函數與 `MaxPooling2D`，模擬顯微鏡視野從局部特徵到整體輪廓的特徵濃縮。
3. **Fully Connected Layers**: 透過 `Flatten` 展平後，接上 512 個神經元的 `Dense` 層，並配合 `Dropout(0.5)` 防止模型死背訓練資料 (Overfitting)。
4. **Output Layer**: 輸出層為 28 個神經元，搭配 `Sigmoid` 激勵函數，獨立計算出 28 種細胞結構的存在機率。

## 核心技術與優化策略
在處理真實生物數據時，本專案實作了以下關鍵技術來克服挑戰：
* **記憶體最佳化 (OOM Prevention):** 捨棄一次性載入龐大資料，改用 `tf.data.Dataset` 建立非同步資料輸送帶，並結合 `AUTOTUNE` 與 `prefetch` 大幅提升 GPU 訓練效率。
* **多重標籤轉換 (Multi-hot Encoding):** 將文字標籤轉換為 28 維陣列，以應對單一蛋白質可能同時存在於多個胞器中的生化特性。
* **資料不平衡處理 (Class Imbalance):** 針對極罕見樣本（如佔比極低的 Cytokinetic bridge 細胞質分裂橋），透過設定 `class_weight` 施加高倍數的誤判懲罰，成功將罕見特徵的預測把握度從 6% 大幅提升至 30% 以上。
* **決策閾值調校 (Threshold Tuning):** 針對罕見特徵放寬決策及格線（例如將 Threshold 調降至 0.3），在醫療與生物影像分析中有效降低漏診率 (False Negatives)。

---
**Author:** 施程耀 (Oliver Shih)
**Institution:** National Taiwan Ocean University (NTOU)
**Field of Study:** Life Science / Bioinformatics / Deep Learning Applications
