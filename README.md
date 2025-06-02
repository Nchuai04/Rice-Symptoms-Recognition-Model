# 水稻徵狀影像辨識模型
## 介紹 
> 水稻徵狀影像辨識模型主要用於辨識水稻病害、蟲害、營養障礙與其他等四大類別。使用者可於田間拍攝到水稻徵狀照片後，透過此模型作快速辨識。
> 團隊利用視覺注意力網路（Visual Attention network，VAN）進行AI影像辨識訓練，VAN利用以下兩種方法來對圖像建立關聯。
> 第一是具備近似於self-attention 的機制來捕捉long-range dependence；
> 第二是使用大核卷積（large kernel convolution）來建立關聯性並產生注意力地圖。

## 功能
> 使用Visual-Attention-Network (VAN)架構進行水稻的病害、蟲害、營養障礙與其他的影像辨識訓練。

## 呈現結果
|                       原始圖片                       |                          推論結果                           |
| :--------------------------------------------------------------: | :---------------------------------------------------------------------: |
| <img src="results\ori_27975_1.jpg" alt="raw" width="600"> | <img src="results\demo_27975_1.png" alt="inference" width="600"> |

## 模型
### 模型架構
- 輸入層（Input Layer）
  - 接受尺寸為𝐻×𝑊×𝐶的輸入圖像，其中𝐻為高度，𝑊為寬度，𝐶為通道數。
- 初始卷積層（Initial Convolution Layer）
  - 使用卷積層將圖像的通道數轉換為所需的特徵數，並進行初步的特徵提取。
- 多頭自注意力層（Multi-Head Self-Attention Layers）
  - 多層堆疊的自注意力機制，每層包含多頭自注意力模塊：
    - 查詢、鍵、值（Query, Key, Value）計算
    - 多頭自注意力機制殘差連接（Residual Connection）
    - 層歸一化（Layer Normalization）
- 位置編碼（Positional Encoding）
  - 為每個特徵圖位置添加位置信息，以捕捉空間關係。
- 層次化結構（Hierarchical Structure）
  - 將多層注意力機制分組為不同的層次，每個層次進行特徵下采樣（例如使用池化層或卷積層），以獲取多尺度特徵。
- 融合多尺度信息（Multi-Scale Information Fusion）
  - 通過融合來自不同層次的特徵，提升模型的特徵表達能力。
- 自適應權重分配（Adaptive Weight Allocation）
  - 動態調整各注意力頭的重要性，提升模型對重要信息的關注能力。
- 完全連接層（Fully Connected Layers）
  - 將最終的特徵圖展平並輸入到一個或多個全連接層，以進行分類或回歸等任務。
- 輸出層（Output Layer）
  - 最終的輸出層，根據具體任務（如分類、分割）給出相應的輸出。

|                       Layer架構                       |                          模型架構                           |
| :--------------------------------------------------------------: | :---------------------------------------------------------------------: |
| <img src="results\LKA.png" alt="raw" width="300"> | <img src="results\structure.png" alt="inference" width="300"> |

### 訓練方式
參照[mmpretrain](https://mmpretrain.readthedocs.io/zh-cn/latest/index.html)框架進行訓練
- 訓練數據: 將水稻異常區域進行裁切後的資料集
- 損失函數: LabelSmoothLoss
- 優化器: AdamW
- 訓練批次大小: 128
- 學習率: 5e-4 * 1024 / 512

### 預訓練權重


### 模型評估
- accuracy/top1: 97.6825  
- accuracy/top3: 99.5731  
- accuracy/top5: 99.7561  
- mAP: 97.3660  
- precision: 97.9251  
- recall: 95.4201  
- f1-score: 96.5334

### 模型可視化
使用 類別激活圖(Vis-CAM) 技術進行視覺化分析，以產生模型對稻穗區域的注意力熱圖，並進行解釋。
|   層    |                                 圖片                                  |
| :-----: | :-------------------------------------------------------------------: |
|  backbone.blocks4.2.norm1  |   <img src="results\rIH03_27975_1.jpg" width="200">   |

### 模型穩定性


### 模型公平性


### 資料集
> 使用Rice nutrient, disease and pest symptom image dataset (水稻營養，病蟲害徵狀影像資料集)
> 此資料集為團隊自行蒐集，影像資料為田間實際水稻徵狀照片並將異常區域進行裁切的前處理。
> 關於此資料集的詳細說明請點選[水稻營養，病蟲害徵狀影像資料集](https://aidata.nchu.edu.tw/smarter/dataset/smarter_04_r14088_0_rice_20230118_img1_123456)



## 自訂資料集
> 要注意標註檔徵狀的代碼，更多標註檔的細節可以參考資料集的詳細說明
> 資料格式為JPG、資料標籤請參考標註檔代碼。


## 安裝
參照[mmpretrain安裝方式](https://mmpretrain.readthedocs.io/zh-cn/latest/get_started.html#id2)

## 使用
參照[mmpretrain目錄格式](https://mmpretrain.readthedocs.io/zh-cn/latest/user_guides/config.html)

## 訓練
參照[mmpretrain訓練方式](https://mmpretrain.readthedocs.io/zh-cn/latest/user_guides/train.html)

## 推論
參照[mmpretrain推論方式](https://mmpretrain.readthedocs.io/zh-cn/latest/user_guides/test.html)

## 模型使用步驟
1. 請下載[Rice Symptom Test package壓縮檔](https://aidata.nchu.edu.tw/smarter/nchu/download_all?id=b44c48b8-9c0c-4ecb-bffa-ea01b63b2815)
2. 使用anaconda建立VAN虛擬環境
3. 根據安裝的cuda版本下載[pytorch](https://pytorch.org/get-started/locally/)
4. 下載模型壓縮檔，並解壓縮到一資料夾中
5. 將anaconda終端所在位置移至解壓縮資料夾
6. 安裝Python所需要的庫 (requirement.txt)
7. 將想辨識的水稻照片放置到source資料夾內
8. 運行程式碼 python riceSymptomTest.py
9. 判讀結果的照片自動保存至result資料夾，該照片會顯示判識的水稻問題名稱
    
## 參考文獻
[mmpretrain GitHub](https://github.com/open-mmlab/mmpretrain)

[VAN](https://arxiv.org/abs/2202.09741)

