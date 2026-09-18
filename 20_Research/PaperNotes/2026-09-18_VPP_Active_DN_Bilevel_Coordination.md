# Day-ahead Coordination of Virtual Power Plants within Active Distribution Networks using Deterministic Bi-Level Optimization

- **论文**：Day-ahead Coordination of Virtual Power Plants within Active Distribution Networks using Deterministic Bi-Level Optimization
- **来源**：arXiv:2609.17927（math.OC / eess.SY）
- **作者**：Daniel Gebbran 等
- **链接**：https://arxiv.org/abs/2609.17927
- **标签**：#VPP #主动配网 #双层优化 #MINLP #MPEC #DER聚合

---

## 1. 解决了什么问题

VPP 嵌入主动配电网后，配电系统运营商（DSO）关心网损和电压质量，而 VPP 聚合商只关心自己的利润，两者目标天然冲突。现有协调方法大多把下层的潮流问题**线性化**成 MILP 以便求解，但牺牲了精度。这篇要解决的是：**在保留完整交流潮流（AC-OPF）的前提下，如何对 DSO 与 VPP 做日前协调，并量化聚合的电网价值**。

## 2. 核心方法解读（师兄给你讲）

这本质是个"领导—跟随"博弈：

- **上层（DSO 是领导）**：最小化自己的支出 + 有功网损 + 电压偏差，同时受非线性 AC 潮流约束；
- **下层（VPP 是跟随者）**：在 DSO 给出的统一价格信号下，最大化自己的利润。

因为上下层嵌套，直接解很麻烦。作者的套路是经典的三板斧：

1. 把下层问题用它的 **KKT 最优性条件 + 强对偶定理**替换掉，把双层问题压成一个单层的 **MPEC**（带均衡约束的数学规划）；
2. 互补松弛条件再用 **Fortuny-Amat big-M** 技巧线性化；
3. 最终得到一个可解的 MILP。

关键点在于：**下层没有像大多数工作那样线性化成 DistFlow/MILP，而是保留了完整的 AC-OPF 非线性方程**，所以最初的模型是一个双层 MINLP，精度更高。

## 3. 算法/模型框架

```
双层 MINLP（上层 DSO 目标 / 下层 VPP 利润，AC-OPF 约束）
      │  KKT 最优性条件 + 强对偶
      ▼
单层 MPEC（含互补条件）
      │  Fortuny-Amat big-M 线性化
      ▼
MILP → 求解
```

**实验**：IEEE 33 节点馈线，24 小时日前调度，4 类分布式资源聚合为单个 VPP。

## 4. 关键创新点

- **保真**：下层保留完整 AC-OPF，而非线性化近似，形成双层 MINLP；
- **三方利益显式权衡**：DSO 支出、网损、电压偏差 vs VPP 利润 vs 聚合商租金，一次性量化；
- **给出聚合价值的可操作数字**（见下）。

## 5. 与已有方法的区别

大多数 VPP/DSO 协调工作会把下层潮流**线性化**（DistFlow、凸松弛或 MILP 化）以保求解效率，本文坚持保留 AC-OPF 的完整非线性，用 KKT+强对偶+big-M 精确处理，精度与物理保真度更高——代价是 big-M 的紧界与数值稳定性需要处理。

## 6. 为什么值得关注

结论很有说服力：相对"各 VPP 对分时电价独立调度"，聚合调度使**有功网损下降 10.5%、累计电压偏差下降 18.3%、低于 0.95 p.u. 的 bus-hours 从 132 降到 29**，而这些收益只以 **0.31% 的社会成本和 0.26% 的 DSO 支出**为代价，且聚合商租金得以保留。这直接量化了 VPP 聚合在配网侧的边际价值，是"聚合到底值不值"的一手论据。

## 7. 待思考的问题

- big-M 的取值怎么选才既紧又稳？对更大系统是否数值退化？
- 确定性模型如何扩展到随机（多场景）或鲁棒版本？
- 多个竞争 VPP（而非单个聚合商）时的均衡（GNE）如何刻画？
- 统一价格信号 vs 节点/分布 LMP 对协调效率的影响？
