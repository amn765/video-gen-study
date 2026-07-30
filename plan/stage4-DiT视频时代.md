# 阶段 4 · DiT 视频时代（约 7 天）

> 目标：进入当代主线——全时空注意力的视频 DiT。
> 读完这一阶段，你应能独立阅读 2024-2025 年任何一篇开源视频模型论文的结构章节。

主线资料：[Sora 技术报告](https://openai.com/index/video-generation-models-as-world-simulators/)（网页报告）+ 开源模型的 GitHub 文档与论文。配 [AI Coffee Break](https://www.youtube.com/@AICoffeeBreak) 的 Sora 讲解视频。

## 任务清单

### ☐ 4-1 Sora 技术报告精读
- 读 OpenAI 官方报告 [Video generation models as world simulators](https://openai.com/index/video-generation-models-as-world-simulators/)（它是网页不是论文，本身就是很好的讲解材料）；可配 [AI Coffee Break](https://www.youtube.com/@AICoffeeBreak) 的 Sora 解析视频。
- 抓住四个关键词：spacetime patches（时空块统一 token 化）、可变时长/分辨率/宽高比（原生尺寸训练）、语言理解（re-captioning + prompt 改写）、涌现的 3D 一致性。
- 思考题：为什么「原生尺寸训练」被认为是 Sora 构图能力强的关键？（对照裁剪/填充训练的代价）
- 完成标志：能解释 spacetime patch 如何把任意时长/分辨率的视频变成统一的 token 序列。

### ☐ 4-2 开源复现潮：Latte 与 Open-Sora 系列
- 读 [Latte](https://arxiv.org/abs/2401.03048)：比较它给出的四种时空分解变体（先空后时 / 先时后空 / 交错 / 分离注意力），记住结论——分解结构能逼近全注意力的效果且省算力。
- 浏览 [Open-Sora](https://github.com/hpcaitech/Open-Sora) 与 [Open-Sora Plan](https://github.com/PKU-YuanGroup/Open-Sora-Plan) 的仓库文档（比论文更适合建立工程全貌），以及 [Open-Sora 2.0 报告](https://arxiv.org/abs/2503.09642)（20 万美元训出商业级模型的成本拆解）。
- 完成标志：能列出开源社区复现 Sora 时的三个共识与三个分歧点。

### ☐ 4-3 ⭐ MMDiT 与全时空注意力
- 重读 [SD3 / MMDiT](https://arxiv.org/abs/2403.03206)，这次关注 rectified flow 训练目标与 MMDiT 结构的结合——正好接上你图谱里的流匹配。
- 读 [CogVideoX](https://arxiv.org/abs/2408.06072)，配合 [THUDM/CogVideo 仓库](https://github.com/THUDM/CogVideo) 的文档看：3D 全时空注意力 + expert adaLN（文本/图像各自独立的 LN 调制）+ 3D RoPE。
- 填表：

| 模型 | 注意力结构 | 条件注入 | 训练目标 |
|------|-----------|----------|----------|
| CogVideoX | 3D 全注意力 | expert adaLN | diffusion |
| SD3 | 双流 MMDiT | 池化向量 adaLN | rectified flow |
| HunyuanVideo | 双流→单流 | adaLN | flow matching |

- 完成标志：能讲清「双流 → 单流」拼接设计为什么兼顾文本对齐与计算效率。

### ☐ 4-4 HunyuanVideo 与 Wan：开源 SOTA 解剖
- 读 [HunyuanVideo](https://arxiv.org/abs/2412.03603)：MLLM 文本编码器、双流→单流结构、数据过滤与课程训练。
- 读 [Wan2.1](https://arxiv.org/abs/2503.20314)，配合 [Wan-Video/Wan2.1 仓库](https://github.com/Wan-Video/Wan2.1) 文档：重点看它的视频 VAE 设计（时空压缩比、特征缓存机制）与 1.3B 小模型的可复现性。
- 思考题：Wan 的 1.3B 模型能在消费级显卡跑，代价是什么？（VAE 压缩率 vs 细节保留）
- 完成标志：能画出任一开源 SOTA 模型的完整数据流（文本 → 编码器 → DiT → VAE 解码）。

### ☐ 4-5 ⭐ 手写：时空 DiT block 与 3D RoPE
- 先读讲解：[EleutherAI: Rotary Embeddings](https://blog.eleuther.ai/rotary-embeddings/) 建立 RoPE 直觉（理解 1D RoPE 后，3D 扩展只是分轴施加）。
- 在阶段 1 的 DiT block 上扩展：token 序列改为 (T · H/p · W/p) 全时空序列，注意力一次覆盖所有 token。
- 实现简化版 3D RoPE：把旋转角按时间/高度/宽度三个轴分段施加：
  ```python
  # 把 head_dim 切成三段，分别编码 t、h、w
  q_t, q_h, q_w = q.split([d_t, d_h, d_w], dim=-1)
  q = torch.cat([apply_rope(q_t, pos_t), apply_rope(q_h, pos_h), apply_rope(q_w, pos_w)], dim=-1)
  ```
- 计算并打印：16 帧、32x32 潜空间、patch=2 时的序列长度与注意力 FLOPs，直观感受全注意力的代价。
- 完成标志：前向跑通，且能用数字说明为什么长视频需要稀疏/分解注意力。

### ☐ 4-6 动手：跑通一个开源视频模型
- 二选一（按显存）：[CogVideoX-2B diffusers 文档](https://huggingface.co/docs/diffusers/api/pipelines/cogvideox) 或 [Wan2.1 diffusers 文档](https://huggingface.co/docs/diffusers/api/pipelines/wan)。
- 记录：加载的组件清单（VAE / 文本编码器 / transformer / scheduler）、峰值显存、单段视频耗时。
- 完成标志：生成一段 480p 视频，并写 5 行笔记说明每个组件的作用。

### ☐ 4-7 长视频路线初步
- 读 [StreamingT2V](https://arxiv.org/abs/2403.14773)，回顾阶段 3 的 FIFO-Diffusion，对比「训练法」与「免训练法」两条长视频路线。
- 思考题：为什么 DiT 时代的长视频比 U-Net 时代更难？（全注意力显存随长度二次增长 vs 卷积的局部性）
- 完成标志：能列出长视频的三种扩展路线（自回归 chunk / 稀疏注意力 / 分层生成）及各自代表工作。

### ☐ 4-8 自测：口述题
1. spacetime patch 与 ViT patch 的关系是什么？
2. 全时空注意力为什么成为 SOTA 标配，尽管它贵？
3. 双流 MMDiT 里文本 token 被合并/丢弃的时机为什么靠后更好？

## 完成标志
- 手写时空 DiT block 通过验证。
- 本地跑通过一个开源视频模型并能画出其组件图。
- 能独立读懂一篇新视频 DiT 论文的模型结构章节。
