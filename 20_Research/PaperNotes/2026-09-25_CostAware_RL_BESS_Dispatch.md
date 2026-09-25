# 成本感知强化学习：动作掩码 + 投影下的电池储能调度

- 论文：Cost-Aware Reinforcement Learning with Action Masking and Projection for Battery Energy Storage Dispatch under Suppressed-Spread Market Shifts
- 作者：Kuanlin Chen, Chen-Wei Kuo, Cheng-En Ou
- 来源：arXiv:2609.23590（2026-09-20），IEEE IECON 2026 录用
- 链接：https://arxiv.org/abs/2609.23590

## 1. 解决了什么问题

电价峰谷差收窄（suppressed spread）后，BESS 循环套利的利润变薄，调度既要保证物理可行性，又要在有限价差里挤出经济性。本文研究 PPO 控制器如何把"经济筛选"和"物理可行性强制"这两件事干净地拆开。

## 2. 核心方法解读（师兄口吻）

核心思路是"职责分离"：把 PPO 的决策拆成两层。物理层（动作掩码 mask + 紧急投影 projection）是确定性的，负责"哪些动作合法、越界就给你投影回来"，保证爬坡、SOC 等硬约束永不被违反；经济层是一个"因果的、forecast-informed 的 advisory"，只负责给经济建议，不碰安全。这样物理安全由硬约束保证，经济判断由学习信号给，互不干扰。

结论也很诚实：在这个设定下 PPO 并没有超过 proxy-cost MPC，论文没吹 RL，而是把它拆解到"可诊断"的程度——这本身就有价值。

## 3. 算法/模型框架

- 控制器：PPO（proximal policy optimization）；
- 物理层：预选动作掩码（pre-selection physical action mask）+ 紧急投影（emergency projection）；
- 经济层：一个因果的、forecast-informed 的经济 advisory（causal 24-step 预测）；
- 消融 M0–M6：去掉 mask 会导致数千次不可行请求打到投影上；同时去掉两个物理层会暴露爬坡违规。

## 4. 关键创新点

1. 显式地把"可行性强制"与"经济筛选"分离，可诊断、可解释；
2. 因果 forecast 输入，保证在线部署时的信息可用性；
3. 结论不吹 RL，与 proxy-cost MPC 诚实对比，定位收益来源。

## 5. 与已有方法的区别

多数 RL 做 BESS 调度直接把约束塞进 reward 或环境里，训练不稳定、容易出不可行解。本文用 mask + projection 在动作空间硬保证可行性，把经济建议当弱信号，鲁棒性和可解释性都更好。

## 6. 为什么值得关注

低峰谷差环境下储能经济性普遍承压，如何设计"安全—经济"解耦的 RL/优化混合策略，是储能实际落地的关键。这套 mask/projection 的思路能直接借鉴到 VPP/DER 的 RL 调度里。

## 7. 待思考的问题

- 能否用带约束的安全 RL（CPO、PPO-Lagrangian 等）做严格对比？
- advisory 能否换成更紧的 MPC/优化层，形成 hierarchical 结构？
- 电池寿命/老化未建模，如何纳入目标？
