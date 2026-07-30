# 阶段 2 · 条件注入与引导（约 3 天）

> 目标：解决「怎么让生成模型听话」——文本条件从哪来、怎么注入、怎么用引导强度换多样性。
> 这是从文生图跨到文生视频前最后一块通用积木。

主线资料（讲解型）：[Sander Dieleman: Guidance: a cheat code for diffusion models](https://benanne.github.io/2022/05/26/guidance.html) + [Hugging Face: Stable Diffusion with Diffusers](https://huggingface.co/blog/stable_diffusion)。原论文作对照。

## 任务清单

### ☐ 2-1 文本编码器：CLIP 与 T5 的分工
- 先读讲解：[OpenAI CLIP 介绍页](https://openai.com/index/clip/)（图文并茂），再看 [The Illustrated Stable Diffusion](https://jalammar.github.io/illustrated-stable-diffusion/) 里文本编码器在整条链路中的位置。
- 对照选读：[CLIP 原论文](https://arxiv.org/abs/2103.00020) 的对比学习目标一节；[Imagen](https://arxiv.org/abs/2205.11487) 第 2 节——理解它为什么选冻结的 [T5](https://arxiv.org/abs/1910.10683) 而不是 CLIP。
- 记住这个经验结论：文生图/文生视频里，文本理解能力往往比视觉-文本对齐更稀缺，所以后来的模型（如 HunyuanVideo）直接用大语言模型做文本编码器。
- 完成标志：能对比 CLIP embedding 与 T5/LLM embedding 在生成任务中的差异（语义对齐 vs 语言理解）。

### ☐ 2-2 ⭐ 引导技术：从 classifier guidance 到 CFG
- 先读讲解：[Guidance: a cheat code for diffusion models](https://benanne.github.io/2022/05/26/guidance.html)（Sander Dieleman 的经典博客，用分数视角把 classifier guidance 和 CFG 统一讲透，比原论文好懂）。
- 对照选读：[Diffusion Models Beat GANs](https://arxiv.org/abs/2105.05233) 的 guidance 推导、[Classifier-Free Diffusion Guidance](https://arxiv.org/abs/2207.12598) 原论文。
- 默写 CFG 采样公式：eps_cfg = eps_uncond + w · (eps_cond - eps_uncond)，并解释 w=1 和 w 很大时的极限行为。
- 思考题：为什么过大的 guidance scale 会导致过饱和与伪影？（提示：采样轨迹被拉离数据流形）
- 完成标志：能从分数视角讲清 CFG 与 classifier guidance 的等价关系。

### ☐ 2-3 动手：用 diffusers 玩透 CFG
- 按官方教程 [Stable Diffusion with Diffusers](https://huggingface.co/blog/stable_diffusion) 跑通管线（组件讲得比 Quicktour 细）；需要速查时用 [Diffusers Quicktour](https://huggingface.co/docs/diffusers/quicktour)。
- 实验矩阵：同一 prompt + 同一 seed，guidance_scale 取 1 / 3 / 7.5 / 15 各生成一张，拼成 2x2 对比图。
- 完成标志：产出对比图 + 三句话总结 w 增大时画面发生了什么。

### ☐ 2-4 条件注入机制总览
- 回看阶段 1 的 adaLN；想做点巩固练习可以看 [HF Diffusion Course](https://huggingface.co/learn/diffusion-course) 的 guidance 单元。
- 对照选读：[SD3 / MMDiT 论文](https://arxiv.org/abs/2403.03206) 第 2 节：文本 token 与图像 token 各自一套权重、在注意力里拼接的「双流」设计。
- 填表：

| 机制 | 条件形态 | 代表模型 |
|------|----------|----------|
| cross-attention | 文本序列 | SD 1.5 / SDXL |
| adaLN | 全局向量 | DiT |
| MMDiT 双流 | 文本 token 全程参与 | SD3 / HunyuanVideo |

- 思考题：MMDiT 相比 cross-attention，文本信息在网络的哪一刻「离开」？这个时机为什么重要？
- 完成标志：能说出三种机制各自的带宽瓶颈。

### ☐ 2-5 自测：口述题
1. CFG 训练中为什么要随机丢弃条件？
2. 为什么文生视频模型普遍从 CLIP 转向更大的文本编码器？
3. guidance scale 与采样步数之间存在什么权衡？

## 完成标志
- CFG 对比实验图完成，采样公式能默写。
- 三种条件注入机制能各自画出示意图。
