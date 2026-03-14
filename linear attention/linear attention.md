# linear attention

---

## [Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention](https://gemini.google.com/share/c285dcf8588c)

- challenge：transformer架构传统attentionO($N^2$)的时间复杂度

- method：linear attention，传统attention实际上就是一个指数核衡量相似度，对应的映射将低维数据映射到无限维空间，而linear attention将核函数写成两个有限维($N > D^2$)的映射，并采用矩阵的结合律避免冗余计算
  ![77288255201](assets/1772882552016.png)

  ![77288265119](assets/1772882651194.png)

  加入因果掩码：

  ![77288280550](assets/1772882805501.png)

- benefit：When it comes to training, the computations can be parallelized and take full advantage of GPUs or other
  accelerators. When it comes to inference, the cost per time and memory for one prediction is constant for our model.
  This means we can simply store the φ (Kj ) VjT matrix as an internal state and update it at every time step like a
  recurrentneural network.

---

## RETHINKING ATTENTION WITH PERFORMERS

---

## EfficientViT: Multi-Scale Linear Attention for High-Resolution Dense Prediction

- 解决高精度输入下密集预测的低效问题

- global receptive field and multi-scale learning（two desirable features for high-resolution dense prediction）

- efficient global attention：ReLU attention

- efficient multi-scale learning：fusing DWConvs into a single DWConv and combining all 1\*1 Convs into a single 1\*1 group Conv
  ![77297815986](assets/1772978159860.png)

  ![77297817819](assets/1772978178190.png)

---

## [FLatten Transformer: Vision Transformer using Focused Linear Attention](https://gemini.google.com/share/29a05d10af14)

- 解决的问题
  - linear attention（ReLU attention） focus ability不足，即attention map过于平滑
    ![77302799163](assets/1773027991634.png)
  - 使用linear attention（ReLU attention）导致的feature diversity不足，即Attention Matrix的秩过低（i.e $rank(Attention Matrix)<=min(N,d)$）
    ![77302801407](assets/1773028014078.png)
- 针对focus ability：
  ![77302745133](assets/1773027451336.png)
  fp只改变向量的方向，将方向向最靠近的轴偏移
  ![77302752692](assets/1773027526928.png)
- 针对feature diversity：
  ![77302758206](assets/1773027582063.png)
  加入深度卷积，深度卷积对应的矩阵可以是满秩的，提高了$upperBound(rank(Attention Matrix))$

---

## Hydra Attention: Efficient Attention with Many Heads

- linear attention的精度受注意力头数的影响较小，而MLA(muti-head linear attention)的时间复杂度为$O(TD^2/H)$，
  则将H设置为D可以最大地减小计算复杂度为$O(TD)$
  ![77305562552](assets/1773055625524.png)

  ![77304661396](assets/1773046613962.png)

- kernel function的选择：
  This might be because cosine similarity changes the nature of attention. With MSA (Eq. 5), attention exclusively mixes information contained in V , as the mixing weights sim(Q, K) must sum to 1. That’s not the case when using cosine similarity or other unrestricted dot-product kernels like mean. And it turns out, these weights summing to 1 might not be a desirable property in the first place.

![77305625400](assets/1773056254004.png)

- 可视化attention：由于每个头的维度只有一维，将attention map直接求和平均会得到无意义的噪声图，因此作者将predicted class对应的logits作为loss function，对最后一层attention layer的cls token求梯度，计算每一个patch token在该梯度g上的分量
  ![77305678457](assets/1773056784573.png)
- hydra attention应该替换哪些层：从后往前替换和交替替换更优，因为hydra attention学到的是每一个token和global feature之间的关系
  ![77305725964](assets/1773057259646.png)

---

## [mamba](https://blog.csdn.net/v_JULY_v/article/details/134923301)

https://gemini.google.com/share/8d0ff3a382cc

---

## [Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model](https://gemini.google.com/share/6c49bc95716b)

- mamba模型适用于对因果关系建模，处理输入图片时，同一个图片内没有因果关系，因此vision mamba选择采用forward pass和backward pass两条路径

![77332254770](assets/1773322547704.png)

---

## [Demystify Mamba in Vision: A Linear Attention Perspective](https://gemini.google.com/share/c2fe208a97c2)

- 将mamba和linear attention表达式统一成相同的形式，通过比较其不同，确定是mamba的哪个机制带来了性能提升，并将其应用到linear attention中
  ![77348607337](assets/1773486073371.png)
- 输入门$\Delta_i$，slightly helpful，因为$\Delta_i$只考虑当前token，而没有考虑全局的语义信息
  ![77348776094](assets/1773487760946.png)
- 遗忘门$\tilde{A}_i$，obvious performance improvement，但是因为引入遗忘门导致无法高效并行计算，尽管使用了mamba的硬件感知的高效算法，但吞吐依然下降严重，分析$\tilde{A}_i$的值发现，遗忘门起到的作用是局部偏置和位置信息，因此可以使用位置编码替代
  ![77348778914](assets/1773487789141.png)
- 捷径连接  $D\odot x_i$，可有可无，带来了 0.2 的精度微升，但吞吐量也有所下降（1152 降至 1066）
- 注意力归一化，去掉线性注意力的归一化（分母 $Q_i Z_i$），模型精度会从 77.6 发生雪崩式下降，跌至 72.4，深层网络中个别长度较大的 token 会完全主导整个特征图
  ![77348779941](assets/1773487799417.png)
- 是否是多头注意力，改为单头，精度会骤降至 73.5 
- vit和mamba宏观架构不同，用 Mamba 的块设计（融合了深度卷积、门控等）替换原本的注意力子块，精度直接拉升至 80.9

![77348695383](assets/1773486953835.png)

- 综合以上分析，作者设计出MILA(Mamba Inspired Linear Attention)

  ![77348784001](assets/1773487840018.png)

---

## [SoLA-Vision: Fine-grained Layer-wise Linear Softmax Hybrid Attention](https://gemini.google.com/share/cb8c53b2d458)

- linear attention虽然是O(N)复杂度，但是表达能力明显弱于softmax attention

- 分析：
  早期的linear attention：
  ![77347399291](assets/1773473992915.png)
  一些现代linear attention：
  ![77347406551](assets/1773474065511.png)
  展开之后，可以发现隐含的距离依赖
  ![77347431384](assets/1773474313841.png)
  以指数衰减的核为例：
  ![77347462678](assets/1773474626786.png)
  应用中心极限定理：

  ![77347464516](assets/1773474645161.png)
  推出感受野随层数的1/2次方提升

  ![77347471750](assets/1773474717506.png)
  分辨率较高时linear attention对四角的猫头鹰attention受到距离衰减的影响
  ![77347563412](assets/1773475634121.png)

- method：
  ![77347494794](assets/1773474947949.png)

  - 早期分辨率太高，global attention(softmax attention)太昂贵，因此选择在stage3、4插入global attention
    ![77347613998](assets/1773476139987.png)
  - HSB为了弥补在早期缺失的全局交互，将早期low level的特征注入到stage3中

- 实验结果：
  classification：
  ![77347627859](assets/1773476278596.png)
  object detection：
  ![77347648448](assets/1773476484484.png)
  semantic segmentation：
  ![77347656682](assets/1773476566824.png)
  visualization(EigenCAM)：

  ![77347733824](assets/1773477338247.png)