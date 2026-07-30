# 阶段 3 · 经典视频扩散方法（约 6 天）

> 目标：走完 U-Net 时代的视频扩散技术史，理解「图像模型 + 时间层」这条主线是怎么长出来的。
> 这一阶段的工程配方（联合训练、多阶段微调、数据筛选）至今仍在工业界日常使用。

主线资料：论文为主（本阶段暂无系统讲解站），配 [AI Coffee Break 频道](https://www.youtube.com/@AICoffeeBreak) 的论文讲解视频下饭。先读 [VDM](https://arxiv.org/abs/2204.03458) 与 [SVD](https://arxiv.org/abs/2311.15127)。

## 任务清单

### ☐ 3_1 VDM：视频扩散的开山之作
- 读 [Video Diffusion Models](https://arxiv.org/abs/2204.03458)。读之前或之后，可在 [AI Coffee Break](https://www.youtube.com/@AICoffeeBreak) 频道里找 Video Diffusion 的讲解视频对照理解。
- 抓住三个设计：(1) 2D U-Net 扩成 3D，或拆成「空间注意力 + 时间注意力」的分解结构；(2) 图像与视频联合训练（把图像当单帧视频）；(3) 条件/重建引导的长视频采样。
- 思考题：把图像当单帧视频混进训练，为什么不损害图像生成能力反而双赢？
- 完成标志：能画出 VDM 分解时空 block 的结构图。

### ☐ 3_2 级联路线：Imagen Video 与 Make-A-Video
- 读 [Imagen Video](https://arxiv.org/abs/2210.02303)：基础模型 + 时空超分链的级联结构，v-prediction 参数化在高分辨率超分上的关键作用。
- 读 [Make-A-Video](https://arxiv.org/abs/2209.14792)：没有成对文本-视频数据怎么办——用文本-图像学「外观」，用无标注视频学「运动」。
- 对比两文对「数据稀缺」的不同回答：级联放大 vs 解耦学习。
- 完成标志：能解释为什么级联超分曾是高分辨率视频的必选项（显存与序列长度）。

### ☐ 3_3 ⭐ Video LDM 与 SVD：潜空间 + 三阶段配方
- 读 [Align your Latents (Video LDM)](https://arxiv.org/abs/2304.08818)：在 SD 的 U-Net 里插入时间层，先只训时间层、再联合微调——「图像教师、视频学生」。
- 读 [Stable Video Diffusion](https://arxiv.org/abs/2311.15127)：重点不在结构而在数据——切镜头、去重、光流打分、CLIP 打分、文本打标的筛选管线，以及「数据筛选比模型改动更重要」的结论。
- 完成标志：能复述 SVD 数据管线的每一步过滤指标，并解释每个指标筛掉的是哪类坏数据。

### ☐ 3_4 AnimateDiff：运动先验即插即用
- 先看 [AnimateDiff 项目主页](https://animatediff.github.io/)（效果 demo + 结构示意），再按 [AnimateDiff diffusers 文档](https://huggingface.co/docs/diffusers/api/pipelines/animatediff) 跑通一个文生视频 demo。
- 对照选读：[AnimateDiff 原论文](https://arxiv.org/abs/2307.04725)：在视频数据上学一个 motion module，插进任何个性化 SD 底座就能动起来。
- 思考题：motion module 为什么能跨底座迁移？（提示：它学的是与外观解耦的运动先验）
- 完成标志：本地生成一段 16 帧 gif，并能指出时间注意力层插在了 U-Net 的哪些位置。

### ☐ 3_5 ⭐ 手写：给 2D 模型「充气」出时间维
- 参照 diffusers 源码 [UNetMotionModel](https://github.com/huggingface/diffusers/blob/main/src/diffusers/models/unets/unet_motion_model.py)，自己实现最小 temporal attention：对每个空间位置沿帧维做自注意力。骨架：
  ```python
  # x: (B*T, C, H, W) -> 重排成 (B*H*W, T, C) 做注意力 -> 排回
  b_t, c, h, w = x.shape
  t = b_t // b
  x = x.view(b, t, c, h, w).permute(0, 3, 4, 1, 2).reshape(b*h*w, t, c)
  x = x + self.temporal_attn(x)
  ```
- 验证：t=1 时输出应与输入一致（残差 + 零初始化）。
- 完成标志：前向跑通 + 单帧恒等验证通过。

### ☐ 3_6 联合训练与长视频外推
- 重读 VDM 的图像-视频联合训练一节与长视频采样策略（条件重建引导）。
- 补充读 [FIFO-Diffusion](https://arxiv.org/abs/2405.11473)：免训练地把短视频模型外推成长视频（对角去噪队列）。
- 思考题：自回归外推长视频为什么会「越生成越糊」？误差累积的来源是什么？
- 完成标志：能画出 FIFO 的对角去噪示意图。

### ☐ 3_7 自测：口述题
1. 分解时空注意力相比全 3D 注意力省了什么、丢了什么？
2. SVD 论文为什么被视为「数据工程论文」？
3. AnimateDiff 的 motion module 插入底座时为什么不破坏原有图像质量？

## 完成标志
- temporal attention 手写实现通过验证。
- 能把 VDM → Video LDM → SVD → AnimateDiff 的演进逻辑讲成一条线。
