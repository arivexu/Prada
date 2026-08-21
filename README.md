# Pathology Foundation Model Baselines for Prada Tuning / Prada Tuning 的病理基础模型上下文基线

## Overview / 概述

The main study evaluates Prada Tuning and other parameter-efficient fine-tuning (PEFT) strategies under the same general-domain CLIP ViT-B/16 backbone. That controlled design isolates the contribution of Prada Tuning, but it does not answer a separate practical question: how does adapting a general-domain CLIP model compare with directly using pathology-pretrained foundation models? To provide this missing context, we evaluate frozen UNI and Virchow2 encoders with a lightweight linear classifier under the same 1-, 2-, 4-, 8-, and 16-shot settings.

> **中文：** 正文在同一个通用领域 CLIP ViT-B/16 骨干网络下评估 Prada Tuning 和其他参数高效微调（PEFT）策略。该受控设计能够分离 Prada Tuning 本身的贡献，但尚未回答另一个实际问题：适配通用领域 CLIP 模型与直接使用病理预训练基础模型相比表现如何？为补充这一背景，我们在相同的 1、2、4、8 和 16-shot 设置下，评估冻结的 UNI 和 Virchow2 编码器与轻量线性分类器的组合。

These experiments are provided as contextual baselines rather than architecture-controlled ablations. Prada Tuning is not integrated into UNI or Virchow2 because these models differ from CLIP in pretraining data, architecture, token construction, and feature representation. The comparison therefore addresses practical performance under a matched downstream protocol, not the isolated effect of backbone pretraining.

> **中文：** 这些实验用于提供上下文基线，而不是进行架构受控的消融研究。由于 UNI 和 Virchow2 在预训练数据、网络结构、token 构造和特征表示方面均与 CLIP 不同，我们没有将 Prada Tuning 集成到这两个模型中。因此，该比较回答的是在匹配下游评估协议下的实际性能问题，而不是单独衡量骨干网络预训练的影响。

## Compared Methods / 对比方法

All pathology foundation-model encoders remain frozen throughout linear-probe training. The implementation trains only a randomly initialized linear classification head using cross-entropy loss. In this README, **LP** denotes this SGD-trained linear head.

> **中文：** 在线性探测训练期间，所有病理基础模型编码器均保持冻结。实现仅使用交叉熵损失训练一个随机初始化的线性分类头。在本 README 中，**LP** 指通过 SGD 训练的线性分类头。

| Method / 方法 | Pretraining and backbone / 预训练与骨干网络 | Input / 输入 | Downstream adaptation / 下游适配 | Feature used / 使用的特征 |
|---|---|---:|---|---|
| Prada Tuning | General-domain CLIP ViT-B/16 / 通用领域 CLIP ViT-B/16 | 224 x 224 | DCP + ViSo Adapter / DCP + ViSo Adapter | Adapted image-text representation / 适配后的图文表征 |
| UNI + LP | Pathology-pretrained UNI ViT-L/16 / 病理预训练 UNI ViT-L/16 | 224 x 224 | Frozen encoder + linear head / 冻结编码器 + 线性头 | 1,024-dimensional global representation / 1,024 维全局表征 |
| Virchow2 + LP | Pathology-pretrained Virchow2 ViT-H/14 / 病理预训练 Virchow2 ViT-H/14 | 224 x 224 | Frozen encoder + linear head / 冻结编码器 + 线性头 | CLS token concatenated with mean patch token, 2,560 dimensions / CLS token 与 patch token 均值拼接，2,560 维 |

## Evaluation Protocol / 评估协议

The comparison uses the same four image-level classification datasets and the same shot counts as the main study. An N-shot training set contains N images per class sampled from the base training partition. For each seed, all methods must load the same cached few-shot manifest (`shot_N-seed_S.pkl`). Validation uses at most four images per class, and final accuracy is measured on the complete base test partition.

> **中文：** 该比较使用与正文相同的四个图像级分类数据集和相同的 shot 数。N-shot 训练集从基础训练划分中为每个类别采样 N 张图像。对于每个随机种子，所有方法必须加载相同的少样本缓存清单（`shot_N-seed_S.pkl`）。验证集每类最多使用 4 张图像，最终准确率在完整的基础测试集上计算。

| Item / 项目 | Setting / 设置 |
|---|---|
| Datasets / 数据集 | BACH, BreakHis, MHIST, and PatchCamelyon (PCam) |
| Prediction unit / 预测单位 | Individual image / 单张图像 |
| Few-shot settings / 少样本设置 | 1, 2, 4, 8, and 16 labeled training images per class / 每类 1、2、4、8 和 16 张有标签训练图像 |
| Validation sampling / 验证集采样 | Up to `min(N, 4)` images per class / 每类最多 `min(N, 4)` 张图像 |
| Test set / 测试集 | Complete base test partition for every run / 每次运行均使用完整基础测试划分 |
| Primary metric / 主要指标 | Top-1 classification accuracy (%) / Top-1 分类准确率（%） |
| Summary statistic / 汇总统计量 | Mean +/- standard deviation across matched seeds / 匹配随机种子上的均值 +/- 标准差 |

For a fair comparison, all methods are evaluated using the same few-shot splits and the same set of random seeds. The reported mean and standard deviation are calculated over these matched runs, and only results obtained under this consistent evaluation protocol are included in the cross-method comparison.

> **中文：** 为保证比较公平，所有方法均使用相同的少样本数据划分和相同的一组随机种子进行评估。所报告的均值和标准差均基于这些匹配的实验运行计算，只有在这一统一评估协议下获得的结果才会纳入方法间比较。

The base partitions follow the image-level protocol described in the main study: PCam uses its predefined partitions; BACH uses a class-stratified 80/10/10 image-level split; MHIST retains the official test partition and divides the official training partition into training and validation subsets; and BreakHis uses a class-stratified 80/10/10 image-level split. These experiments evaluate image-level classification, not patient-level diagnosis. In particular, the evaluated BreakHis split is image-disjoint but not patient-disjoint.

> **中文：** 基础数据划分遵循正文中的图像级协议：PCam 使用预定义划分；BACH 使用按类别分层的 80/10/10 图像级划分；MHIST 保留官方测试集，并将官方训练集进一步划分为训练集和验证集；BreakHis 使用按类别分层的 80/10/10 图像级划分。这些实验评估的是图像级分类，而不是患者级诊断。尤其需要注意，所评估的 BreakHis 划分在图像层面互不重叠，但并非患者级互斥。

## Linear-Probe Configuration / 线性探测配置

To make the downstream comparison consistent across pathology foundation models, UNI and Virchow2 use the same classifier-training hyperparameters. Model-specific feature construction is retained because it is part of each released encoder pipeline. Frozen features are L2-normalized before entering the FP32 linear head.

> **中文：** 为保证病理基础模型之间的下游比较一致，UNI 和 Virchow2 使用相同的分类器训练超参数。由于模型特定的特征构建方式属于各自发布的编码器流程，因此予以保留。冻结特征在输入 FP32 线性分类头之前进行 L2 归一化。

| Item / 项目 | Setting / 设置 |
|---|---|
| Trainable component / 可训练部分 | Linear head weights and bias only / 仅线性头权重与偏置 |
| Loss / 损失函数 | Cross-entropy / 交叉熵 |
| Optimizer / 优化器 | SGD |
| Initial learning rate / 初始学习率 | 0.0035 |
| Training epochs / 训练轮数 | Maximum epochs are set to 200 for 16/8 shots, 100 for 4/2 shots, and 50 for 1 shot, following CoOp. |
| Training batch size / 训练批量大小 | 4 |
| Learning-rate schedule / 学习率调度 | Cosine decay / 余弦衰减 |
| Warm-up / 预热 | 1 epoch, constant warm-up learning rate of 1e-5 / 1 个 epoch，固定预热学习率 1e-5 |
| Training transforms / 训练变换 | Random resized crop, random horizontal flip, normalization / 随机尺度裁剪、随机水平翻转、归一化 |
| Encoder precision / 编码器精度 | Automatic mixed precision / 自动混合精度 |
| Classifier precision / 分类头精度 | FP32 |
| Model selection / 模型选择 | Final training epoch (`last_step`); no early stopping / 最后一个训练 epoch（`last_step`）；不使用早停 |

## Results / 实验结果

Each few-shot cell should be reported as **mean +/- standard deviation (%)** . 

> **中文：** 每个少样本单元格均应报告为**均值 +/- 标准差（%）**。

### PatchCamelyon (PCam)

| Method / 方法 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot |
|---|---:|---:|---:|---:|---:|
| UNI + LP | 54.9% ± 4.1% | 63.3% ± 2.9% | 66.1% ± 6.9% | 71.3% ± 4.0% | 72.9% ± 3.5% |
| Virchow2 + LP | 54.2% ± 5.6% | 62.0% ± 9.1% | 67.3% ± 4.2% | 72.8% ± 3.1% | 73.7% ± 5.6% |
| Prada Tuning | 56.8% ± 2.8% | 65.2% ± 3.5% | 69.2% ± 4.6% | 73.2% ± 1.4% | 74.3% ± 0.6% |

### BACH

| Method / 方法 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot |
|---|---:|---:|---:|---:|---:|
| UNI + LP | 46.7% ± 5.1% | 45.0% ± 4.1% | 64.2% ± 1.2% | 74.2% ± 1.2% | 77.5% ± 2.0% |
| Virchow2 + LP | 48.3% ± 3.1% | 70.8% ± 10.3% | 85.8% ± 6.6% | 95.8% ± 3.1% | 96.7% ± 3.1% |
| Prada Tuning | 36.7% ± 1.2% | 46.7% ± 4.7% | 58.3% ± 3.1% | 68.3% ± 1.2% | 73.3% ± 1.2% |

### MHIST

| Method / 方法 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot |
|---|---:|---:|---:|---:|---:|
| UNI + LP | 53.4% ± 6.6% | 57.7% ± 7.8% | 60.3% ± 3.7% | 65.3% ± 2.8% | 67.8% ± 1.7% |
| Virchow2 + LP | 45.3% ± 1.5% | 50.5% ± 3.3% | 51.1% ± 3.8% | 54.9% ± 3.2% | 64.9% ± 6.2% |
| Prada Tuning | 60.2% ± 0.2% | 58.2% ± 2.3% | 62.4% ± 0.3% | 66.1% ± 2.0% | 68.4% ± 1.5% |

### BreakHis

| Method / 方法 | 1-shot | 2-shot | 4-shot | 8-shot | 16-shot |
|---|---:|---:|---:|---:|---:|
| UNI + LP | 25.3% ± 1.6% | 28.5% ± 7.0% | 38.1% ± 3.4% | 41.0% ± 2.0% | 50.6% ± 3.9% |
| Virchow2 + LP | 20.3% ± 3.6% | 24.8% ± 4.5% | 35.0% ± 3.3% | 38.6% ± 2.7% | 49.0% ± 2.0% |
| Prada Tuning | 25.1% ± 1.3% | 28.6% ± 4.0% | 36.9% ± 2.4% | 39.7% ± 1.6% | 53.5% ± 2.6% |

## Result Summary / 结果总结

The comparison revealed clear dataset-dependent performance patterns. Prada Tuning achieved the highest observed mean accuracy among the three evaluated methods across all five shot settings on both PCam and MHIST. On BreakHis, Prada Tuning achieved the highest values in the 2-shot and 16-shot settings, while UNI + LP performed best in the 1-shot, 4-shot, and 8-shot settings. Virchow2 + LP showed a distinct advantage on BACH and achieved the highest mean accuracy across all five shot settings.

> **中文：** 对比结果呈现出明显的数据集依赖性。与另外两种方法相比，Prada Tuning 在 PCam 和 MHIST 的全部五种 shot 设置下均取得了最高的平均准确率。在 BreakHis 上，Prada Tuning 在 2-shot 和 16-shot 设置下表现最佳，而 UNI + LP 在 1-shot、4-shot 和 8-shot 设置下取得了最高结果。Virchow2 + LP 则在 BACH 上表现出明显优势，并在全部五种 shot 设置下取得了最高的平均准确率。

In the lowest-data regime, comprising the 1-shot and 2-shot settings, no single approach consistently dominated across all four datasets. Prada Tuning achieved the highest observed mean accuracy on PCam and MHIST in both settings. Virchow2 + LP performed best on BACH, whereas the strongest BreakHis results were divided between UNI + LP and Prada Tuning.

> **中文：** 在数据量最低的 1-shot 和 2-shot 设置下，没有任何一种方法能够在四个数据集上始终保持领先。Prada Tuning 在 PCam 和 MHIST 的两种设置下均取得了最高的平均准确率。Virchow2 + LP 在 BACH 上表现最佳，而 BreakHis 上的最佳结果则分别由 UNI + LP 和 Prada Tuning 取得。

With more labeled examples, Virchow2 + LP showed particularly strong performance on BACH, reaching 95.8% ± 3.1% and 96.7% ± 3.1% in the 8-shot and 16-shot settings, respectively. Under the same settings, Prada Tuning maintained the highest observed mean accuracy on PCam and MHIST. It also achieved the best 16-shot result on BreakHis, with an accuracy of 53.5% ± 2.6%.

> **中文：** 随着有标签样本数量增加，Virchow2 + LP 在 BACH 上表现出尤为突出的性能，在 8-shot 和 16-shot 设置下分别达到 95.8% ± 3.1% 和 96.7% ± 3.1%。在相同的 shot 设置下，Prada Tuning 在 PCam 和 MHIST 上仍保持最高的平均准确率。此外，Prada Tuning 在 BreakHis 的 16-shot 设置下也取得了最佳结果，准确率为 53.5% ± 2.6%。

Overall, the pathology-pretrained encoders did not provide a uniform advantage across datasets and shot settings. Virchow2 offered substantial benefits on BACH, whereas Prada Tuning remained consistently competitive on PCam and MHIST and showed mixed but competitive performance on BreakHis. These findings indicate that adapting a general-domain CLIP model can remain practically competitive with frozen pathology-pretrained representations, although the relative advantage depends strongly on the target dataset.

> **中文：** 总体而言，病理预训练编码器并未在所有数据集和 shot 设置下表现出一致优势。Virchow2 在 BACH 上带来了明显收益，而 Prada Tuning 在 PCam 和 MHIST 上始终保持较强竞争力，并在 BreakHis 上呈现出各有胜负但总体可比的表现。这些结果表明，与冻结的病理预训练表征相比，适配通用领域 CLIP 模型仍然可以具有实际竞争力，但不同方法的相对优势在很大程度上取决于目标数据集。

Because the compared systems differ substantially in their pretraining corpora, pretraining objectives, model capacity, architecture, and feature construction, these findings should be interpreted as a practical contextual comparison. They do not establish the intrinsic superiority of any adaptation mechanism or backbone under fully controlled conditions.

> **中文：** 由于所比较的系统在预训练语料、预训练目标、模型容量、网络结构和特征构建方式方面存在显著差异，因此这些结果应被解释为实际应用背景下的上下文比较。它们不能证明任何一种适配机制或骨干网络在完全受控条件下具有内在优越性。

## Interpretation Boundaries / 解释边界

The matched few-shot manifests and complete test partitions improve comparability across the three methods. Freezing the encoders and applying the same linear-head objective also standardize the evaluation of UNI and Virchow2. However, Prada Tuning and the pathology foundation-model baselines are not controlled for pretraining corpus, pretraining objective, parameter count, architecture, feature construction, or computational cost. The results therefore contextualize whether adapting general-domain CLIP is practically competitive with released pathology-pretrained representations. They do not replace the controlled comparisons among PEFT strategies conducted under the common CLIP backbone in the main study.

> **中文：** 匹配的少样本清单和完整的测试划分提高了三种方法之间的可比性。冻结编码器并采用相同的线性分类头训练目标，也使 UNI 和 Virchow2 的评估更加统一。然而，Prada Tuning 与病理基础模型基线在预训练语料、预训练目标、参数量、网络结构、特征构建方式和计算成本方面并未受到严格控制。因此，这些结果用于说明通用领域 CLIP 适配与已发布的病理预训练表征相比是否具有实际竞争力，但不能替代正文中在统一 CLIP 骨干网络下进行的受控 PEFT 策略比较。

The evaluation is additionally limited to four public image-level classification datasets. Patient- or case-level independence cannot be verified for BACH and MHIST because the required grouping metadata are unavailable. The BreakHis partitions are image-disjoint but not patient-disjoint. Consequently, the reported results should not be interpreted as evidence of patient-independent generalization, clinical utility, or superiority on whole-slide image tasks.

> **中文：** 此外，本次评估仅限于四个公开的图像级分类数据集。由于缺少必要的分组元数据，BACH 和 MHIST 的患者级或病例级独立性无法得到验证。BreakHis 的各数据划分在图像层面互不重叠，但并非患者级互斥。因此，所报告的结果不应被解释为患者独立泛化能力、临床实用性或全切片图像任务优越性的证据。
