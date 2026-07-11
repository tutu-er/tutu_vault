---
title: "Chain-of-Thought Prompting - 原理解读"
date: 2026-07-11
tags: [论文笔记, LLM, Prompting, Reasoning, NeurIPS2022]
status: 已整理
rating: 10
venue: NeurIPS 2022
source: https://arxiv.org/abs/2201.11903
---

# Chain-of-Thought Prompting Elicits Reasoning in Large Language Models

> **作者**: Jason Wei et al.  
> **会议**: NeurIPS 2022  
> **关键词**: chain-of-thought, prompting, reasoning, emergence  
> **原文**: https://arxiv.org/abs/2201.11903

## 1. 解决了什么问题

标准 few-shot prompting 给模型几个输入输出示例，但答案通常是一步到位。这对分类、抽取、简单问答够用，对数学应用题、常识推理、符号推理却不够。复杂任务的答案往往不是直接映射，而是需要中间步骤。

本文提出的问题很简单：如果在 prompt 的示例里显式展示“中间推理过程”，大模型是否会学会在新问题上也生成推理链，从而得到更准确的答案？

## 2. 核心方法

Chain-of-Thought prompting 的形式是：

```text
问题 -> 中间推理步骤 -> 最终答案
```

它不改模型参数，只改上下文示例。模型看到若干带推理过程的样例后，会模仿这种格式，在生成答案前先生成一串自然语言推理步骤。

关键点是：CoT 不是给模型注入新知识，而是改变推理时的计算轨迹。自然语言中间步骤为模型提供了更长的“scratchpad”，让复杂问题可以被拆成多个局部简单问题。

## 3. 原理理解

从计算角度看，普通 prompting 把所有推理压缩到一次隐式前向传播里；CoT 则把部分隐式计算外显为 token 序列。生成的每个中间 token 都会进入后续上下文，成为下一步推理的条件。

因此，CoT 相当于给自回归模型增加了可读的中间状态：

```text
x -> z1 -> z2 -> ... -> zk -> y
```

其中 `z` 是推理链，`y` 是答案。虽然这些步骤不一定忠实反映模型内部机制，但它们确实改变了后续 token 的条件分布。

## 4. 关键发现

1. **规模门槛明显**: CoT 在小模型上帮助有限，在足够大的模型上效果显著。
2. **任务类型相关**: 对算术、符号、常识多步推理更有帮助，对简单语义任务不一定必要。
3. **无需训练**: 只通过上下文示例就能触发能力。
4. **自然语言可解释性**: 推理链给调试和分析提供了表面线索，尽管不能等同于真实因果解释。

## 5. 与已有方法的区别

| 方法 | 是否改参数 | 是否生成中间步骤 | 适用场景 |
|---|---|---|---|
| 标准 few-shot | 否 | 否 | 简单映射任务 |
| Fine-tuning | 是 | 取决于数据 | 特定任务 |
| CoT prompting | 否 | 是 | 多步推理任务 |

CoT 的价值在于极低成本地打开了大模型的推理能力，也让“prompt 设计”从格式技巧变成了一种推理计算控制方式。

## 6. 实验与结果

论文在算术、常识、符号推理等任务上展示了显著提升。尤其在 GSM8K 数学应用题上，带 CoT 的大模型表现超过许多专门微调系统。

重要的是，论文不是证明模型真的“像人一样推理”，而是证明自然语言中间过程可以显著改善复杂任务的输出分布。

## 7. 为什么值得读

CoT 是后续 reasoning LLM、self-consistency、Tree of Thoughts、ReAct、process supervision、test-time compute 的起点。今天很多“让模型多想一会儿”的方法，本质上都是在扩展 CoT 的思想：把推理过程变成长一点、可搜索一点、可验证一点、可反馈一点。

## 8. 局限与思考

- 推理链可能是事后合理化，不一定忠实。
- 错误中间步骤会把模型带向错误答案。
- 对小模型、知识不足任务或需要外部信息任务帮助有限。
- 公开展示完整推理链在安全和隐私场景中可能带来额外风险。

## 9. 关联笔记

- [[2026-07-11_Self_Consistency|Self-Consistency]]
- [[2026-07-11_ReAct|ReAct]]
- [[2026-07-11_What_Is_ICL|What learning algorithm is in-context learning?]]

