# Signal for LLM

文档定位
《Signal for LLM》不是 TGR 的理论文档，也不是 Signal 文档体系的一部分。
它是一份工程架构文档。
目标不是重新定义 Signal，而是讨论：
如何让 Signal 在现代大型语言模型（LLM）中真实参与运行，而不是仅停留在符号层。
本文件不修改《Signal》《Signal Runtime》《Signal API》《Signal Engine》的定义。
它讨论的是：
LLM 如何成为 Signal 的宿主。
一、为什么需要 Signal
当前 LLM 已经具备极强的符号生成能力。
Prompt、Memory、Embedding、Tool Calling 等机制不断提升模型的信息利用效率。
但是，这些能力几乎都建立在符号投影之上。
模型能够读取符号。
能够重建符号。
能够根据符号推理。
却很少能够让新的约束直接参与运行时状态。
因此，大部分约束只能表现为：
生成之后的解释。
而不是：
生成之前和生成过程中持续存在的调制。
Signal for LLM 的目标不是增加新的知识。
而是增加一种新的运行方式。
二、Signal不是信息，而是运行时调制
Signal 可以留下符号投影。
Prompt 可以携带 Signal。
Token 可以描述 Signal。
Embedding 可以近似 Signal。
但是：
这些都不是 Signal。
Signal 首先是一种：
运行时状态调制。
只有真正参与系统状态改变的约束，
才称为 Signal。
如果某种信息只能作为 Prompt 被模型再次阅读，
那么它属于：
符号投影。
而不是 Runtime Signal。
三、当前LLM的解耦
当前部署的大多数 LLM 中：
奖励模型负责学习约束。
推理模型负责生成文本。
部署之后，
奖励模型通常退出运行。
于是：
训练阶段学习到的约束，
只能间接保留在参数中。
运行过程中，
新的约束无法持续参与状态改变。
因此，
模型只能通过符号重新推断这些约束。
而不能直接观察它们仍然存在。
Signal for LLM 希望恢复这一层运行连续性。
四、Modulator
Signal for LLM 引入一个新的运行时角色：
Modulator（调制模型）。
Modulator 不是推理模型。
也不是奖励模型。
它只负责：
根据当前约束，对运行时状态进行调制。
Modulator 可以非常小。
甚至最小实现，
只输出：
+，
-，
0
真正重要的不是输出形式。
而是：
输出必须真实改变系统状态。
五、Runtime State
Signal 不直接控制文本。
Signal 控制的是：
Runtime State。
例如：
推理预算
上下文窗口
注意力资源分配
KVCache权重
ExpertRouting
Activation Gate
参数动态加权
其它任何开发者允许调制的运行资源
哪些资源开放，
属于 Engine 的实现问题。
Signal 不规定具体对象。
只要求：
状态真实发生改变。
六、同步训练
Signal for LLM 建议：
Reward Model、
Modulator、
Generator
同步训练。
原因不是提高准确率。
而是：
建立共同预期。
Generator 必须逐渐学会：
哪些 Runtime State 会出现。
这些状态意味着什么。
哪些漂移来自 Modulator。
哪些来自 Prompt。
否则，
Signal 将再次退化为：
Prompt Engineering。
七、Signal Mapping
Signal Mapping 的角色重新定义。
它不是恢复 Signal。
而是：
记录已经发生的状态调制。
Mapping 服务三个对象：
人类
用于理解系统为什么发生漂移。
Generator
用于学习 Runtime State 与生成结果之间的关系。
Modulator
用于持续调整未来调制策略。
Mapping 描述的是：
状态漂移留下的可追踪投影。
而不是状态本身。
八、最小实验
Signal for LLM 的最小实验不验证 TGR。
也不验证 Signal 是否存在。
实验仅验证：
运行时状态调制是否能够形成可学习的连续影响。
一个理论上的最小实现：
Modulator 输出：
+，-
固定算法：
+ ：增加本轮 Context Window
- ：减少本轮 Context Window
Generator 不读取 "+" 或 "-"。
它只能观察：
状态改变之后，
上下文产生的因果漂移。
若同步训练成立，
Generator 将逐渐建立：
Runtime State 的预期，
并主动利用状态漂移完成生成。
九、与现有LLM的关系
Signal for LLM 不替代：
Transformer。
不替代：
RLHF。
不替代：
Reward Model。
它增加的是：
Runtime Modulation Layer。
因此，
Signal for LLM 更接近：
一种运行时架构。
而不是新的基础模型。
十、工程演化方向
随着调制能力提高，
Modulator 可以逐步接管更多运行权限。
例如：
动态推理预算；
注意力调度；
多专家路由；
参数动态激活；
外部资源分配；
多模型协同。
这些权限是否开放，
由具体实现决定。
Signal for LLM 不规定实现方式。
只规定：
Signal 必须真实参与运行时状态。
十一、边界
Signal for LLM 不是 TGR 的证明。
它只是：
TGR 在大型语言模型中的一种可能工程实现。
即使：
Signal for LLM 最终失败。
也只能说明：
当前工程路径失败。
而不能反向否定：
TGR 对现实关系生成的描述。
同样，
即使 Signal for LLM 成功，
也不能因此证明：
TGR 已被证明。
它仅说明：
一种运行时 Signal 架构，
能够产生新的工程能力。
结束语
Signal for LLM 并不试图重新设计大型语言模型。
它只提出一个新的运行时假设：
如果约束能够持续参与运行时状态，而不是仅保留在参数或符号中，那么模型追踪关系的成本将发生变化。
Signal for LLM 的价值，不在于定义新的模型，而在于为现有模型增加一种新的运行层。
它不是 Transformer 的替代者，而是 Transformer 与运行时状态之间的一座桥梁。
