---
title: "What learning algorithm is in-context learning? - ICL 原理解读"
date: 2026-07-11
tags: [论文笔记, LLM, InContextLearning, Transformer, ICLR2023]
status: 已整理
rating: 9
venue: ICLR 2023
source: https://arxiv.org/abs/2211.15661
---

# What learning algorithm is in-context learning? Investigations with linear models

> **作者**: Ekin Akyürek et al.  
> **会议**: ICLR 2023  
> **关键词**: in-context learning, implicit learning, linear regression, transformer mechanism  
> **原文**: https://arxiv.org/abs/2211.15661

## 1. 解决了什么问题

大语言模型能在 prompt 中看到几个示例后完成新任务，看起来像是在“不更新参数的情况下学习”。这就是 in-context learning。问题是：模型到底是在记忆训练分布中的模板，还是在上下文里隐式执行某种学习算法？

本文用线性回归作为可分析的简化场景，研究 Transformer 的 ICL 是否可以被解释为在激活中实现梯度下降、岭回归或最小二乘等经典学习算法。

## 2. 核心观点

论文提出一个强解释：Transformer 可以把上下文中的示例编码成一个隐式模型，并随着更多示例出现而更新这个隐式模型。换句话说，ICL 不只是模式匹配，也可能是在前向传播中执行某类学习算法。

在简化线性任务里，模型输入是一串 `(x_i, y_i)` 示例和一个查询 `x_q`，输出预测 `y_q`。论文发现训练后的 Transformer 行为与经典估计器非常接近。

## 3. 原理拆解

线性回归任务中，理想预测器可以写成：

```text
y_q = x_q^T w
```

上下文示例提供了估计 `w` 的信息。Transformer 的注意力层可以在 token 之间聚合矩阵、向量和误差信息，从而近似：

- 梯度下降更新；
- 岭回归闭式解；
- 最小二乘解；
- 在某些条件下接近 Bayesian estimator。

关键在于，注意力不是单纯检索相似样本，而可以组合上下文统计量，形成一个临时任务模型。

## 4. 方法证据

论文提供三类证据：

1. **构造性证明**: Transformer 有表达能力实现线性模型学习算法。
2. **行为拟合**: 训练出的 ICL 模型预测接近梯度下降、岭回归或最小二乘。
3. **表征分析**: 模型后层激活中出现了类似权重向量和矩阵统计量的编码。

这三类证据从“能不能实现”“训练后像不像”“内部有没有痕迹”三个角度支持算法解释。

## 5. 与已有解释的区别

| 解释 | 核心看法 | 本文补充 |
|---|---|---|
| 模式匹配 | 找相似示例并模仿 | 无法解释连续函数估计 |
| 任务识别 | 判断 prompt 属于哪个任务 | 还要解释如何适应新参数 |
| 隐式算法 | 在激活中执行学习过程 | 本文在可控线性场景中给证据 |

## 6. 为什么值得读

这篇论文的价值在于把 ICL 从神秘现象拉回可分析的学习理论问题。它说明 Transformer 的上下文窗口不是普通缓存，而可能是一个可微的临时学习工作区。

对 LLM 应用来说，这解释了为什么示例顺序、噪声、上下文长度、任务分布会显著影响 few-shot 效果；对理论研究来说，它为“预训练如何内化学习算法”提供了可验证入口。

## 7. 局限与思考

- 线性回归是简化场景，不能直接等同于自然语言任务。
- 真实 LLM 的 ICL 可能混合了算法学习、语义检索、模板匹配和世界知识。
- 论文解释的是“可能机制”，不是所有 ICL 行为的唯一机制。
- 如何在复杂任务中识别模型是否真的执行了可泛化算法，仍是开放问题。

## 8. 关联笔记

- [[2026-07-11_Chain_of_Thought|Chain-of-Thought Prompting]]
- [[2026-07-11_Emergent_Abilities_Mirage|Emergent Abilities Mirage]]

