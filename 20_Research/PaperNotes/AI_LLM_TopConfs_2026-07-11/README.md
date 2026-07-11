# 近几年 AI / LLM 顶会优质论文解读索引

> 创建日期: 2026-07-11  
> 范围: 主要覆盖 AI、神经网络、LLM、对齐、推理、工具使用、参数高效微调、长上下文与模型安全方向；刻意避开 CV 主线论文。  
> 说明: “顶会”以 NeurIPS、ICML、ICLR、ACL 系列和 COLM 等机器学习 / 语言模型主流会议为主；少数论文的影响力强于会议标签，已在单篇笔记中说明。

## 阅读路线

### 1. 大模型训练与规模律

- [[2026-07-11_InstructGPT_RLHF|InstructGPT / RLHF: Training language models to follow instructions with human feedback]]
- [[2026-07-11_Chinchilla_Compute_Optimal|Chinchilla: Training Compute-Optimal Large Language Models]]

### 2. 推理能力与上下文学习

- [[2026-07-11_Chain_of_Thought|Chain-of-Thought Prompting Elicits Reasoning in Large Language Models]]
- [[2026-07-11_Self_Consistency|Self-Consistency Improves Chain of Thought Reasoning in Language Models]]
- [[2026-07-11_What_Is_ICL|What learning algorithm is in-context learning?]]

### 3. 工具调用、行动与 Agent 雏形

- [[2026-07-11_ReAct|ReAct: Synergizing Reasoning and Acting in Language Models]]
- [[2026-07-11_Toolformer|Toolformer: Language Models Can Teach Themselves to Use Tools]]

### 4. 微调与对齐

- [[2026-07-11_LoRA|LoRA: Low-Rank Adaptation of Large Language Models]]
- [[2026-07-11_QLoRA|QLoRA: Efficient Finetuning of Quantized LLMs]]
- [[2026-07-11_DPO|Direct Preference Optimization]]

### 5. 架构与效率

- [[2026-07-11_FlashAttention|FlashAttention: Fast and Memory-Efficient Exact Attention]]
- [[2026-07-11_Hyena|Hyena Hierarchy: Towards Larger Convolutional Language Models]]
- [[2026-07-11_Transformers_Are_SSMs|Transformers are SSMs / Mamba-2]]

### 6. 理解、评估与安全

- [[2026-07-11_Emergent_Abilities_Mirage|Are Emergent Abilities of Large Language Models a Mirage?]]
- [[2026-07-11_Stealing_Production_LM|Stealing Part of a Production Language Model]]

## 总体脉络

这批论文可以连成一条线：先是 Chinchilla 重新校准“参数量、数据量、算力”的关系，InstructGPT 把预训练模型变成更符合人类意图的助手；随后 CoT、Self-Consistency、ICL 理论解释了“上下文里为什么能推理/学习”；ReAct 和 Toolformer 把语言模型从纯文本生成推向工具调用与行动；LoRA、QLoRA、DPO 则解决了低成本定制和偏好对齐；FlashAttention、Hyena、Mamba-2 代表底层算子与架构效率路线；Emergent Mirage 和 model stealing 则提醒我们，能力评估和安全边界本身也是核心研究问题。

