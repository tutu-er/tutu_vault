---
title: "Transformers are SSMs / Mamba-2 - 状态空间对偶解读"
date: 2026-07-11
tags: [论文笔记, SSM, Mamba, Transformer, ICML2024]
status: 已整理
rating: 10
venue: ICML 2024
source: https://arxiv.org/abs/2405.21060
---

# Transformers are SSMs: Generalized Models and Efficient Algorithms Through Structured State Space Duality

> **作者**: Tri Dao, Albert Gu  
> **会议**: ICML 2024  
> **关键词**: state space model, Mamba-2, attention, semiseparable matrix, long sequence  
> **原文**: https://arxiv.org/abs/2405.21060

## 1. 解决了什么问题

Mamba 等选择性状态空间模型在长序列上展示了强效率，但它们和 Transformer attention 的关系并不清晰。研究者需要知道：SSM 是完全不同的架构路线，还是和 attention 有更深的数学统一？

本文要解决的是：建立 attention 与结构化 SSM 之间的对偶关系，并据此设计更快、更可扩展的 Mamba-2。

## 2. 核心观点

论文提出 Structured State Space Duality，指出某些 SSM 和 attention 变体可以统一到结构化半可分矩阵的计算框架下。

通俗说，序列模型做的都是把历史 token 信息混合到当前位置。Attention 用显式 `QK^T` 权重矩阵表示这种混合；SSM 用递归状态更新表示这种混合。论文展示二者可以看作同一类结构化矩阵运算的不同实现方式。

## 3. 原理解释

SSM 的递推形式大致是：

```text
h_t = A_t h_{t-1} + B_t x_t
y_t = C_t h_t
```

展开递推后，`y_t` 实际上也是对历史 `x_1 ... x_t` 的加权和。这个加权矩阵具有特殊下三角结构。Attention 同样可以写成矩阵混合，只是权重来自 query-key 相似度。

SSD 框架把这些混合矩阵放到统一的半可分结构中，从而可以借用矩阵乘法、分块并行和硬件友好算法来加速 SSM。

## 4. Mamba-2 的改进

Mamba-2 基于这个理论框架改进了原 Mamba：

1. 使用更适合矩阵乘法加速的 SSD 层。
2. 支持更大的 state dimension，提升表达能力。
3. 改善并行性和硬件利用率。
4. 在保持长序列线性优势的同时，提高训练吞吐。

论文报告 Mamba-2 核心层比 Mamba 更快，并在语言建模上保持竞争力。

## 5. 与 Transformer / Mamba 的关系

| 模型 | 核心机制 | 优势 | 局限 |
|---|---|---|---|
| Transformer | softmax attention | 动态 pairwise 交互强 | 长序列二次成本 |
| Mamba | selective scan | 线性长序列 | 理论关系和扩展性待清晰 |
| Mamba-2 / SSD | SSM-attention 统一框架 | 更快、更可解释的 SSM | 仍需大规模验证 |

## 6. 为什么值得读

这篇论文的重要性在于，它不是单纯提出一个新模型，而是给 Transformer 与 SSM 之间搭了一座桥。它让“架构替代”从经验比较走向结构化理解。

如果未来 LLM 架构不再完全由 attention 主导，那么 SSD/Mamba-2 这类工作就是关键节点。它同时连接了长上下文效率、硬件友好计算和序列建模理论。

## 7. 局限与思考

- Mamba-2 是否能在最大规模、最复杂指令和推理任务上全面替代 Transformer，还没有定论。
- Attention 的显式检索式交互在某些任务中可能仍有优势。
- SSM 的隐状态压缩可能导致长程细节保留问题。
- 真实系统可能采用 hybrid 架构，而不是纯 attention 或纯 SSM。

## 8. 关联笔记

- [[2026-07-11_Hyena|Hyena]]
- [[2026-07-11_FlashAttention|FlashAttention]]

