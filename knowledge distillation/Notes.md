# Notes

---

Seeing Further on the Shoulders of Giants: Knowledge Inheritance for Vision Foundation Models

![77004151846](assets/1770041518467.png)


- mutiple teacher distillation


- project feature of teacher into a unified space to be aligned with feature of students.
  避免导致student倾向于学习feature magnitude更大的teacher
- freeze sentinel pre-trained encoder(dino v2) and train the adapter for specific knowledge.

![77004715219](assets/1770047152195.png)

---

DUNE: Distilling a Universal Encoder from Heterogeneous 2D and 3D Teachers

![77004225081](assets/1770042250819.png)

- heterogeneous teachers
- transformer projector instead of MLP
- full data sharing works the best (except for semantic segmentation)
- discard transformer projectors during inference

---





