---
title: "Emergent Abilities Mirage - 大模型涌现能力质疑解读"
date: 2026-07-11
tags: [论文笔记, LLM, Evaluation, ScalingLaw, NeurIPS2023]
status: 已整理
rating: 9
venue: NeurIPS 2023
source: https://arxiv.org/abs/2304.15004
---

# Are Emergent Abilities of Large Language Models a Mirage?

> **作者**: Rylan Schaeffer, Brando Miranda, Sanmi Koyejo  
> **会议**: NeurIPS 2023  
> **关键词**: emergence, evaluation metrics, scaling, LLM analysis  
> **原文**: https://arxiv.org/abs/2304.15004

## 1. 解决了什么问题

许多论文声称大语言模型存在“涌现能力”：小模型完全不会，参数规模到某个阈值后突然会了。这种说法很吸引人，也影响了人们对 scaling 的预期。

本文提出质疑：所谓涌现是否真的是模型内部能力突然发生相变，还是评测指标选择造成的视觉假象？

## 2. 核心观点

论文认为，很多“突然出现”的曲线来自非线性或离散指标。例如 accuracy 会把连续改进压缩成 0/1；当模型输出概率逐渐变好但还没跨过答案判定阈值时，accuracy 看起来不变；一旦跨过阈值，就像突然涌现。

如果改用更连续的指标，如 token-level loss、校准概率、部分分数，能力随规模变化可能更平滑。

## 3. 原理解释

假设模型对正确答案的概率随规模平滑上升：

```text
p(correct answer | model size) 平滑增加
```

但评测使用 exact match：

```text
score = 1 if answer exactly right else 0
```

那么当概率还没超过某个决策边界时，得分长期接近 0；超过边界后，得分快速上升。外观看像能力突然出现，但底层概率变化可能一直是连续的。

## 4. 方法证据

论文做了三类分析：

1. 用简单数学模型说明非线性指标可以制造涌现形状。
2. 在 InstructGPT/GPT-3 系列任务上比较不同指标。
3. 对 BIG-Bench 中的涌现任务做元分析，并展示改变指标后涌现现象会减弱或消失。

论文还说明，在视觉任务中也可以通过指标选择制造类似“涌现”曲线。

## 5. 与主流叙事的区别

| 观点 | 解释 |
|---|---|
| 强涌现叙事 | 大模型到某规模后获得新能力 |
| 本文观点 | 至少一部分涌现来自指标非线性和统计处理 |
| 折中理解 | 模型能力可能平滑提高，但任务成功率可能非线性显现 |

## 6. 为什么值得读

这篇论文提醒我们：评测指标不是中性的。LLM 能力讨论中，曲线形状、阈值、答案抽取方式、任务聚合方式都会影响结论。

它不是否认大模型能力随规模增强，而是要求研究者区分“模型行为真实突变”和“测量方式导致突变”。这对阅读 scaling law、benchmark 排名、reasoning 能力报告都非常重要。

## 7. 局限与思考

- 即使部分涌现是指标假象，也不代表所有能力变化都是平滑的。
- 用户体验层面的可用性本来就可能是阈值型的：错一点也是错。
- 论文主要讨论固定模型输出和指标关系，对训练动态内部变化解释有限。
- 后续 reasoning model 的 test-time compute 又引入新的非线性能力曲线，需要重新分析。

## 8. 关联笔记

- [[2026-07-11_Chinchilla_Compute_Optimal|Chinchilla]]
- [[2026-07-11_What_Is_ICL|What learning algorithm is in-context learning?]]

