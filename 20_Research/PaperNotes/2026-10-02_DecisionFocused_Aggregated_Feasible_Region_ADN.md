# Decision-Focused Aggregated Feasible Region Characterization for Active Distribution Networks

- **论文标题:** Decision-Focused Aggregated Feasible Region Characterization for Active Distribution Networks
- **作者/机构:** Danyang Xu, Kai Hou, Zeyu Liu, Hongjie Jia, Xiaodan Yu（天津大学 智能配电装备与系统全国重点实验室）
- **来源:** IEEE Transactions on Smart Grid（Early Access，2026）
- **链接:** https://ieeexplore.ieee.org/document/11715865
- **DOI:** 10.1109/TSG.2026.3738461
- **标签:** #FeasibleRegion #DecisionFocused #ActiveDistributionNetwork #Aggregation #SmartGrid

> ⚠️ 论文为 2026-09-30 刚入库的 Early Access，IEEE Xplore 与 Crossref 均未放出摘要，以下为基于标题 + 相关文献脉络的初步标注，待拿到全文/摘要后再补充方法细节。

---

## 1. 解决了什么问题

主动配电网（ADN）内部有大量分布式资源（DER），上级调度/输电网不希望、也无法逐一建模每个资源。核心问题：如何把 ADN 内部海量异构资源的**聚合可行域（aggregated feasible region）**刻画出来，提交给上层用于调度/出清。本文的关键差异在"**Decision-Focused**"——不是单纯追求可行域近似得"更准"，而是让刻画结果直接服务于下游决策（调度/出清目标），使决策更优。

## 2. 核心方法解读（师兄式讲解）

先厘清概念：可行域表征（Feasible Region Characterization）就是回答"这个 ADN 作为一个整体，到底能向上提供多大的功率/灵活性空间"。这和之前记的 VPP 可行域（见 `2026-09-01_VPP_Feasible_Region_Inverse_Optimization`）是同一类问题，只是对象从 VPP 换成 ADN。

传统做法两条路：
- **解析聚合**（Minkowski 和、顶点枚举等）——精确但通用性差，换约束/换资源就要重推；
- **数据驱动近似**（如逆优化拟合虚拟电池）——通用但只追求"拟合误差小"。

本文的"Decision-Focused"是第三层视角：**误差小的近似，不一定带来好的决策**。所以它把"可行域刻画"和"上层决策目标"耦合起来，让刻画参数朝"使最终调度/出清决策最优"的方向去优化——这是 decision-focused learning 在可行域表征上的迁移（同类思想见 `2026-08-07_DiffAPQP_Decision_Focused_Learning`）。

> 具体技术路线（聚合模型形式、决策耦合的损失/目标设计、是否端到端可微等）需待全文补充。

## 3. 算法/模型框架说明

```
┌────────────────────────────────────────────┐
│  ADN 内部异构 DER（光伏/储能/柔性负荷…）      │
└──────────────────┬─────────────────────────┘
                   │ 聚合
                   ▼
┌────────────────────────────────────────────┐
│  聚合可行域刻画（参数化/学习得到）            │
│  关键：Decision-Focused                     │
│  ↳ 不以"近似误差最小"为目标                  │
│  ↳ 而以"下游决策最优"为目标                   │
└──────────────────┬─────────────────────────┘
                   ▼
┌────────────────────────────────────────────┐
│  上层调度/出清模型（消费该可行域）            │
│  → 反馈决策质量，反推可行域刻画参数            │
└────────────────────────────────────────────┘
```

## 4. 关键创新点（初步）

- **Decision-Focused 视角**：把可行域表征从"拟合精度"升级到"决策价值"，直击"近似再准、用起来不优"的痛点
- **面向主动配电网**：区别于多数针对 VPP/聚合商的工作，落到 ADN 场景
- **聚合 + 决策闭环**：可行域刻画与上层调度形成端到端耦合

## 5. 与已有方法的区别

| 方法 | 思路 | 局限 |
|------|------|------|
| 解析聚合（Minkowski 和等） | 精确推导聚合可行域 | 通用性差、难扩展 |
| 数据驱动近似（逆优化/虚拟电池） | 拟合误差最小 | 近似准≠决策优 |
| 本文（Decision-Focused） | 以决策质量为优化目标 | 待看全文验证 |

## 6. 为什么值得关注

- 与 VPP 可行域系列（`2026-09-01_VPP_Feasible_Region_Inverse_Optimization`、`2026-09-01_VPP_RealTime_Power_Disaggregation`）同属"聚合灵活性表征"主线，本文把视角从 VPP 推到 ADN，并引入 decision-focused 范式
- "decision-focused + 可行域"结合在电力系统里还比较新，对做聚合/出清/learning-to-optimize 的研究有直接启发
- 天津大学（贾宏杰团队）在主动配电网领域积累深厚，值得跟踪

## 7. 待补充/待思考

- [ ] 拿到全文后补：聚合模型形式（凸多面体？虚拟电池？）、decision-focused 的损失/目标如何构造、是否端到端可微
- [ ] 与 VPP 可行域逆优化方法相比，decision-focused 带来的决策增益有多大？有没有理论保证？
- [ ] 聚合可行域对原始 ADN 的**保守性/可行性**如何保证（避免夸大能力导致出清后无法执行）？
- [ ] 能否迁移到 DRO / 两阶段随机调度场景？

---

**最后更新**: 2026-10-02
