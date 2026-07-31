# Prada

## Additional Contextual Baselines with Pathology Foundation Models

中文翻译：  
## 使用病理基础模型的补充上下文基线实验

To address the concern regarding whether adapting a general-domain CLIP backbone is practically competitive with existing pathology-pretrained foundation models, we provide additional contextual baseline experiments in this repository. These experiments are designed to complement the main manuscript by evaluating frozen pathology foundation model features with lightweight classifiers under the same few-shot settings.

中文翻译：  
为回应关于“基于通用领域 CLIP 的适配方法是否能够与现有病理预训练基础模型在实际性能上竞争”的问题，我们在本仓库中提供了补充的上下文基线实验。这些实验旨在作为正文实验的补充，在相同少样本设置下，评估冻结的病理基础模型特征结合轻量分类器的性能。

### Purpose

中文翻译：  
### 实验目的

The main manuscript focuses on comparing different parameter-efficient fine-tuning strategies under the same general-domain CLIP backbone. This design isolates the contribution of the proposed Prada Tuning framework. However, recent computational pathology studies increasingly adopt pathology-pretrained patch-level foundation models as frozen feature extractors. Therefore, we additionally evaluate representative pathology foundation models, including UNI, Virchow2, and CONCH, as contextual baselines.

中文翻译：  
正文中的主要实验集中在同一个通用领域 CLIP 骨干网络下比较不同参数高效微调策略，这种设计有助于单独分析所提出 Prada Tuning 框架的贡献。然而，近年来计算病理学研究越来越多地使用病理预训练的 patch-level 基础模型作为冻结特征提取器。因此，我们额外评估了若干代表性病理基础模型，包括 UNI、Virchow2 和 CONCH，作为上下文基线。

### Compared Models

中文翻译：  
### 对比模型

| Model | Pretraining Domain | Evaluation Mode | Trainable Component | Notes |
|---|---|---|---|---|
| CLIP | General-domain image-text pairs | Zero-shot / linear probe | None / linear classifier | General-domain VLM baseline |
| Prada Tuning | General-domain CLIP adapted to pathology | Few-shot PEFT | DCP + ViSo Adapter | Proposed method in the manuscript |
| UNI | Pathology-pretrained foundation model | Frozen feature + linear probe | Linear classifier / logistic regression | Pathology patch-level foundation model |
| Virchow2 | Pathology-pretrained foundation model | Frozen feature + linear probe | Linear classifier / logistic regression | Pathology patch-level foundation model |
| CONCH | Pathology-oriented vision-language model | Zero-shot / frozen feature + linear probe | None / linear classifier | Pathology VLM contextual baseline |

中文翻译：  

| 模型 | 预训练领域 | 评估方式 | 可训练部分 | 说明 |
|---|---|---|---|---|
| CLIP | 通用图像-文本数据 | 零样本 / 线性探测 | 无 / 线性分类器 | 通用领域 VLM 基线 |
| Prada Tuning | 基于通用 CLIP 并适配到病理领域 | 少样本 PEFT | DCP + ViSo Adapter | 本文提出的方法 |
| UNI | 病理预训练基础模型 | 冻结特征 + 线性探测 | 线性分类器 / 逻辑回归 | 病理 patch-level 基础模型 |
| Virchow2 | 病理预训练基础模型 | 冻结特征 + 线性探测 | 线性分类器 / 逻辑回归 | 病理 patch-level 基础模型 |
| CONCH | 病理视觉语言模型 | 零样本 / 冻结特征 + 线性探测 | 无 / 线性分类器 | 病理 VLM 上下文基线 |

### Evaluation Protocol

中文翻译：  
### 评估协议

We follow the same few-shot protocol as the main manuscript. For each dataset, we evaluate all models under the 1-shot, 2-shot, 4-shot, 8-shot, and 16-shot settings. The same train/test splits are used for all compared methods whenever applicable.

中文翻译：  
我们遵循正文中相同的少样本实验协议。对于每个数据集，我们在 1-shot、2-shot、4-shot、8-shot 和 16-shot 设置下评估所有模型。在适用情况下，所有对比方法均使用相同的训练/测试划分。

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

中文翻译：  

- 数据集：`XXX`
- 少样本设置：`1 / 2 / 4 / 8 / 16 shots`
- 随机种子数量：`XXX`
- 图像预处理方式：`XXX`
- 特征提取分辨率：`XXX`
- 分类器：`linear probe / logistic regression`
- 优化器：`XXX`
- 学习率：`XXX`
- 训练轮数：`XXX`
- 评价指标：`accuracy / AUC / F1-score / XXX`

### Feature Extraction and Classifier Training

中文翻译：  
### 特征提取与分类器训练

For pathology-pretrained foundation models, the backbone is kept frozen during evaluation. We first extract image-level or patch-level features from each input sample. A lightweight classifier is then trained on top of the frozen features using the few-shot training set. No backbone parameters are updated.

中文翻译：  
对于病理预训练基础模型，在评估过程中骨干网络保持冻结。我们首先从每个输入样本中提取 image-level 或 patch-level 特征，然后在冻结特征之上使用少样本训练集训练一个轻量分类器。整个过程中不更新骨干网络参数。

For WSI-level evaluation, patch features are aggregated using `XXX` pooling. For patch-level or image-level datasets, each image is directly encoded into a single feature representation.

中文翻译：  
对于 WSI-level 评估，我们使用 `XXX` 池化方式聚合 patch 特征。对于 patch-level 或 image-level 数据集，每张图像直接编码为一个特征表示。

### Main Results

中文翻译：  
### 主要结果

Table 1 reports the contextual baseline results under the same few-shot splits. These results are intended to provide an additional practical reference for comparing general-domain CLIP adaptation with pathology-pretrained foundation model features.

中文翻译：  
表 1 报告了在相同少样本划分下的上下文基线结果。这些结果旨在为比较“通用领域 CLIP 适配方法”和“病理预训练基础模型特征”提供额外的实践参考。

| Method | Backbone | Adaptation / Classifier | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | Avg. |
|---|---|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | CLIP | None | XXX | XXX | XXX | XXX | XXX | XXX |
| Linear probe | CLIP | Linear classifier | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | CLIP | DCP + ViSo Adapter | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI | UNI | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 | Virchow2 | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | CONCH | Text prompt inference | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | CONCH | Frozen feature + linear probe | XXX | XXX | XXX | XXX | XXX | XXX |

中文翻译：  

| 方法 | 骨干网络 | 适配方式 / 分类器 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | 平均值 |
|---|---|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | CLIP | 无 | XXX | XXX | XXX | XXX | XXX | XXX |
| Linear probe | CLIP | 线性分类器 | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | CLIP | DCP + ViSo Adapter | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI | UNI | 冻结特征 + 线性探测 | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 | Virchow2 | 冻结特征 + 线性探测 | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | CONCH | 文本提示推理 | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | CONCH | 冻结特征 + 线性探测 | XXX | XXX | XXX | XXX | XXX | XXX |

### Dataset-wise Results

中文翻译：  
### 各数据集结果

#### Dataset: XXX

中文翻译：  
#### 数据集：XXX

| Method | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | Avg. |
|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | XXX | XXX | XXX | XXX | XXX | XXX |

中文翻译：  

| 方法 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot | 平均值 |
|---|---:|---:|---:|---:|---:|---:|
| CLIP zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| Prada Tuning | XXX | XXX | XXX | XXX | XXX | XXX |
| UNI linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| Virchow2 linear probe | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH zero-shot | XXX | XXX | XXX | XXX | XXX | XXX |
| CONCH linear probe | XXX | XXX | XXX | XXX | XXX | XXX |

### Interpretation

中文翻译：  
### 结果分析

These contextual baselines help clarify the practical relationship between adapting a general-domain CLIP model and directly using pathology-pretrained foundation model features. The results show that `XXX`. In particular, Prada Tuning achieves `XXX`, while UNI, Virchow2, and CONCH obtain `XXX`, `XXX`, and `XXX`, respectively. These observations suggest that `XXX`.

中文翻译：  
这些上下文基线有助于说明“适配通用领域 CLIP 模型”和“直接使用病理预训练基础模型特征”之间的实际关系。实验结果显示，`XXX`。具体而言，Prada Tuning 达到 `XXX`，而 UNI、Virchow2 和 CONCH 分别达到 `XXX`、`XXX` 和 `XXX`。这些结果说明 `XXX`。

It should be noted that these pathology-specific foundation models differ from CLIP in terms of pretraining data, input resolution, architecture design, and feature granularity. Therefore, the purpose of these experiments is not to directly integrate Prada Tuning into each pathology-specific backbone, but to provide a contextual comparison under a consistent few-shot evaluation protocol.

中文翻译：  
需要注意的是，这些病理专用基础模型在预训练数据、输入分辨率、网络结构和特征粒度等方面均与 CLIP 存在差异。因此，这些实验的目的并不是将 Prada Tuning 直接集成到每一个病理专用骨干网络中，而是在统一的少样本评估协议下提供一个上下文对比。

### Reproducibility

中文翻译：  
### 可复现性

The scripts used for feature extraction and linear-probe evaluation will be released in this repository.

中文翻译：  
用于特征提取和线性探测评估的脚本将在本仓库中发布。

```bash
# Extract frozen features
python tools/extract_features.py \
  --dataset XXX \
  --backbone XXX \
  --checkpoint XXX \
  --output XXX

# Train lightweight classifier
python tools/train_linear_probe.py \
  --features XXX \
  --labels XXX \
  --shots XXX \
  --seed XXX \
  --classifier XXX
