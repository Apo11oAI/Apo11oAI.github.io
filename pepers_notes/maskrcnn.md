> 论文地址：https://arxiv.org/abs/1703.06870 
>
> TF复现地址：https://github.com/facebookresearch/Detectron

# 领域
计算机视觉 

# 主题
instance segmentation
区分
识别

# 相关工作

## RCNN
The Region-based CNN
a manage-able number of candidate object regions
## Instance Segmentation
Driven by the effectiveness of R-CNN, many approaches to instance segmentation are basedonsegment proposals.

# 主要思想
## 网络架构设计理念
1. 理念简单易懂
2. 灵活
3. 通用
## 基本思路
- 扩展Faster R-CNN
- 添加一个在每一个关注区（Region of Interest (RoI)）预测分割mask的分支
- 该分支与已存在的分类分支、边框回归分支并行
# 实践

## 数据集

[MS COCO 国内下载源](http://datadownload.f3322.net:666/share/coco2014/)
### mask表示

### 对于关键点检测
view each keypoint as a one-hotbinary mask

 

## 模型
![概览](../imgs/maskrcnn-1.png)
![概览](../imgs/maskrcnn-2.png)

### 特征提取


#### resnet
特征表达能力更强
#### FPN
挖掘多尺度信息
![概览](../imgs/maskrcnn-3.png)
### 生成ROI
#### 区域建议网络（RPN）
注意力机制
![概览](../imgs/maskrcnn-4.png)
#### RoIAlign
去掉RoIPool的量化
#### Region Proposal layer
#### mask分支
predict an `m×m` mask from each RoI using  an  FCN
像素对应（pixel-to-pixel correspondence）
We note that our mask branches have a straightforwardstructure. 
More complex designs have the potential to im-prove performance but are not the focus of this work.
#### 损失函数
**multi-task** loss on **each** sampled RoI on the *k*-th mask (other mask outputs do not contribute to the loss)
分类误差+检测误差+分割误差，即`L=Lcls+Lbox+Lmask`

#####L<sub>cls</sub>、L<sub>bbox</sub>：
 
 利用全连接预测出每个RoI的所属类别及其矩形框坐标值，可以参看Fast RCNN网络中的介绍。

 #####L<sub>mask</sub>：
 L<sub>mask</sub> is only defined on the *k*-th mask (other mask outputs do not contribute to the loss)

 mask分支采用FCN对每个RoI的分割输出维数为`K*m*m`（其中：m表示RoI Align特征图的大小），即K个类别的m*m的二值mask;
 
 保持m*m的空间布局，pixel-to-pixel操作需要保证RoI特征 映射到原图的对齐性，这也是使用RoIAlign解决对齐问题原因，减少像素级别对齐的误差。
 
 `K*m*m`二值mask结构解释：最终的FCN输出一个K层的mask，每一类为一层，Log输出，用0.5作为阈值进行二值化，产生背景和前景的分割Mask

这样，Lmask 使得网络能够输出每一类的 mask，且不会有不同类别 mask 间的竞争. 分类网络分支预测 object 类别标签，以选择输出 mask，对每一个ROI，如果检测得到ROI属于哪一个分 类，就只使用哪一个分支的相对熵误差作为误差值进行计算。

（举例说明：分类有3类（猫，狗，人），检测得到当前ROI属于“人”这一类，那么所使用的Lmask为“人”这一分支的mask，即，每个class类别对应一个mask可以有效避免类间竞争（其他class不贡献Loss）

 对每一个像素应用sigmoid，然后取RoI上所有像素的交叉熵的平均值作为Lmask。
## 训练
### config
1. resize = (短边=800像素)
2. mini-batch = 2 images per GPU 
    - ResNeXt = 1 images per GPU 
3. sampled RoIs:N(C4 backbone = 64 | FPN = 512)
    1. positive to negatives: 1:3
4. num_GPU = 8
5. iterations(epoches) = 160,000
6. 学习率 = 0.02
    - 在第120K时除以10
    - ResNeXt = 0.01
7. weight decay = 0.0001
8. momentum = 0.9
## 预测
### config
1. proposal number
    - C4 backbone = 300 
    - FPN = 1000
2. 模型更改
    - 在proposals中执行边界框预测，并进行非极大值抑制
    - 对剩下的探测框根据评分进行排序，得到100个探测框
    - 只对这100个探测框执行mask分支。

## 效果

# 理论

