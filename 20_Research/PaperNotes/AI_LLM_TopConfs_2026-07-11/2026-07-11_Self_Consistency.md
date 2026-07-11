---
title: "Self-Consistency - CoT 多路径投票解读"
date: 2026-07-11
tags: [论文笔记, LLM, Reasoning, Decoding, ICLR2023]
status: 已整理
rating: 9
venue: ICLR 2023
source: https://arxiv.org/abs/2203.11171
---

# Self-Consistency Improves Chain of Thought Reasoning in Language Models

> **作者**: Xuezhi Wang et al.  
> **会议**: ICLR 2023  
> **关键词**: self-consistency, chain-of-thought, sampling, majority vote  
> **原文**: https://arxiv.org/abs/2203.11171

## 1. 解决了什么问题

CoT prompting 让模型能写出中间推理步骤，但如果用 greedy decoding，只得到一条推理链。一条链可能偶然走错，而复杂问题通常可以从多条不同路径得到同一个正确答案。

本文要解决的是：能否不改模型、不训练 verifier，只通过采样多条推理路径并聚合答案，提高 CoT 的稳定性和准确率？

## 2. 核心方法

Self-Consistency 的流程是：

1. 对同一个问题，用 CoT prompt 多次采样，生成多条不同推理链。
2. 从每条推理链中抽取最终答案。
3. 对答案做投票或边缘化，选择出现最一致的答案。

直觉是：错误推理可能五花八门，正确推理往往会收敛到同一个答案。与其相信第一条链，不如让模型“从几个角度想一遍”，再看答案是否一致。

## 3. 原理解释

普通 CoT 近似选择：

```text
argmax_y p(y | x)
```

Self-Consistency 更接近对隐含推理路径 `r` 做边缘化：

```text
p(y | x) = sum_r p(y, r | x)
```

由于不能穷举所有推理路径，论文用温度采样得到有限条 `r`，再用最终答案频率近似边缘概率。它不是让单条推理更可靠，而是让多个不完全可靠的样本通过集成降低方差。

## 4. 与已有方法的区别

| 方法 | 解码方式 | 主要问题 | Self-Consistency 的改进 |
|---|---|---|---|
| Greedy CoT | 取最高概率路径 | 容易被单一路径错误拖垮 | 采样多路径 |
| Beam search | 找高概率文本 | 高概率不等于正确推理 | 关注答案一致性 |
| verifier rerank | 需要额外模型 | 训练成本高 | 不需要训练新模型 |

## 5. 实验与结果

论文在 GSM8K、SVAMP、AQuA、StrategyQA、ARC-Challenge 等推理任务上带来明显提升。GSM8K 上的提升尤其突出，说明算术和多步推理问题非常适合“多路径采样 + 答案聚合”。

这个结果后来也解释了为什么推理模型常常受益于 test-time compute：推理时多花计算，可以用采样、搜索、验证、投票等方式换取更高正确率。

## 6. 为什么值得读

Self-Consistency 是从 CoT 到 test-time scaling 的关键桥梁。它告诉我们：大模型的能力不仅由训练决定，也由推理时如何使用计算决定。对于工程实践，它是最简单、收益很高的 reasoning 增强方法之一。

## 7. 局限与思考

- 成本随采样条数线性增长。
- 对开放式答案，答案抽取和聚合更困难。
- 如果模型系统性错误，多数投票会强化错误。
- 它提高的是结果一致性，不保证推理过程忠实或可解释。

## 8. 关联笔记

- [[2026-07-11_Chain_of_Thought|Chain-of-Thought Prompting]]
- [[2026-07-11_ReAct|ReAct]]

