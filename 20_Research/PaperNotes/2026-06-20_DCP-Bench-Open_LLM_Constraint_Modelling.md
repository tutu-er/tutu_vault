# DCP-Bench-Open: Evaluating LLMs for Constraint Modelling of Discrete Combinatorial Problems

> 📅 创建日期: 2026-06-20
> 🏷️ 标签: #LLM #ConstraintProgramming #Benchmark #推理时计算 #CombinatorialOptimization
> ⭐ 价值评分: 8/10
> 📚 来源: JAIR, arXiv:2506.06052v3, https://github.com/DCP-Bench/DCP-Bench-Open
> 👤 作者: Kostis Michailidis (KU Leuven), Dimos Tsouros, Tias Guns

---

## 📌 解决了什么问题

用 LLM 把自然语言描述的离散组合优化问题（如 TSP、调度、图着色）自动翻译成可执行的约束模型（MiniZinc / CPMpy / OR-Tools），并通过**系统化的 prompt 工程 + 推理时计算**把零样本准确率从 ~60% 推到 ~91%。

核心挑战：
- 约束建模是**声明式**而非命令式——约束必须同时成立，而非逐步执行
- 同一问题可有多**种变量视角（viewpoint）**，无法用传统的变量映射做评估
- 缺乏足够多样、足够复杂的 benchmark（之前只有 NL4Opt、LGPs 这种小规模同质化数据集）

---

## 🧠 核心方法（方法论解读）

### 整体思路

```
用户自然语言描述问题
    ↓
System Prompt（三层渐进：Basic → Guidelines → Documentation）
    ↓  （可选：RAICL 检索相似示例注入 prompt）
LLM 生成约束模型代码
    ↓
执行模型 → 求解器
    ↓
解 或 错误 traceback
    ↓  （可选：Self-Verification 自验证循环）
最终输出正确解
```

整体管道形式化：**exec(LLM(p, τ)) = a⁺**

### 核心组件拆解

#### 1. System Prompt 三层渐进设计

| 层级 | 内容 | 模拟场景 |
|------|------|---------|
| **Level 1 Basic** | 身份设定 + 输出格式（JSON 模板） | 聊天框随口说 |
| **Level 2 Guidelines** | + 建模步骤指导 + 课堂级建议 + 代码模板 | 给较详细的通用指导 |
| **Level 3 Documentation** | + 框架 API 单行文档（全部可用类/函数） | 附上库的 cheatsheet |

设计精妙：形成了自然的**消融实验梯度**，能分离「LLM 缺乏建模知识」vs「缺乏框架语法知识」两个瓶颈。

#### 2. RAICL（检索增强上下文学习）

$$p = sys \oplus (i_1, o_1), ..., (i_8, o_8) \oplus c$$

- 用 token embedding 语义相似度检索 8 个最相似示例
- Leave-one-out 策略：测试某问题时用剩余 100 个作检索库

> ⚠️ **反直觉结果**：RAICL 在详细文档 prompt 下反而降低性能。LLM 更擅长从结构化文档推理，而非从示例泛化。

#### 3. Repeated Sampling + Solution Majority Voting

```
Algorithm:
1. τ=0.8 高随机性 → 生成 k=10 个候选模型
2. 分别求解 → 收集解集合 S
3. 找到最高频解 s_maj
4. 返回第一个产生 s_maj 的模型
5. 全无解 → fallback 到第一个模型
```

**设计精妙之处**：不判断「模型结构是否正确」（这对约束建模极难），而是通过**解的一致性**反向筛选——不同模型可能产生同一正确解，投票即可。

#### 4. Self-Verification 自验证循环

三个评判维度（完整的正确性分解）：

| 维度 | 检查内容 | 失败典型 |
|------|---------|---------|
| **Runtime Integrity** | 代码跑通、API 正确 | 语法错误、import 缺失 |
| **Model Accuracy** | 变量/约束/目标正确定义 | 错误解、unsatisfiable |
| **Solution Output** | 输出格式符合要求 | JSON key 错误 |

迭代输入：验证 prompt ⊕ 系统 prompt ⊕ 问题描述 ⊕ 上一轮代码 ⊕ 上一轮执行结果（解或 traceback）

- LLM 判断 `[[OK]]` → 停止
- LLM 判断 `[[FIXED]]` → 用修正代码下一轮，最多 10 轮

**精妙之处**：不同于通用代码自修复（只看 traceback），这里把**原始问题**、**模型代码**、**求解器输出**三者都喂回去，让 LLM 能交叉验证。

---

## 🔬 实验设计

### 5 个递进式研究问题

```
Q1: 哪个建模框架最好？     → 变量：MiniZinc vs CPMpy vs OR-Tools
Q2: 什么类型错误最常见？   → 变量：prompt 层级 × 错误分类
Q3: 推理时计算方法影响？   → 变量：RAICL/Reasoning/Sampling/SV/组合
Q4: 对隐藏实例泛化如何？   → 变量：单实例 vs 多实例
Q5: 全组合验证           → 最优配置 × 全部框架 × 多实例
```

### 数据集：DCP-Bench-Open

- **164 个**离散组合问题，来自 5 个来源（CSPLib、CPMpy examples、Håkan K.、课程习题、ComplexOR）
- 约束数量 1~2463，变量数 2~716，**349 种不同约束关系**
- 23 个问题含多数据实例（共 167 个 hidden instances）
- Ground-truth 为 CPMpy 模型，但评估**框架无关**（只要输出正确解即可）

### 三种评估指标

```
SIA (Single Instance Accuracy)    = 在默认实例上正确 → 乐观上界
MIA (Multiple Instance Accuracy)  = 在所有实例上都正确（∧）→ 严格指标
AIA (Averaged Instance Accuracy)  = 先算每问题的实例平均再跨问题平均 → 平衡
```

**为什么不用 model-level / constraint-level 评估？**
同一问题可以有多种**不同变量视角**（如 TSP 可用 successor 变量或 circuit 约束），无法做变量映射。Solution-level 评估规避了这个问题。

### 7 个 LLM

gpt-5.1, gpt-oss-120B, DeepSeek-V3.2, Qwen3-Coder-480B, Qwen3-235B, Kimi K2 Instruct, Cogito v2.1 671B

### 实验控制

- Seed=42, τ=0 确定性（采样时 τ=0.8）, token limit=12k
- 执行超时 10s（小实例），最终实验放宽到 30min
- Ubuntu 24.04, 32GB RAM, Intel Core Ultra 7 165H

---

## 📊 关键结果

### Q1: 框架对比

| 框架 | 类型 | 零样本最高准确率 |
|------|------|:--:|
| MiniZinc | 领域特定语言 | 57.3% |
| CPMpy | 高层 Python | 70.1% |
| OR-Tools | 底层 Python API | 75.0% |

→ Python 框架显著优于领域特定语言；CPMpy 文档级 prompt 下所有 LLM 一致性最好（>60%）

### Q2: 错误类型分析

随 prompt 层级提高：**可检测错误（语法/API）↓，建模错误（逻辑）↑**
→ 更多信息让 LLM 写出能跑的代码，但逻辑正确性仍是大挑战

Self-Verification 大幅减少可检测错误（如 gpt-5.1: 19→5），但对建模错误效果因模型而异。

### Q3: 推理时计算方法

| 方法 | 效果 |
|------|------|
| RAICL | ❌ 意外无效，甚至降低性能 |
| Reasoning | ⚠️ 效果不稳定，因模型而异 |
| Repeated Sampling | ✅ 所有模型提升 >10% |
| Self-Verification | ✅ 与 Sampling 相当 |
| **Sampling + SV 组合** | 🏆 **最高 91%（gpt-5.1 + CPMpy）** |

### Q4: 多实例泛化

SIA → MIA 普遍下降 10-30%：LLM 容易**过拟合默认实例**（硬编码数组大小、假设特定值域）

### Q5: 最终全组合

MiniZinc 提升最猛：基线 37.8% → 组合方法 **73.2%**（+35.4%）

---

## 💡 为什么值得关注

1. **方法论极其干净**：5 个 Q 递进逻辑清晰、每个组件有独立消融对比、评估指标设计考虑了约束建模的特性
2. **RAICL 翻车的重要教训**：在详细文档 prompt 下，从示例泛化不如从结构化文档推理——这对 LLM+formal 建模领域有启示
3. **Solution Majority Voting** 是一个聪明的 hack：通过解一致性绕过变量映射难题
4. **LLM 过拟合到默认实例**的发现很重要：MIA 比 SIA 低 10-30%，说明当前 LLM 在「学习约束」vs「记忆数据」之间的 gap 还不小
5. **与我们的研究关联**：如果我们要做 LLM + 电力系统优化（如 DRL/OPF），这篇的 prompt 设计范式、推理时计算方法都值得借鉴

## ❓ 待思考的问题

- [ ] RAICL 失效可能和检索策略有关？如果改用**结构相似度**（同类型约束、同规模问题）而非语义相似度呢？
- [ ] Self-Verification 用同一个 LLM 做生成和验证——是否存在「自己查不出自己的错」的盲区？
- [ ] 如何在电力系统优化场景（OPF、UC）构建类似的 Benchmark？约束类型差异大吗？
- [ ] Sampling + SV 的算力成本（10× 生成 + 求解 + 10 轮验证）对实际应用是否可行？
- [ ] Multi-instance 泛化问题能否通过「多实例感知 prompt」解决？

---

**最后更新**: 2026-06-20
