# 阶段 6 · 采样加速与评测（约 4 天）

> 目标：解决视频生成落地的两大障碍——太慢、好坏没法量化。
> 你图谱里的 rectified flow 是少步采样的理论一极，这一阶段补齐工程全谱系。

主线资料（讲解型）：[Lilian Weng: What are Diffusion Models?](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/)（加速采样章节）+ [VBench 项目主页](https://vchitect.github.io/VBench-project/)。原论文作对照。

## 任务清单

### ☐ 6-1 蒸馏路线一：一致性模型家族
- 先读讲解：[Lilian Weng 的扩散长文](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/) 中 fast sampling 相关章节（DDIM、progressive distillation、consistency 都在其中，串讲比单篇论文好吸收）；再看 [OpenAI Consistency Models 页面](https://openai.com/index/consistency-models/) 的直观介绍。
- 对照选读：[Consistency Models](https://arxiv.org/abs/2303.01469) 与 [Progressive Distillation](https://arxiv.org/abs/2202.00512)：一个让 ODE 轨迹上各点自洽，一个逐步把采样步数折半；对照你学过的 rectified flow 拉直路径思路。
- 读 [AnimateLCM](https://arxiv.org/abs/2402.00769)：一致性蒸馏如何落到视频上。
- 思考题：rectified flow 的「拉直」与 consistency 的「自洽」分别在改什么？（路径形状 vs 解算器映射）
- 完成标志：能用三张示意图区分三种少步方案的目标。

### ☐ 6-2 蒸馏路线二：分布匹配 DMD
- 读 [DMD](https://arxiv.org/abs/2311.18828) 与 [DMD2](https://arxiv.org/abs/2405.14867)：用两个分数网络估计 KL 梯度做单步生成，对抗损失稳定训练（这两篇图例做得好，直接读原文效率最高）。
- 读 [T2V-Turbo](https://arxiv.org/abs/2405.18750)：把奖励模型反馈混进一致性蒸馏，学生质量反超教师。
- 思考题：DMD 为什么天然适合接 RL/奖励微调？（一步生成使梯度可回传到奖励端）
- 完成标志：能解释 DMD 里「假分数网络」扮演的角色。

### ☐ 6-3 ⭐ 免训练加速：特征缓存
- 读 [DeepCache](https://arxiv.org/abs/2312.00858)、[PAB](https://arxiv.org/abs/2408.12588)（按注意力类型定不同缓存周期）、[TeaCache](https://arxiv.org/abs/2411.19108)（用 timestep embedding 差异决定是否重算）。
- 动手：给阶段 4 跑通的 diffusers pipeline 接上 TeaCache（参考 [TeaCache 官方仓库](https://github.com/ali-vilab/TeaCache) 的 diffusers 集成说明），对比开/关缓存的耗时与画质。
- 完成标志：产出一份「加速比 vs 画质损失」实测记录。

### ☐ 6-4 系统级加速概览
- 概览三类手段（只读概览不深挖）：CFG 蒸馏（条件/无条件两次前向合并为一次）、序列并行（参考 [xDiT 仓库](https://github.com/xdit-project/xDiT) 的多卡方案）、稀疏注意力（滑窗/块状，长视频必需）。
- 认识一个为效率而生的模型家族：[LTX-Video](https://arxiv.org/abs/2501.00103)（高压缩 VAE + 实时生成）。
- 完成标志：能为「手机端 5 秒视频生成」的假想需求挑一套加速组合拳并说明理由。

### ☐ 6-5 ⭐ 评测：FVD 与 VBench
- 先看 [VBench 项目主页](https://vchitect.github.io/VBench-project/)（16 个维度各有图示和 leaderboard，比论文直观），再对照读 [VBench 论文](https://arxiv.org/abs/2311.17982)。
- 读 [FVD 原始论文](https://arxiv.org/abs/1812.01717)：用 I3D 特征的 Fréchet 距离评视频；理解它对时长、分辨率、人类偏好的盲区。
- 动手：把阶段 4 或 5 生成的 5 段视频，用 [VBench 开源代码](https://github.com/Vchitect/VBench) 跑其中 3 个维度。
- 完成标志：有实测分数，并能说清 FVD 与 VBench 结论打架时该信谁、为什么。

### ☐ 6-6 自测：口述题
1. 一致性蒸馏与 DMD 的单步生成机制差异是什么？
2. TeaCache 为什么「免训练」也不明显掉画质？
3. 为什么单一标量指标无法评价视频生成？

## 完成标志
- 完成一次缓存加速实测并留存数据。
- 完成一次 VBench 维度评测并留存报告。
