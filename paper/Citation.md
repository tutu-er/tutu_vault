
*[IEEE TRANSACTIONS ON SMART GRID, VOL. 11, NO. 5, SEPTEMBER 2020  Topology Identification and Line Parameter Estimation for Non-PMU Distribution Network:  A Numerical Method]
	针对中高压网，需要完整的智能电表量测，使用潮流迭代方法，在LVDG末端设备中没有完整的

*[Learning with End-Users in Distribution Grids: Topology and Parameter Estimation]
	隐藏节点辨识，使用相关性的标准方法

提供了一组算例

*[Low-Voltage Distribution Grid Topology Identification With Latent Tree Model]*
	使用EM和BIC网络

提供了一组算例

*[An Improved Algorithm for Topology Identification … and Its Application for Fault Detection]*
	 传统方法，递归分组，需要电流
	
提供电表数据
![[Pasted image 20261111213210.png|401]]

*[Topology  identification of radial distribution networks using smart meter data]*
	传统RG方法，标准灵敏度分析，
*[Topology Identification of Distribution Networks With Partial Smart Meter Coverage]*
	同作者论文，使用了部分电表数据，做低可观性部分分析


*[Learning Distribution Grid Topologies: A Tutorial]*
	大型综述文章，综合了各种方法，包括LVDG

*[Identifying Topology of Low Voltage Distribution Networks Based on Smart Meter Data]*
	使用电量量测和相关性分析，逐层恢复拓扑连接

提供标准算例

*[Graph Algorithms for Topology Identification Using Power Grid Probing]*
	Probing 系列，使用主动逆变器进行扰动，增强数据获取；分析了使用部分数据和含噪声情形下的拓扑恢复工作

*[TOPOLOGY ESTIMATION IN LOW VOLTAGE GRIDS USING  WALLBOX CHARGING DATA RECORDINGS]*
	已有利用EV进行工程化实践的研究

*[Topology and Parameter Identification of Three-Phase Low-Voltage Distribution Network and Voltage Control Strategy Based on User Smart Meter Measurements: A Data-Driven Approach]*
	使用了三相数据，增加的相序辨识的工作

[An Optimization-Based Topology Error Detection Method for Power System State]
	高压网算例，利用二元变量建模线路拓扑错误

*[[Topology Identification in Distribution Systems Using Line Current Sensors: An MILP Approach]]*
	相似方法，为每个潜在开关创建二元变量，通过线路KCL关系恢复拓扑

*[Joint Topology Identification and State Estimation in Unobservable]*
	使用优化方法和稀疏表示，在稀疏信息下完成开关状态的辨识

*[Topology and Parameter Identification of Distribution Network Using Smart Meter and µPMU Measurements]*
	使用多种量测设备进行量测，并恢复相角数据，进一步讨论了uPMU的最优选址

*[Bayesian Error-in-Variables Models for the Identification of Power Networks]*
	利用贝叶斯框架和uPMU量测等，利用在备选拓扑上的MLE，实现了考虑双边误差的拓扑辨识

*[PaToPaEM: A Data-Driven Parameter and Topology Joint Estimation Framework for Time Varying System in Distribution Grids]*
	使用EM算法估计运行状态，讨论潜在的多个候选拓扑

*[Distributed Energy Resources Topology Identification via Graphical Modeling]*
	使用电压概率图模型，并利用互信息进行树生成

*[Graphical Models in Meshed Distribution Grids: Topology Estimation, Change Detection & Limitations]*
	利用协方差矩阵作为主要的辨识依据，并使用二次图结构特点进行辨识

*[Guaranteed Phase & Topology Identification in Three Phase Distribution Grids]*
	从三相线性模型出发，以多相电压统计联合识别相别和拓扑，给出判别条件

*[Simultaneous Robust State Estimation, Topology Error Processing, and Outage Detection for Unbalanced Distribution Systems]*
	将配电网的开关状态作为二进制决策变量，使用AC模型的线性近似，同时完成拓扑识别、状态估计和停电区域检测

*[A data driven approach to distribution network topology identification]*
	早期LVDG latent 节点辨识工作，使用电压灵敏度矩阵，使用Purfer序列逐步恢复拓扑

*[Adaptive optimal-weight method for topology identification and line parameter estimation of large-scale low-voltage distribution grids]*
	近期隐藏节点工作，耦合了大量启发式算法的工作

*[Topology Identification in Distribution Networks using Harmonic Synchrophasor Measurements]*
	使用谐波信息进行开关状态辨识

*[Signal Injection-Based Topology Identification for Low-Voltage Distribution Networks Considering Missing Data]*
	完善了传统中使用特征电流进行拓扑辨识的方法

*[Error Bounds for Radial Network Topology Learning From Quantized Measurements]*
	估计了噪声在配电网拓扑辨识和参数拟合中的影响

*[Distribution Network Topology Identification Using Asynchronous Transformer Monitoring Data]*
	也是辨识了开关状态，使用了整数变量松弛代表未测相角

*[Full-Scale Distribution System Topology Identification Using Markov Random Field]*


*[Power Distribution Network Topology Detection Using Dual-Graph Structure Graph Neural Network Model]*
	双图GNN方法，利用数据训练得到当前拓扑状态，有一定泛化性，但受限于已知线路

*[Adaptive Physics-Informed Data-Driven Topology Identification for Distribution Networks]*



*[Network Tomography Based on Additive Metrics]*
	NJ方法来源

*[Learning Latent Tree Graphical Models]*
	RG方法来源，需要与前面使用这两种方法的LVDG辨识一块说

[Show Me the Money! A Field Experiment on Electric Vehicle Charge Timing](https://doi.org/10.1257/pol.20230653)