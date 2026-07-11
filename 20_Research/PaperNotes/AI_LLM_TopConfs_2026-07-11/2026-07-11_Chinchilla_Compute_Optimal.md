---
title: "Chinchilla - 计算最优训练规模律解读"
date: 2026-07-11
tags: [论文笔记, LLM, ScalingLaw, 预训练, NeurIPS2022]
status: 已整理
rating: 10
venue: NeurIPS 2022
source: https://arxiv.org/abs/2203.15556
---

# Training Compute-Optimal Large Language Models

> **作者**: Jordan Hoffmann et al.  
> **会议**: NeurIPS 2022  
> **关键词**: scaling laws, compute optimal, data tokens, Chinchilla  
> **原文**: https://arxiv.org/abs/2203.15556

## 1. 解决了什么问题

早期大模型竞赛常把重点放在“参数越大越好”，但训练 token 数没有同比增长。本文指出：在固定训练算力下，很多大模型不是太小，而是**参数太多、训练数据太少**，处在 undertrained 状态。

论文要回答的问题是：给定一笔训练算力预算，应当如何分配到模型参数量 `N` 和训练 token 数 `D` 上，才能获得最低预训练损失？

## 2. 核心结论

Chinchilla 的核心结论可以概括为：在计算最优训练下，模型大小和训练 token 数应该近似同步扩张。也就是说，参数翻倍时，数据 token 也应大致翻倍。

这和此前“把模型做得更大，数据相对固定”的做法形成鲜明对比。论文据此训练了 70B 参数的 Chinchilla，用和 280B Gopher 类似的训练算力，但使用更多 token，最终在大量下游任务上超过更大的 Gopher、GPT-3、Jurassic-1 和 Megatron-Turing NLG。

## 3. 原理拆解

论文将语言模型损失写成参数量和数据量的函数：

```text
L(N, D) = A / N^alpha + B / D^beta + L0
C ≈ k * N * D
```

其中 `N` 是参数量，`D` 是 token 数，`C` 是训练计算量。直觉上：

- `A / N^alpha` 表示模型容量不够带来的损失；
- `B / D^beta` 表示数据不够带来的损失；
- 在固定 `C` 下，一味增加 `N` 会挤压 `D`，反而让数据项变差。

最优点不是最大模型，而是模型容量和数据覆盖之间的平衡点。

## 4. 方法流程

1. 训练大量不同参数量、不同 token 数的小到中等规模 Transformer。
2. 拟合损失随 `N`、`D`、`C` 变化的经验规模律。
3. 根据规模律预测固定算力下的最优 `N` 和 `D`。
4. 按预测训练 Chinchilla，并与同等算力但更大参数的模型比较。

这个流程的重要性在于：它不是只给一个模型结果，而是给出了一套可外推的训练资源分配原则。

## 5. 与已有规模律的区别

| 视角 | 早期大模型实践 | Chinchilla |
|---|---|---|
| 主要扩张对象 | 参数量 | 参数量和 token 数同步 |
| 典型问题 | 大模型训练不足 | 更接近计算最优 |
| 关注点 | 模型容量 | 训练算力分配 |
| 影响 | 追求更大模型 | 推动更长训练和数据治理 |

## 6. 为什么值得读

这篇论文直接影响了 LLaMA、PaLM 后续模型、开源大模型和推理成本讨论。它让研究者意识到：同样的 GPU 预算，可能训练一个更小但更充分的数据模型，实际效果更好，推理成本也更低。

对今天的 LLM 工程而言，Chinchilla 是“预训练预算表”的基础文献。它还引出了后续问题：如果互联网高质量数据有限，是否还能按 Chinchilla 继续扩张？是否应该为了大量推理请求而故意 overtrain？这些问题都成为后续 scaling law 研究的主线。

## 7. 局限与思考

- 规模律依赖训练数据分布和模型架构，不能机械套到所有场景。
- 它主要优化预训练 loss，不直接优化指令遵循、推理、工具使用等后训练能力。
- 后来很多模型为了降低推理成本会超过 Chinchilla-optimal token 数训练，因此“训练最优”和“部署最优”并不完全一致。
- 数据质量、去重、合成数据、多轮对话数据没有被这个简单公式充分刻画。

## 8. 关联笔记

- [[2026-07-11_InstructGPT_RLHF|InstructGPT / RLHF]]
- [[2026-07-11_Emergent_Abilities_Mirage|Emergent Abilities Mirage]]

