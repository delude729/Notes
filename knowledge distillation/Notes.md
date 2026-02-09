# Notes

---

## Seeing Further on the Shoulders of Giants: Knowledge Inheritance for Vision Foundation Models

![77004151846](assets/1770041518467.png)


- mutiple teacher distillation


- project feature of teacher into a unified space to be aligned with feature of students.
  避免导致student倾向于学习feature magnitude更大的teacher
- freeze sentinel pre-trained encoder(dino v2) and train the adapter for specific knowledge.

![77004715219](assets/1770047152195.png)

---

## DUNE: Distilling a Universal Encoder from Heterogeneous 2D and 3D Teachers

![77004225081](assets/1770042250819.png)

- heterogeneous teachers

- transformer projector instead of MLP

  为了提取和其他patch之间的关系

- full data sharing works the best (except for semantic segmentation)

- discard transformer projectors during inference

---

## [DTO-KD: DYNAMIC TRADE-OFF OPTIMIZATION FOR EFFECTIVE KNOWLEDGE DISTILLATION (ICLR'2026)](https://gemini.google.com/share/95bbe6deed6a)

challenges:

1. the trade-off between optimizing for the primary task loss and mimicking the teacher’s outputs.
2. the gradient disparity arising from architectural and representational mismatches between teacher and student models.

![77008569153](assets/1770085691534.png)

![77008595930](assets/1770085959305.png)

- multi-teacher, two in this paper


- method : MGDA
  和AMMKD方法差不多(?

- corollary 1 : g*的方向和g1、g2的方向对齐（有正分量）![77010219869](assets/1770102198690.png)

  corollary 2 : 沿g1、g2的正分量大小相等
  ![77010223585](assets/1770102235852.png)

- corollary 3 : g*有下界，保证了训练的高效
  ![77010225609](assets/1770102256098.png)

- corollary 4 : g*有上界，保证了训练的稳定性
  ![77010230004](assets/1770102300048.png)

- g1,g2需要两次反向传播，效率低。论文提出近似算法以提高效率![77010629481](assets/1770106294817.png)

![77011166989](assets/1770111669891.png)

在分类和目标检测上均达**SOTA**![77011180867](assets/1770111808674.png)
![77011194586](assets/1770111945864.png)
![77011195722](assets/1770111957225.png)

---

## [EXPLORING KNOWLEDGE PURIFICATION IN MULTI-TEACHER KNOWLEDGE DISTILLATION FOR LLMS (ICLR' 2026)](https://gemini.google.com/share/a6c28ceaa2dd)

- LLM、multi-teacher

- challenge: knowledge conflict from multi teachers

- method:

  - knowledge aggregation（效率最低）
    聚合器(通常是强大的LLM，如GPT4)，将所有teacher的理由聚合成一个连贯的理由rp

    - LLM routing(效率最高)
      - Plackett-Luce Ranking
      - PLM Classifier
      - Similarity-based Router（**效果最好**）
    - RL-based Teacher Selection（**效果最好**，但迁移性差）

![77011087857](assets/1770110878572.png)

![77011594588](assets/1770115945888.png)

![77011615744](assets/1770116157444.png)

---

## Beyond Next-Token Alignment: Distilling Multimodal Large Language Models via Token Interactions

- VLM

- motivation: Attention patterns are different for different instructions.Most visual tokens are redundent for aligning.

- ipa loss: take the attention of instruction tokens to visual tokens as weights for aligning.

- tpa loss:
  之前的蒸馏方法存在的limitation： 

  ![77056446748](assets/1770564467488.png)
  method：
  ![77056450128](assets/1770564501287.png)
  但是直接align这个V*V的矩阵计算开销太大，所以使用Monte Carlo进行近似估计：
  ![77056468124](assets/1770564681245.png)

weakness:(未中稿ICLR'26)

- The two proposed modules, IVA and TPA, feel separate and not well-integrated. IVA is a VLM-specific technique for aligning visual-instruction interactions. TPA, however, is a general-purpose LLM distillation method for text generation. The paper does not convincingly unify them into a single coherent KD framework.
- The performance claims are not fully supported. Ablation: The performance gain from IVA is not significant. Table 3 shows adding IVA on top of TPA only provides a minimal 0.3-point gain (66.4 to 66.7), suggesting TPA does all the heavy lifting. Efficiency: TPA adds significant training overhead (509 / 355, about 1.43x). A critical baseline is missing: training Vanilla KD for the same amount of time as TPA (e.g., on 1.43x more data). It is possible that Vanilla KD could match the performance of TPA if given the same computational budget.

---

