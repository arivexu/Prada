# Prada

## Additional Contextual Baselines with Pathology Foundation Models

To address the concern regarding whether adapting a general-domain CLIP backbone is practically competitive with existing pathology-pretrained foundation models, we provide additional contextual baseline experiments in this repository. These experiments are designed to complement the main manuscript by evaluating frozen pathology foundation model features with lightweight classifiers under the same few-shot settings.

### Purpose

The main manuscript focuses on comparing different parameter-efficient fine-tuning strategies under the same general-domain CLIP backbone. This design isolates the contribution of the proposed Prada Tuning framework. However, recent computational pathology studies increasingly adopt pathology-pretrained patch-level foundation models as frozen feature extractors. Therefore, we additionally evaluate representative pathology foundation models, including UNI, Virchow2, and CONCH, as contextual baselines.

### Compared Models

| Model | Pretraining Domain | Evaluation Mode | Trainable Component | Notes |
|---|---|---|---|---|
| CLIP | General-domain image-text pairs | Zero-shot / linear probe | None / linear classifier | General-domain VLM baseline |
| Prada Tuning | General-domain CLIP adapted to pathology | Few-shot PEFT | DCP + ViSo Adapter | Proposed method in the manuscript |
| UNI | Pathology-pretrained foundation model | Frozen feature + linear probe | Linear classifier / logistic regression | Pathology patch-level foundation model |
| Virchow2 | Pathology-pretrained foundation model | Frozen feature + linear probe | Linear classifier / logistic regression | Pathology patch-level foundation model |
| CONCH | Pathology-oriented vision-language model | Zero-shot / frozen feature + linear probe | None / linear classifier | Pathology VLM contextual baseline |

### Evaluation Protocol

We follow the same few-shot protocol as the main manuscript. For each dataset, we evaluate all models under the 1-shot, 2-shot, 4-shot, 8-shot, and 16-shot settings. The same train/test splits are used for all compared methods whenever applicable.

- Datasets: `XXX`
- Few-shot settings: `1 / 2 / 4 / 8 / 16 shots`
- Number of random seeds: `XXX`
- Image preprocessing: `XXX`
- Feature extraction resolution: `XXX`
- Classifier: `linear probe / logistic regression`
- Optimizer: `XXX`
- Learning rate: `XXX`
- Training epochs: `XXX`
- Evaluation metric: `accuracy / AUC / F1-score / XXX`

### Feature Extraction and Classifier Training

For pathology-pretrained foundation models, the backbone is kept frozen during evaluation. We first extract image-level or patch-level features from each input sample. A lightweight classifier is then trained on top of the frozen features using the few-shot training set. No backbone parameters are updated.

For WSI-level evaluation, patch features are aggregated using `XXX` pooling. For patch-level or image-level datasets, each image is directly encoded into a single feature representation.

### Main Results

Table 1 reports the contextual baseline results under the same few-shot splits. These results are intended to provide an additional practical reference for comparing general-domain CLIP adaptation with pathology-pretrained foundation model features.

| Method | Backbone | Adaptation / Classifier | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | Avg. |
|---|---|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | CLIP | None | XXX | XXX | XXX | XXX | XXX | XXX |
| Linear probe | CLIP | Linear classifier | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | CLIP | DCP + ViSo Adapter | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI | UNI | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 | Virchow2 | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | CONCH | Text prompt inference | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | CONCH | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |

### Dataset-wise Results


#### Dataset: XXX


| Method | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | Avg. |
|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | XXX | XXX | XXX | XXX | XXX | XXX |

### Interpretation


These contextual baselines help clarify the practical relationship between adapting a general-domain CLIP model and directly using pathology-pretrained foundation model features. The results show that `XXX`. In particular, Prada Tuning achieves `XXX`, while UNI, Virchow2, and CONCH obtain `XXX`, `XXX`, and `XXX`, respectively. These observations suggest that `XXX`.

中文翻译：  
这些上下文基线有助于说明“适配通用领域 CLIP 模型”和“直接使用病理预训练基础模型特征”之间的实际关系。实验结果显示，`XXX`。具体而言，Prada Tuning 达到 `XXX`，而 UNI、Virchow2 和 CONCH 分别达到 `XXX`、`XXX` 和 `XXX`。这些结果说明 `XXX`。

It should be noted that these pathology-specific foundation models differ from CLIP in terms of pretraining data, input resolution, architecture design, and feature granularity. Therefore, the purpose of these experiments is not to directly integrate Prada Tuning into each pathology-specific backbone, but to provide a contextual comparison under a consistent few-shot evaluation protocol.

中文翻译：  
需要注意的是，这些病理专用基础模型在预训练数据、输入分辨率、网络结构和特征粒度等方面均与 CLIP 存在差异。因此，这些实验的目的并不是将 Prada Tuning 直接集成到每一个病理专用骨干网络中，而是在统一的少样本评估协议下提供一个上下文对比。

