# 隐私保护下的电网与AI数据中心协同运行：Checkpoint感知的三阶段方案

- 论文：Privacy-Preserving Coordinated Operation of Power Grids and AI Data Centers: A Checkpoint-Aware Three-Phase Scheme
- 作者：Ziang Liu, Ruizhang Yang, Xin Cui, Francis Yunhe Hou
- 来源：arXiv:2609.26365（2026-09-22）
- 链接：https://arxiv.org/abs/2609.26365

## 1. 解决了什么问题

大模型训练/推理让 AI 数据中心（AIDC）迈向吉瓦级负荷。AIDC 靠 DVFS 有巨大运行灵活性，但周期性模型 checkpoint 会带来负荷骤降/骤升，侵蚀运行备用、加剧线路阻塞。而电网与 AIDC 双方都不愿共享各自的专有数据与决策权，直接协同很困难。本文要在"双方都保密"的前提下实现电网与 AIDC 的协调运行。

## 2. 核心方法解读（师兄口吻）

一句话：让电网和 AIDC 各算各的，只交换"接口信息"，但能保证全局可行。

具体是这样：电网先自己算一个"认证过的安全域内近似"——相当于给 AIDC 划一块"你在里面随便折腾都安全"的区域；AIDC 拿到这块区域后，在里面优化自己训练/推理任务的分配，产出功率计划和 checkpoint 告警；电网再拿着告警做一个"考虑 checkpoint 不确定性"的两阶段鲁棒 OPF。三步只交换紧凑的接口信息，谁也不用把底牌交给对方。

## 3. 算法/模型框架

三阶段分层架构：

- Phase I：电网算子计算 AIDC 安全域的 **认证内近似（certified inner approximation）**，作为后续协同的约束域；
- Phase II：AIDC 算子在认证安全域内优化训练/推理负载分配，生成功率计划与 checkpoint 告警；
- Phase III：电网算子求解 **checkpoint 感知的两阶段鲁棒 OPF**，同时考虑新能源出力与 checkpoint 双重不确定性。

验证场景：改进的 IEEE 14 节点系统 + 改进的 NYISO 系统。

## 4. 关键创新点

1. 首次把 AIDC 特有的 checkpoint 骤降/骤升行为显式建模进电网调度；
2. "认证安全域内近似"为协同的可行性提供了数学保证；
3. 隐私保护：双方仅交换紧凑接口信息，避免频繁迭代通信与数据泄露。

## 5. 与已有方法的区别

传统电网-数据中心协同要么假设全量共享数据（中心化，现实中不可行），要么干脆忽略 checkpoint 这种 AIDC 特有动态。本文用"安全域内近似 + 两阶段鲁棒 OPF"把双方解耦，既有隐私又有可行性保证，且不依赖频繁交互。

## 6. 为什么值得关注

把"AI 负载灵活性"当作一种可调度的电网资源，是当下最热的方向之一。checkpoint 这个建模视角很新，直接命中了鲁棒/期权类调度的痛点，跟我做的鲁棒优化 + 能源-AI 交叉高度契合。

## 7. 待思考的问题

- 认证内近似的保守性有多大？会不会牺牲 AIDC 太多灵活性？
- 两阶段鲁棒 OPF 的不确定集如何从 checkpoint 行为精确刻画？
- 能否扩展到含储能 AIDC、以及参与电力市场报价的 AIDC？
