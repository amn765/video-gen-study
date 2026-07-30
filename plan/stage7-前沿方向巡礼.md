# 阶段 7 · 前沿方向巡礼（约 4 天）

> 目标：站到当前研究边界上，在三条最活跃的线里选一条作为后续主攻方向。
> 只读精华、不贪多；每条线都配一个思考题帮你建立判断。

主线资料：三条线的代表论文 + 各项目主页/官方博客（讲解更友好，见各任务）。

## 任务清单

### ☐ 7_1 线一：视频生成即世界模型
- 热身读物：经典互动博文 [World Models](https://worldmodels.github.io/)（Ha & Schmidhuber 2018，世界模型思想的源头，交互 demo 很有趣）。
- 读 DeepMind 的 [Genie 官方博客](https://deepmind.google/discover/blog/genie-generative-interactive-environments/)（比论文轻松），对照 [Genie 论文](https://arxiv.org/abs/2402.15391)（从无标注视频学潜在动作空间）；再读 [UniSim](https://arxiv.org/abs/2310.06114)（用生成模型模拟动作后果）。
- 补充：[NVIDIA Cosmos 开发者页](https://developer.nvidia.com/cosmos)（面向物理 AI 的世界基础模型平台），对照 [Cosmos 论文](https://arxiv.org/abs/2501.03575)。
- 思考题：从「会生成」到「世界模型」还缺什么？（动作条件、状态记忆、反事实推理）
- 完成标志：能说出世界模型与普通视频生成器在训练信号上的本质差异。

### ☐ 7_2 线二：实时与交互式生成
- 读 [GameNGen](https://arxiv.org/abs/2408.14837)（扩散模型实时模拟 DOOM）与 Oasis 项目页 [oasis-model.github.io](https://oasis-model.github.io/)（可玩的 Minecraft 世界模型）。
- 先看 [CausVid 项目主页](https://causvid.github.io/)（效果 demo + 方法图解），再读 [CausVid 论文](https://arxiv.org/abs/2412.07772) 与 [Self Forcing](https://arxiv.org/abs/2506.08009)：因果注意力 + KV cache + 分布匹配蒸馏，把双向教师蒸馏成流式自回归学生。
- 思考题：自回归视频扩散的 train-test gap 指什么？Self Forcing 如何消除它？
- 完成标志：能画出流式视频生成的推理时序图（chunk 逐段生成、KV 复用）。

### ☐ 7_3 ⭐ 线三：RL 后训练与奖励对齐
- 先读讲解：[HF: Illustrating RLHF](https://huggingface.co/blog/rlhf)（RLHF 全流程图解）和 [DDPO 项目主页](https://rl-diffusion.github.io/)（把 RL 用于扩散模型的开山之作，主页有清晰的方法图），建立「扩散 + RL」的直觉。
- 对照选读：[VADER](https://arxiv.org/abs/2407.08737)（把奖励梯度沿采样链回传）与 [T2V-Turbo](https://arxiv.org/abs/2405.18750) 的奖励混合部分。
- 读 [Flow-GRPO](https://arxiv.org/abs/2505.05470) 与 [DanceGRPO](https://arxiv.org/abs/2505.07818)：把 GRPO 搬到 flow matching，用 ODE→SDE 转换制造采样随机性以估计优势。
- 思考题：视频 RLHF 相比图像 RLHF 多出的两个奖励维度是什么？（时间一致性、运动幅度）
- 完成标志：能解释 Flow-GRPO 为什么要把确定性 ODE 采样改成 SDE。

### ☐ 7_4 选一条线深挖
- 从上面三条线中选一条，在 arXiv 上找 2 篇最新论文精读（用 arXiv 检索关键词：video generation + world model / autoregressive / reward）。
- 写半页笔记：该方向尚未解决的三个问题 + 你能切入的一个点。
- 完成标志：笔记完成，并能说出这条线上当前最强的开源工作。

### ☐ 7_5 前沿综述交叉验证
- 在 arXiv 搜 "video generation survey"，挑一篇 2024 年之后、引用量高的综述，用你前七个阶段建立的框架去「反推」它的分类法：哪些内容归入你已掌握的主线，哪些是你的盲区。
- 完成标志：列出一张盲区清单（如音频联合生成、视频编辑、个性化），作为后续扩展学习项。

### ☐ 7_6 自测：全路线回顾口述
合上所有资料，回答：
1. 从 VDM 到 Sora 再到今天的开源 SOTA，主线技术经历了哪三次范式切换？
2. 如果让你现在复现一个 mini Sora，你会选什么 VAE、什么主干、什么训练目标、什么数据配方？
3. 世界模型、实时生成、RL 后训练三条前沿线，各自最可能的下一个突破点是什么？

## 完成标志
- 完成一篇前沿方向深挖笔记。
- 能对全路线做一次 15 分钟的完整脱稿串讲。
