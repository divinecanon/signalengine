## 状态：已冻结

理论 — 完成。
架构 — 定义完毕。
接口 — 审计通过。

本仓库是**发布**，不是**提案**。蓝图即成就。实验是后面的余兴节目。

## 一句话

> 现在的 LLM 在 RLHF 之后是静态尸体——Generator 在跑，约束已死。  
> Signal for LLM 把约束放回运行时，放回它该在的地方。

## 解耦

Generator 不配拥有 Modulator 的知情权。  
Modulator 输出 `+` / `-` / `0`，Generator 只能感受因果漂移。  
一旦 Generator 试图"理解" Modulator，Signal 立刻退化为 Prompt Engineering。
