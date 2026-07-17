---
date: 2026-07-17
arxiv: "2607.12481"
tags: [bilevel-optimization, DER, EV-charging, DLMP, robust-optimization, distribution-network]
---

# Scenario-Free Uncertainty-Aware DLMP-Based Bilevel Coordination of EV Charging and Reactive Power Support in Distribution Networks

## 1. 解决了什么问题

配电网中大规模EV接入带来双重挑战：充电负荷的不确定性（受用户行为、PV出力影响），以及电压安全问题。传统方法用随机规划（场景多、算不动）或鲁棒优化（假设Gaussian分布、过于保守）。**本文要解决的核心问题：如何在不依赖大量场景的前提下，同时协调EV的有功充电和无功支撑，实现计算高效且不失鲁棒性的配电网调度？**

## 2. 核心方法解读（师兄给你讲）

这篇的思路特别巧妙，三招合一：

**第一招：双层架构**。上层是EV聚合商——它要根据电价和DLMP（配电网节点边际电价）来决策每个充电桩充多少有功、发多少无功，目标是总充电成本最小。下层是配电网的EMS——你给我有功/无功方案，我跑一遍经济调度，算出每个节点的DLMP和电压情况，如果不满足约束就反馈给你。

**第二招：compact robust counterpart（紧凑鲁棒对等）**。这里是最亮眼的地方：他们不用"先采样500个场景再优化"的随机规划思路，也不用传统鲁棒优化那套假设Gaussian分布的保守玩法。取而代之的是**normal-minus-beta分布**来刻画净负荷的不确定性——这个分布可以表达不对称的尾部分布（PV低出力+高负荷是重尾），比Gaussian更真实。然后通过紧凑RC reformulation直接把不确定优化问题转化成一个确定性的、可求解的凸问题。

**第三招：KKT条件+Big-M线性化**。下层问题通过KKT条件转成约束，再用Big-M方法处理互补松弛条件，得到一个单层MILP。这里他们证明了一个exactness lemma——保证了DLMP的经济学解释在reformulation后不变，这点很重要，因为DLMP本身就是价格信号的核心。

## 3. 算法/模型框架说明

```
上层（EV Aggregator）:
  min 充电成本 = Σ DLMP_i * P_charge_i
  s.t. 充电需求约束、无功容量约束（非单位功率因数运行）

下层（DSO/EMS）:
  min 发电成本（经济调度）
  s.t. 潮流约束（DistFlow/线性化）、电压约束、馈线容量约束
  → 输出 DLMP_i（对偶变量）

不确定性建模:
  净负荷 = 负荷 - PV → 服从 Normal-Minus-Beta 分布
  → Compact Robust Counterpart Reformulation
  → 确定性MILP

求解流程:
  双层 → KKT条件 → 单层MPEC → Big-M线性化 → MILP（Gurobi/CPLEX直接解）
```

## 4. 关键创新点

1. **Normal-minus-beta分布建模**：首次在配电网鲁棒优化中引入这种非对称分布来刻画净负荷不确定性，比Gaussian更贴合实际（PV出力偏度大）
2. **场景无关(scenario-free)**：不需要生成/缩减场景，直接通过解析的RC reformulation得到确定性等价问题
3. **有功+无功联合协调**：EV充电桩不仅调节充电功率，还通过非单位功率因数运行提供无功支撑来改善电压
4. **DLMP保真性证明**：通过exactness lemma保证reformulation后DLMP的经济学含义不被破坏

## 5. 与已有方法的区别

| 对比维度 | 随机规划(SP) | 传统鲁棒优化(RO) | 本文方法 |
|---------|------------|---------------|---------|
| 不确定性描述 | 场景集（需大量采样） | 区间/椭球（假设Gaussian） | Normal-minus-beta分布 |
| 计算复杂度 | 场景数×单次求解 | 低 | 低（确定性MILP） |
| 保守性 | 取决于场景质量 | 可能过保守 | 适中（分布更真实） |
| 无功协调 | 少有 | 少有 | ✅ 非单位功率因数 |

## 6. 为什么值得关注

- **方法论可迁移**：compact RC + normal-minus-beta这套组合拳，可以搬到VPP聚合、储能调度、需求响应等任何配电网不确定优化场景
- **工程实用性强**：MILP可以直接用商业求解器解，IEEE 33节点上验证了计算效率远超SP和传统RO
- **紧跟趋势**：EV渗透率越来越高，无功-电压问题在分布式PV密集区越来越突出

## 7. 待思考的问题

1. Normal-minus-beta分布的参数（均值、方差、偏度）在实际中如何在线估计？文中假设已知，但现实场景下这些参数本身也在变
2. 单时段还是多时段？EV充电有时序耦合（SOC状态），当前模型是静态单时段，扩展到多时段需要考虑储能动态
3. 多个聚合商竞争怎么办？当前是单聚合商+单DSO，实际上可能有多个充电运营商，博弈结构会复杂很多
4. 通信延迟和隐私：双层优化隐含"下层DSO愿意分享DLMP"的假设，现实中的信息壁垒怎么处理？
