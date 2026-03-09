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

## FLatten Transformer: Vision Transformer using Focused Linear Attention

- 解决的问题
  - linear attention（ReLU attention） focus ability不足，即attention map过于平滑
    ![77302799163](assets/1773027991634.png)
  - 使用linear attention（ReLU attention）导致的feature diversity不足，即O的秩过低（i.e $rank(O)<min(N,d)$）
    ![77302801407](assets/1773028014078.png)
- 针对focus ability：
  ![77302745133](assets/1773027451336.png)
  fp只改变向量的方向，将方向向最靠近的轴偏移
  ![77302752692](assets/1773027526928.png)
- 针对feature diversity：
  ![77302758206](assets/1773027582063.png)
  加入深度卷积，深度卷积对应的矩阵可以是满秩的，提高了$upperBound(rank(O))$

---

