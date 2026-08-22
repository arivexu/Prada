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

## Reproducibility of the CLIP-Based SOTA Comparisons / 基于 CLIP 的 SOTA 比较可复现性说明

This section documents the experimental protocol used for the CLIP-based baseline comparisons reported in Table I of the manuscript. The section focuses exclusively on the general-domain CLIP comparisons and does not include the separate contextual comparison with pathology-pretrained foundation models.

> **中文：** 本节记录论文表 I 中基于 CLIP 的基线比较所采用的实验协议。本节仅关注通用领域 CLIP 模型下的比较，不包含病理预训练基础模型的独立上下文比较。

The purpose of these experiments is to compare Prada Tuning with representative parameter-efficient fine-tuning and adaptation strategies under a common CLIP-based setting. The common CLIP backbone allows the comparison to focus on the relative behavior of different adaptation strategies while keeping the general model family fixed.

> **中文：** 这些实验旨在统一的 CLIP 设置下，将 Prada Tuning 与具有代表性的参数高效微调和适配方法进行比较。使用相同的 CLIP 骨干网络，可以在保持通用模型家族一致的情况下，重点比较不同适配策略的相对表现。

### Compared Methods / 对比方法

The manuscript evaluates the following methods: Linear Probe, Full Fine-tuning, CoOp, CoCoOp, CLIP-Adapter, Tip-Adapter, MaPLe, UPT, MCPL, LoRA, PromptSRC, CoPrompt, and Prada Tuning.

> **中文：** 论文评估了以下方法：Linear Probe、Full Fine-tuning、CoOp、CoCoOp、CLIP-Adapter、Tip-Adapter、MaPLe、UPT、MCPL、LoRA、PromptSRC、CoPrompt 和 Prada Tuning。

The method descriptions below follow the definitions given in the manuscript.

> **中文：** 下述方法描述均遵循论文中的定义。

| Method / 方法 | Description in the manuscript / 论文中的方法描述 | Backbone setting / 骨干网络设置 |
|---|---|---|
| Linear Probe | Trains a classification head while fixing the backbone parameters / 固定骨干网络参数，仅训练分类头 | CLIP ViT-B/16 vision encoder / CLIP ViT-B/16 视觉编码器 |
| Full Fine-tuning | Updates the classification head together with the backbone / 同时更新分类头和骨干网络 | CLIP ViT-B/16 vision encoder / CLIP ViT-B/16 视觉编码器 |
| CoOp | Trains shallow text prompts while freezing the backbone / 冻结骨干网络，训练浅层文本 prompt | CLIP ViT-B/16 / CLIP ViT-B/16 |
| CoCoOp | Learns conditioning prompts explicitly on image instances while keeping the backbone frozen / 在冻结骨干网络的条件下，针对图像实例学习条件 prompt | CLIP ViT-B/16 / CLIP ViT-B/16 |
| CLIP-Adapter | Trains a parameterized feature adapter at the end of the visual encoder while keeping the backbone frozen / 冻结骨干网络，在视觉编码器末端训练参数化特征适配器 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| Tip-Adapter | Constructs an adapter using a key-value cache from the few-shot training set without gradient-based training / 利用少样本训练集构建键值缓存，不进行基于梯度的训练 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| MaPLe | Learns vision-language prompts; the first-layer prompt is initialized with ``a photo of a <category>'', while prompts in other layers are randomly initialized / 学习视觉语言 prompt；第一层 prompt 使用 ``a photo of a <category>'' 初始化，其他层 prompt 随机初始化 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| UPT | Generates prompts using two MLP layers and transforms them with self-attention layers / 使用两个 MLP 层生成 prompt，并通过自注意力层进行变换 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| MCPL | Follows the official implementation / 遵循官方实现 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| LoRA | Follows the official implementation of low-rank adaptation / 遵循低秩适配方法的官方实现 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| PromptSRC | Follows the official implementation / 遵循官方实现 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| CoPrompt | Follows the official implementation / 遵循官方实现 | CLIP ViT-B/16 / CLIP ViT-B/16 |
| Prada Tuning | Jointly combines DCP prompt tuning and ViSo Adapter tuning / 联合使用 DCP prompt tuning 和 ViSo Adapter tuning | CLIP ViT-B/16 / CLIP ViT-B/16 |

The manuscript specifies that the prompt depth of MaPLe is 9 and its prompt length is 2. The first-layer prompt is initialized using the category-description template, while the prompts in subsequent layers are randomly initialized.

> **中文：** 论文明确说明 MaPLe 的 prompt depth 为 9，prompt length 为 2。第一层 prompt 使用类别描述模板进行初始化，后续层的 prompt 采用随机初始化。

For Prada Tuning, the manuscript uses L2V-DCP with prompt depth 11 and prompt length 2. The ViSo Adapter is inserted after the fourth, fifth, and sixth layers of the visual encoder, and its covariance matrix has a dimension of \(128 \times 128\).

> **中文：** 对于 Prada Tuning，论文采用 prompt depth 为 11、prompt length 为 2 的 L2V-DCP。ViSo Adapter 插入视觉编码器的第 4、5、6 层之后，其协方差矩阵维度为 \(128 \times 128\)。

### Common Backbone and Evaluation Protocol / 统一骨干网络与评估协议

The manuscript adopts CLIP ViT-B/16 as the common backbone for CoOp, CoCoOp, CLIP-Adapter, Tip-Adapter, MaPLe, UPT, MCPL, LoRA, PromptSRC, and CoPrompt. For Linear Probe and Full Fine-tuning, the vision encoder of CLIP ViT-B/16 is used as the image representation backbone.

> **中文：** 对于 CoOp、CoCoOp、CLIP-Adapter、Tip-Adapter、MaPLe、UPT、MCPL、LoRA、PromptSRC 和 CoPrompt，论文统一采用 CLIP ViT-B/16 作为骨干网络。对于 Linear Probe 和 Full Fine-tuning，使用 CLIP ViT-B/16 的视觉编码器作为图像表征骨干。

The experiments are conducted on four pathology image datasets: PCam, BACH, MHIST, and BreakHis. The evaluated prediction unit is an individual image. The experiments use the 1-, 2-, 4-, 8-, and 16-shot settings rather than a base-to-novel class split, because the clinical label space in these pathology classification tasks is fixed.

> **中文：** 实验在四个病理图像数据集上进行：PCam、BACH、MHIST 和 BreakHis。预测单位为单张图像。由于这些病理分类任务具有固定的临床标签空间，实验采用 1、2、4、8 和 16-shot 设置，而不是 base-to-novel 类别划分。

For each few-shot setting, the reported result is calculated on the corresponding test partition. The manuscript reports the mean and standard deviation over three independent runs for the few-shot experiments.

> **中文：** 对于每种少样本设置，结果均在对应的测试划分上计算。论文对少样本实验报告三次独立运行的均值和标准差。

The complete base train/validation/test partitions are established before few-shot sampling. The few-shot training samples are selected from the base training partition, while validation and final testing remain separate from the sampled training images.

> **中文：** 完整的基础训练集、验证集和测试集划分在少样本采样之前建立。少样本训练样本从基础训练集中选择，验证集和最终测试集与采样得到的训练图像保持独立。

### Prada Tuning Training Configuration Reported in the Manuscript / 论文中报告的 Prada Tuning 训练配置

For few-shot learning, the manuscript uses SGD with a batch size of 4 and an initial learning rate of 0.0035. The maximum number of epochs is set to 200 for 8- and 16-shot experiments, 100 for 2- and 4-shot experiments, and 50 for 1-shot experiments, following the schedule described in the manuscript.

> **中文：** 对于少样本学习，论文采用 SGD 优化器，批量大小为 4，初始学习率为 0.0035。根据论文中的训练计划，8-shot 和 16-shot 实验最多训练 200 个 epoch，2-shot 和 4-shot 实验最多训练 100 个 epoch，1-shot 实验最多训练 50 个 epoch。

The Prada Tuning configuration uses the generic prompt template ``a photo of a [CLASS]'' and does not rely on manually designed pathology-specific prompts.

> **中文：** Prada Tuning 使用通用 prompt 模板 ``a photo of a [CLASS]''，不依赖人工设计的病理领域特定 prompt。

### Baseline Implementation Policy / 基线实现原则

For baselines whose descriptions in the manuscript state ``follows the official implementation'', the implementation should be identified by its official source, version, and configuration used for the reported experiment. This applies in particular to MCPL, LoRA, PromptSRC, and CoPrompt.

> **中文：** 对于论文中描述为 ``follows the official implementation'' 的基线，应明确记录其官方来源、版本以及生成论文结果时使用的具体配置。这尤其适用于 MCPL、LoRA、PromptSRC 和 CoPrompt。

The manuscript specifies the functional role of each baseline and the common CLIP backbone, but it does not provide a separate numerical hyperparameter search range for every baseline in the ``Comparison with SOTA'' subsection. Therefore, numerical search ranges, validation rules, and stopping criteria should only be reported in this README when they are supported by the original experiment records.

> **中文：** 论文明确说明了各个基线的功能角色以及统一的 CLIP 骨干网络，但在 ``Comparison with SOTA'' 小节中没有为每一个基线单独给出数值化的超参数搜索范围。因此，只有在原始实验记录能够支持的情况下，README 才应报告具体的搜索范围、验证规则和停止条件。

The public record should not infer baseline learning rates, batch sizes, epoch budgets, early-stopping rules, or checkpoint-selection criteria from an implementation file if those values are not explicitly stated as the settings used to generate the published Table-I results.

> **中文：** 如果某些学习率、批量大小、训练轮数、早停规则或检查点选择标准没有被明确说明为生成论文表 I 结果时使用的配置，则公开记录不应仅根据代码文件推断这些实验事实。

### Validation and Model Selection / 验证与模型选择

The manuscript reports the final test accuracy for each method and shot setting and reports the mean and standard deviation over three independent runs. The manuscript does not separately specify a universal validation criterion, a common hyperparameter search space, or a common early-stopping rule for all baseline methods.

> **中文：** 论文报告了每种方法和每个 shot 设置下的最终测试准确率，并报告三次独立运行的均值和标准差。论文没有为所有基线统一规定一个验证标准、共同的超参数搜索空间或统一的早停规则。

Accordingly, the validation and model-selection protocol should be recorded on a method-by-method basis. The following information should be attached to each baseline result before the public comparison is considered fully reproducible:

> **中文：** 因此，验证和模型选择流程应当按照方法逐一记录。在将公开比较视为完全可复现之前，每个基线结果都应附带以下信息：

| Required item / 必要信息 | Required record / 应记录内容 |
|---|---|
| Validation criterion / 验证标准 | The metric used to select a hyperparameter or checkpoint, if validation was used / 如果使用验证集，应记录用于选择超参数或检查点的指标 |
| Search range / 搜索范围 | The exact values or intervals evaluated for each tuned parameter / 每个调参参数实际评估的具体数值或区间 |
| Search budget / 搜索预算 | Number of configurations or refinement steps evaluated / 评估的配置数量或细化搜索次数 |
| Early stopping / 早停 | Whether early stopping was used and what criterion determined stopping / 是否使用早停以及停止判据 |
| Checkpoint selection / 检查点选择 | The exact checkpoint evaluated for the test result / 测试结果所使用的确切检查点 |
| Number of runs / 运行次数 | Number of independent runs and the unit of independence / 独立运行次数及独立性的定义 |
| Seed provenance / 随机种子来源 | The seed set used by every method / 每种方法所使用的随机种子集合 |
| Few-shot manifest / 少样本清单 | The exact sampled training images used in each run / 每次运行使用的确切训练图像清单 |

The three-run mean and standard deviation are a valid matched comparison only if every method uses the same seed set and the same few-shot training manifests. If different methods were evaluated with different seed sets, those results should not be aggregated as a matched comparison.

> **中文：** 只有当所有方法使用相同的随机种子集合和相同的少样本训练清单时，三次运行的均值和标准差才可以构成有效的匹配比较。如果不同方法使用了不同的随机种子集合，则这些结果不应被汇总为匹配比较。

### Observed Weak-Baseline Patterns / 较弱基线的观测结果

The manuscript identifies CLIP-Adapter and Full Fine-tuning as methods whose performance is unexpectedly weak or variable on selected datasets. These observations are discussed descriptively and are not interpreted as definitive evidence of a single optimization failure mechanism.

> **中文：** 论文指出，CLIP-Adapter 和 Full Fine-tuning 在部分数据集上的表现较弱或波动较大。这些现象仅进行描述性讨论，不被解释为某一种确定的优化失败机制的证据。

The CLIP-Adapter results on BACH remain close to the four-class chance level across the evaluated shot settings. On BreakHis, the CLIP-Adapter results are weak in the low-shot settings and improve as the number of labeled examples increases.

> **中文：** CLIP-Adapter 在 BACH 上的结果在不同 shot 设置下均接近四分类随机水平。在 BreakHis 上，CLIP-Adapter 在低 shot 设置下表现较弱，并随着有标签样本数量增加而有所改善。

Full Fine-tuning shows non-monotonic behavior on BACH and relatively large run-to-run variation in some shot settings. On BreakHis, its accuracy improves as more labeled examples become available, but the performance remains sensitive to the specific few-shot run.

> **中文：** Full Fine-tuning 在 BACH 上呈现非单调趋势，并且在部分 shot 设置下具有较大的运行间波动。在 BreakHis 上，随着有标签样本数量增加，其准确率有所提升，但性能仍然对具体的少样本运行较为敏感。

These patterns may be related to the interaction between the general-domain CLIP pretraining distribution and the pathology image domain, the limited number of labeled examples, the adaptation capacity of each method, and optimization sensitivity. They are compatible with possible overfitting or unstable optimization, but the aggregate accuracy and standard deviation do not establish these mechanisms conclusively.

> **中文：** 这些现象可能与通用领域 CLIP 的预训练分布和病理图像领域之间的差异、有限的有标签样本数量、不同方法的适配能力以及优化敏感性共同相关。它们与潜在的过拟合或优化不稳定相一致，但汇总准确率和标准差无法确定性地证明这些机制。

An accuracy close to the class-prior chance level is not, by itself, evidence of mode collapse. A specific claim of mode collapse would require additional run-level evidence, such as predicted-class distributions or class-wise confusion patterns.

> **中文：** 接近类别先验随机水平的准确率本身不能证明发生了模式坍塌。如果要明确声称模式坍塌，需要额外的运行级证据，例如预测类别分布或按类别统计的混淆模式。

### Diagnostic Evidence / 诊断性证据

The manuscript reports aggregate test accuracy and standard deviation across independent runs. It does not report complete per-epoch validation curves, training-loss curves, or predicted-class distributions for every baseline run in the ``Comparison with SOTA'' subsection.

> **中文：** 论文报告了独立运行上的汇总测试准确率和标准差，但在 ``Comparison with SOTA'' 小节中没有报告每个基线运行对应的完整逐 epoch 验证曲线、训练损失曲线或预测类别分布。

Therefore, the weak-baseline interpretation is limited to the observed shot-wise performance trends and run-to-run variability. The results should not be used to claim that CLIP-Adapter or Full Fine-tuning definitely suffers from overfitting, mode collapse, or unstable convergence without additional traceable diagnostic evidence.

> **中文：** 因此，对较弱基线的解释仅限于观测到的 shot 变化趋势和运行间波动。在没有额外且可追溯的诊断证据的情况下，不应据此声称 CLIP-Adapter 或 Full Fine-tuning 必然存在过拟合、模式坍塌或收敛不稳定。

If validation curves, training-loss curves, or predicted-class distributions are available for the exact runs reported in Table I, they should be added to this repository with the corresponding dataset, shot setting, method, and seed metadata. Diagnostics that cannot be linked unambiguously to the published results should not be presented as evidence for those results.

> **中文：** 如果能够获得与表 I 中确切运行相对应的验证曲线、训练损失曲线或预测类别分布，则应将其连同数据集、shot 设置、方法和随机种子元数据一并添加到仓库中。无法与论文结果明确对应的诊断内容，不应被作为这些结果的支持证据。

### Scope of the Comparison / 比较范围

The reported comparison evaluates practical image-level few-shot behavior under a common general-domain CLIP family. It is not a fully controlled comparison of all baseline hyperparameters, model capacities, pretraining data, or optimization budgets.

> **中文：** 该比较评估的是统一通用领域 CLIP 模型家族下的实际图像级少样本表现。它不是对所有基线的超参数、模型容量、预训练数据或优化预算进行完全控制的比较。

The results therefore provide evidence about the relative practical behavior of Prada Tuning and the evaluated CLIP-based baselines under the reported protocol. They do not, by themselves, prove that Prada Tuning is intrinsically superior to every baseline under identical tuning budgets and fully controlled optimization conditions.

> **中文：** 因此，这些结果能够说明 Prada Tuning 与所评估 CLIP 基线在当前实验协议下的相对实际表现。但仅凭这些结果，不能证明 Prada Tuning 在相同调参预算和完全受控优化条件下内在优于所有基线。

### Reproduction Record / 复现实验记录

Before a final public release, each Table-I result should be associated with the following records:

> **中文：** 在最终公开发布之前，表 I 中的每条结果都应关联以下实验记录：

| Record / 记录 | Required information / 所需信息 |
|---|---|
| Method / 方法 | Exact baseline name and implementation / 确切的基线名称和实现 |
| Backbone / 骨干网络 | CLIP variant and encoder branch / CLIP 版本及使用的编码器分支 |
| Dataset and shot / 数据集与 shot | Dataset, shot number, and partition protocol / 数据集、shot 数和划分协议 |
| Hyperparameters / 超参数 | Values actually used for the published result / 生成论文结果时实际使用的参数 |
| Validation / 验证 | Validation metric and selection rule / 验证指标和选择规则 |
| Search / 搜索 | Search range and search budget / 搜索范围和搜索预算 |
| Stopping / 停止 | Early-stopping or fixed-epoch rule / 早停或固定训练轮数规则 |
| Checkpoint / 检查点 | Checkpoint used for final test evaluation / 最终测试所使用的检查点 |
| Runs / 运行 | Number of runs and seed set / 运行次数和随机种子集合 |
| Manifest / 清单 | Exact few-shot sample manifest / 确切的少样本采样清单 |
| Output / 输出 | Test accuracy, validation accuracy, and available diagnostics / 测试准确率、验证准确率和可用诊断信息 |






















