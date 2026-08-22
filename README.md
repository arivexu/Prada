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

## Reproducibility of the CLIP-Based Baselines in Table I / 表 I 中 CLIP 基线的可复现性说明

This section documents the optimization and model-selection procedures for the CLIP-based baseline comparison reported in Table I of the manuscript. It is independent of the pathology foundation-model contextual comparison and focuses only on the general-domain CLIP baselines, including Linear Probe, Full Fine-tuning, CoOp, CoCoOp, CLIP-Adapter, Tip-Adapter, MaPLe, UPT, MCPL, LoRA, PromptSRC, and CoPrompt.

> **中文：** 本节记录论文表 I 中基于 CLIP 的基线比较所采用的优化和模型选择流程。本节独立于病理基础模型上下文比较，仅关注通用领域 CLIP 基线，包括 Linear Probe、Full Fine-tuning、CoOp、CoCoOp、CLIP-Adapter、Tip-Adapter、MaPLe、UPT、MCPL、LoRA、PromptSRC 和 CoPrompt。

### Shared Evaluation Protocol / 统一评估协议

All CLIP-based methods use the same general-domain CLIP ViT-B/16 backbone and `224 x 224` image inputs. The same base train/validation/test partitions are used for every method. For an N-shot experiment, N training images are sampled per class from the base training partition. The validation subset contains at most `min(N, 4)` images per class, and the complete base test partition is retained for final evaluation. The primary metric is top-1 classification accuracy.

> **中文：** 所有基于 CLIP 的方法均使用相同的通用领域 CLIP ViT-B/16 骨干网络和 `224 x 224` 输入图像。所有方法使用相同的基础训练集、验证集和测试集划分。对于 N-shot 实验，从基础训练集中为每个类别采样 N 张训练图像。验证集每类最多包含 `min(N, 4)` 张图像，最终评估使用完整的基础测试集。主要评价指标为 Top-1 分类准确率。

The four datasets are evaluated at the image level using the 1-, 2-, 4-, 8-, and 16-shot settings. PCam uses its predefined partitions; BACH uses a class-stratified 80/10/10 image-level split; MHIST retains its official test partition and divides the official training partition into training and validation subsets; and BreakHis uses a class-stratified 80/10/10 image-level split.

> **中文：** 四个数据集均在图像级别进行评估，并使用 1、2、4、8 和 16-shot 设置。PCam 使用官方预定义划分；BACH 使用按类别分层的 80/10/10 图像级划分；MHIST 保留官方测试集，并将官方训练集进一步划分为训练集和验证集；BreakHis 使用按类别分层的 80/10/10 图像级划分。

All trainable neural baselines use cross-entropy loss. Unless otherwise stated, they use SGD, cosine learning-rate decay, one epoch of constant warm-up at `1e-5`, CLIP normalization, and FP16 computation. The test partition is used only for final reporting and is not used to select learning rates, coefficients, or checkpoints.

> **中文：** 所有可训练的神经网络基线均使用交叉熵损失。除非另有说明，这些方法均使用 SGD、余弦学习率衰减、1 个 epoch 的固定 `1e-5` 预热学习率、CLIP 归一化和 FP16 计算。测试集仅用于最终结果报告，不用于选择学习率、方法系数或模型检查点。

### Method-Specific Optimization Settings / 方法特定优化设置

The following table summarizes the documented configurations for the CLIP-based comparison. The epoch notation `50/100/200` denotes 50 epochs for 1-shot, 100 epochs for 2- and 4-shot, and 200 epochs for 8- and 16-shot experiments.

> **中文：** 下表总结了基于 CLIP 的比较中所记录的方法特定配置。训练轮数 `50/100/200` 表示 1-shot 使用 50 个 epoch，2-shot 和 4-shot 使用 100 个 epoch，8-shot 和 16-shot 使用 200 个 epoch。

| Method / 方法 | Trainable component / 可训练部分 | Optimizer / 优化器 | Initial LR / 初始学习率 | Batch size / 批量大小 | Epochs / 训练轮数 | Method-specific settings / 方法特定设置 |
|---|---|---:|---:|---:|---:|---|
| Linear Probe | Linear classifier on frozen CLIP image features / 冻结 CLIP 图像特征上的线性分类器 | L-BFGS logistic regression | Selected through `C` / 通过 `C` 选择 | Full sampled feature set / 全部采样特征 | Up to 1,000 iterations / 最多 1,000 次迭代 | L2-regularized logistic regression; validation-based `C` search / 带 L2 正则的逻辑回归；基于验证集搜索 `C` |
| Full Fine-tuning | CLIP image encoder and classification head / CLIP 图像编码器与分类头 | SGD | 0.0035 | 4 | `50/100/200`* | All image-encoder and classification-head parameters are updated / 更新全部图像编码器和分类头参数 |
| CoOp | Text context vectors / 文本上下文向量 | SGD | 0.002 | 32 | `50/100/200` | 16 context tokens; class-specific context disabled; class token at the end / 16 个上下文 token；不使用类别特定上下文；类别 token 位于末尾 |
| CoCoOp | Conditional prompt learner / 条件提示学习器 | SGD | 0.002 | 1 | 10 | Four context tokens initialized with `a photo of a` / 4 个上下文 token，以 `a photo of a` 初始化 |
| CLIP-Adapter | Visual feature adapter only / 仅视觉特征适配器 | SGD | 0.002 | 32 | `50/100/200` | Bottleneck reduction factor 4; adapter branch weight 0.2; image and text encoders frozen / 瓶颈压缩率 4；适配器分支权重 0.2；图像和文本编码器冻结 |
| Tip-Adapter | No gradient-based component / 无基于梯度的训练组件 | Training-free / 免训练 | -- | -- | -- | Key-value cache constructed from the few-shot training set / 根据少样本训练集构建键值缓存 |
| MaPLe | Vision-language prompts and projection layers / 视觉语言 prompt 和投影层 | SGD | 0.0035 | 16 | 100 | Prompt length 2; prompt depth requires historical configuration confirmation / Prompt 长度 2；prompt depth 需要根据历史配置确认 |
| UPT | Unified prompt generator / 统一 prompt 生成器 | SGD | 0.0015 | 16 | 100 | Fixed method-specific configuration; no dataset-specific sweep documented / 固定的方法特定配置；未记录数据集特定搜索 |
| MCPL | Method-specific prompt parameters / 方法特定 prompt 参数 | SGD | 0.002 | 4 | 100 | Fixed method-specific configuration; historical implementation should be archived explicitly / 固定的方法特定配置；应明确归档历史实现 |
| LoRA | Low-rank parameters with CLIP weights frozen / CLIP 权重冻结，仅训练低秩参数 | SGD | 0.002 | 4 | 200 | Rank 8; scaling parameter 16 / 秩为 8；缩放参数为 16 |
| PromptSRC | Vision and text prompts / 视觉和文本 prompt | SGD | 0.0025 | 4 | 50 | Context length 4; prompt depth 9; text/image regularization weights 25/10 / 上下文长度 4；prompt depth 9；文本/图像正则权重 25/10 |
| CoPrompt | Prompt and projection parameters / Prompt 和投影参数 | SGD | 0.0035 | 4 | 100 | Context length 2; prompt depth 9; two RandAugment views; cosine-distillation weight 1.0 / 上下文长度 2；prompt depth 9；两个 RandAugment 视图；余弦蒸馏权重 1.0 |
| Prada Tuning | DCP prompts, coupling layers, and ViSo Adapters / DCP prompt、耦合层和 ViSo Adapter | SGD | 0.0035 | 4 | `50/100/200` | Prompt length 2 and depth 11; ViSo Adapter after layers 4--6; covariance dimension 128 / Prompt 长度 2、深度 11；ViSo Adapter 位于第 4--6 层；协方差维度 128 |

\* The current executable `ViT/zy.yaml` configuration contains a 50-epoch default for full fine-tuning, whereas the manuscript describes a shot-dependent `50/100/200` schedule. The exact configuration used to generate the published Table-I rows must be recorded before the final public release.

> **中文：** 当前可执行的 `ViT/zy.yaml` 配置中，全量微调的默认训练轮数为 50 个 epoch，而论文描述的是随 shot 变化的 `50/100/200` 训练轮数方案。因此，在最终公开发布前，必须记录生成论文表 I 结果时实际使用的确切配置。

The current repository also contains a prompt-depth discrepancy for MaPLe: the manuscript describes depth 9, whereas the current `MaPLe/zy.yaml` snapshot specifies depth 11. This discrepancy should be resolved by attaching the exact historical configuration to the corresponding result files.

> **中文：** 当前仓库中 MaPLe 的 prompt depth 也存在不一致：论文描述为 9，而当前 `MaPLe/zy.yaml` 快照指定为 11。该差异应通过将生成对应结果时使用的历史配置附加到结果文件中来解决。

### Validation, Search, and Model Selection / 验证、搜索与模型选择

The neural baselines were evaluated using fixed method-specific configurations derived from their released recipes. We did not perform a broad dataset-specific grid search, and no hyperparameter was selected using the test partition. When validation was used for a selection decision, the criterion was top-1 accuracy on the fixed validation subset.

> **中文：** 神经网络基线采用源自其公开方案的固定方法特定配置进行评估。我们没有进行大范围的数据集特定网格搜索，也没有使用测试集选择超参数。当验证集被用于选择决策时，选择标准是固定验证子集上的 Top-1 准确率。

The CLIP linear probe was the exception because its regularization strength was explicitly selected using validation accuracy. The classifier uses L2-regularized logistic regression with the L-BFGS solver and `max_iter=1000`. The initial search evaluates `C` over `[1e6, 1e4, 1e2, 1, 1e-2, 1e-4, 1e-6]`, followed by eight log-space refinement steps around the best validation value.

> **中文：** CLIP 线性探测是一个例外，因为其正则化强度明确通过验证集准确率进行选择。分类器采用带 L2 正则的逻辑回归、L-BFGS 求解器和 `max_iter=1000`。初始搜索在 `[1e6, 1e4, 1e2, 1, 1e-2, 1e-4, 1e-6]` 上进行，随后围绕验证集上表现最优的值进行 8 次对数空间细化搜索。

The repository configurations do not enable `best_val` checkpoint selection for the Table-I neural baselines. Unless a method-specific configuration explicitly overrides this behavior, the final checkpoint from the last training epoch (`last_step`) is evaluated. No test-set checkpoint selection or test-set early stopping is used.

> **中文：** 对于表 I 中的神经网络基线，仓库配置没有启用 `best_val` 检查点选择。除非方法特定配置明确覆盖该行为，否则评估使用最后一个训练 epoch 的最终检查点（`last_step`）。不使用基于测试集的检查点选择，也不使用基于测试集的早停。

The epoch budget is treated as part of each method configuration rather than as an early-stopping limit. This means that the reported results correspond to the final checkpoint under the documented training schedule, rather than the best test accuracy observed retrospectively during training.

> **中文：** 训练轮数被视为各方法配置的一部分，而不是早停上限。这意味着所报告的结果对应于记录的训练计划下的最终检查点，而不是事后从训练过程中选择测试准确率最高的检查点。

### Number of Runs and Seed Matching / 运行次数与随机种子匹配

Table I reports the mean and standard deviation over three independent runs for each method and shot setting. Each run uses one few-shot training draw and evaluates the resulting model on the complete base test partition.

> **中文：** 表 I 对每种方法和每个 shot 设置报告 3 次独立运行的均值和标准差。每次运行使用一次少样本训练采样，并在完整的基础测试集上评估所得模型。

The number of runs alone does not guarantee a matched comparison. For every valid cross-method comparison, all methods must use one common seed set and identical few-shot manifests. The historical launcher scripts contain method-specific seed loops, so they are execution entry points rather than evidence that all historical outputs form a matched aggregate.

> **中文：** 运行次数本身并不能保证比较是匹配的。对于任何有效的方法间比较，所有方法必须使用同一组随机种子和完全相同的少样本清单。历史启动脚本包含方法特定的随机种子循环，因此这些脚本只是执行入口，不能证明所有历史输出可以构成匹配汇总。

Results computed from unmatched seed sets must not be presented as a matched comparison. If the historical result files do not satisfy the common-seed requirement, the affected rows must either be recomputed using the common seed set or reported separately as unmatched contextual results.

> **中文：** 使用不匹配随机种子集合计算得到的结果不得作为匹配比较呈现。如果历史结果文件不满足共同随机种子要求，则受影响的结果行必须使用共同随机种子重新计算，或者单独报告为非匹配的上下文结果。

### Diagnostic Interpretation of Weak Baselines / 较弱基线的诊断性解释

The weak results of CLIP-Adapter and full fine-tuning are interpreted descriptively rather than as proof of a specific optimization failure.

> **中文：** 对于 CLIP-Adapter 和全量微调的较弱结果，我们采用描述性解释，而不将其视为某一种特定优化失败的证据。

| Method and dataset / 方法与数据集 | Observed result / 观测结果 | Supported interpretation / 可支持的解释 | Not established / 无法据此确定 |
|---|---|---|---|
| CLIP-Adapter on BACH | 21.7%--25.0% across 1--16 shots / 1--16-shot 为 21.7%--25.0% | Little useful improvement from the fixed visual adapter under this domain shift / 固定视觉适配器在该领域偏移下带来的有效改进有限 | Mode collapse or a unique optimization defect / 模式坍塌或某一种特定优化缺陷 |
| CLIP-Adapter on BreakHis | 7.0%--12.8% for 1--8 shots and 18.1% at 16-shot / 1--8-shot 为 7.0%--12.8%，16-shot 为 18.1% | Severe low-shot weakness with partial recovery as labels increase / 低 shot 下表现较弱，标签增加后出现一定恢复 | The unique cause of the failure / 失败的唯一原因 |
| Full fine-tuning on BACH | Non-monotonic means; standard deviations reach 12.0% and 14.1% at 4- and 16-shot / 均值不单调，4-shot 和 16-shot 标准差达到 12.0% 和 14.1% | Sensitivity to few-shot sampling and optimization; overfitting remains possible / 对少样本采样和优化较敏感，不能排除过拟合可能 | Confirmed overfitting without epoch-wise validation traces / 在缺少逐 epoch 验证轨迹时确认过拟合 |
| Full fine-tuning on BreakHis | Accuracy increases from 8.3% to 39.6%; standard deviation reaches 8.8% and 8.3% at 8- and 16-shot / 准确率从 8.3% 提升到 39.6%，8-shot 和 16-shot 标准差达到 8.8% 和 8.3% | Additional labels improve optimization, but run-to-run sensitivity remains / 更多标签有助于优化，但运行间敏感性仍然存在 | Prediction collapse or unstable convergence as the sole explanation / 将预测坍塌或收敛不稳定确定为唯一解释 |

Aggregate accuracy and standard deviation alone cannot distinguish conclusively among overfitting, unstable convergence, insufficient adaptation capacity, and concentrated class predictions. In particular, an accuracy close to the class-prior chance level is not by itself evidence of mode collapse.

> **中文：** 仅凭汇总准确率和标准差，无法确定这些结果究竟源于过拟合、收敛不稳定、适配能力不足还是类别预测过度集中。特别是，接近类别先验随机水平的准确率本身并不能证明发生了模式坍塌。

The current experiment archive does not contain per-epoch validation curves, training-loss curves, or predicted-class histograms that can be traced unambiguously to every Table-I run. Accordingly, these diagnostics are not presented as available supporting evidence. The interpretation is restricted to the observed shot-wise trends and run-to-run variability.

> **中文：** 当前实验归档没有保留能够与表 I 每次运行明确对应的逐 epoch 验证曲线、训练损失曲线或预测类别直方图。因此，本 README 不将这些诊断内容作为已经存在的支持证据。相关解释仅限于观测到的 shot 变化趋势和运行间波动。

The manuscript discussion has therefore been calibrated to state that the weak baseline patterns are consistent with domain-shift difficulty, limited adaptation capacity, few-shot sampling sensitivity, and optimization sensitivity. The manuscript does not claim that the BACH result proves mode collapse, catastrophic overfitting, or any other unique failure mechanism.

> **中文：** 因此，论文 Discussion 中的表述已进行审慎化处理，说明较弱基线的表现可能与领域偏移困难、适配能力有限、少样本采样敏感性和优化敏感性相一致。论文不再声称 BACH 上的结果证明了模式坍塌、灾难性过拟合或其他某一种确定的失败机制。



### Reproducibility Boundaries / 可复现性边界

This README documents the available optimization settings and the evidence that can be traced to the current repository. It does not claim that every historical Table-I result was generated by the newest configuration file. Before the final public release, each reported row should be linked to its exact configuration file, few-shot manifest, seed set, checkpoint, and result log.

> **中文：** 本 README 记录当前仓库中能够核验的优化配置以及可以追溯的实验依据，但不声称论文表 I 中的每一条历史结果都由当前最新配置文件生成。在最终公开发布之前，每一条报告结果都应关联到其确切的配置文件、少样本清单、随机种子集合、模型检查点和结果日志。

The comparison is therefore intended to establish practical behavior under the reported image-level few-shot protocol. It should not be interpreted as a proof that Prada Tuning is intrinsically superior to every baseline under identical optimization budgets, identical architectures, or fully controlled experimental conditions.

> **中文：** 因此，该比较旨在说明所报告的图像级少样本协议下不同方法的实际表现。它不应被解释为 Prada Tuning 在完全相同的优化预算、相同网络结构或完全受控实验条件下内在优于所有基线的证明。






















