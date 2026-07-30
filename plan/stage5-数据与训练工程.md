# 阶段 5 · 数据与训练工程（约 5 天）

> 目标：理解「数据配方 > 模型结构」这条工业界铁律，并亲手完成一次小规模微调。
> 这一阶段之后，你看论文的眼光会从「结构创新」扩展到「系统配方」。

主线资料：[Panda-70M 项目主页](https://snap-research.github.io/Panda-70M/) + [Open-Sora Plan 仓库文档](https://github.com/PKU-YuanGroup/Open-Sora-Plan) + [HF: LoRA 高效微调教程](https://huggingface.co/blog/lora)。

## 任务清单

### ☐ 5-1 视频数据管线全景
- 先看 [Panda-70M 项目主页](https://snap-research.github.io/Panda-70M/)（有管线流程图和 demo），再对照读 [Panda-70M 论文](https://arxiv.org/abs/2402.19479) 的数据构建流程；补充对照 [InternVid](https://arxiv.org/abs/2307.06942)。
- 整理一条标准管线的环节：原始视频 → 镜头切分（scene detection）→ 黑边/水印/低质过滤 → 运动强度打分（光流）→ 美学打分 → 文本打标 → 去重。
- 思考题：为什么「运动太少」（静态幻灯片）和「运动太乱」（镜头硬切）都要过滤？它们分别会教会模型什么坏习惯？
- 完成标志：能画出管线流程图，并为每环标注一个常用开源工具。

### ☐ 5-2 视频打标（captioning）实践
- 重读 Sora 报告中 re-captioning 一节（DALL·E 3 式思路：用高度描述性字幕重标训练数据）。
- 动手：找 5~10 个公开视频素材（如 Pexels），用一个多模态模型（GPT-4o / Qwen2-VL 等）为每个视频生成「短 caption（一句话）+ 长 caption（含镜头运动、主体、风格、光线）」两版。
- 思考题：短 caption 和长 caption 分别适合训练的哪个阶段？
- 完成标志：产出 caption 对比表，并总结长 caption 应包含的五个要素。

### ☐ 5-3 ⭐ 多阶段训练配方
- 读 [Open-Sora 2.0 报告](https://arxiv.org/abs/2503.09642) 的训练流程章节：图像预训练 → 低分辨率短视频 → 高分辨率长视频的课程式安排，以及各阶段的成本占比。
- 理解 bucket 策略：把不同时长/分辨率的样本分桶、同桶组 batch，避免 padding 浪费。
- 思考题：为什么直接用高分辨率长视频从头训几乎不可行？从算力、数据分布、优化稳定性三个角度回答。
- 完成标志：能写出一份三阶段训练计划表（数据规模、分辨率、时长、batch、预期显存）。

### ☐ 5-4 可控生成：I2V 与相机/轨迹控制
- 回顾图像侧的 [ControlNet](https://arxiv.org/abs/2302.05543)：零卷积旁路思想（论文图 2 一张图就能看懂）。
- 读 [MotionCtrl](https://arxiv.org/abs/2312.03641)（统一相机运动与物体运动）与 [CameraCtrl](https://arxiv.org/abs/2404.02101)（用 Plücker 坐标注入相机位姿）。
- 整理 I2V 的三种条件注入方式：首帧 latent 拼接、跨注意力注入参考图、通道维 concat + 掩码。
- 完成标志：能对比三种 I2V 注入方式在「保真度 vs 多样性」上的差异。

### ☐ 5-5 ⭐ 动手：LoRA 微调一个视频模型
- 先读 [HF: Using LoRA for Efficient Fine-Tuning](https://huggingface.co/blog/lora) 建立 LoRA 直觉（低秩注入为什么有效），再按 diffusers 官方教程 [CogVideoX LoRA 微调指南](https://huggingface.co/docs/diffusers/training/cogvideox)（显存不足可换 Wan2.1-1.3B 对应指南），在 20~50 个自选视频上训练一个风格/概念 LoRA。
- 记录：训练 loss 曲线、学习率、rank、显存占用；每隔 500 步存一次样例视频。
- 完成标志：loss 明显下降，且微调后的模型对新 prompt 能生成带目标风格的视频。

### ☐ 5-6 自测：口述题
1. 为什么视频模型训练里「数据筛选」比「模型调参」收益更大？
2. 课程式多阶段训练为什么有效？（从优化难度与数据效率回答）
3. LoRA 只调低秩矩阵，为什么足以注入一个新风格？

## 完成标志
- 完成一次真实 LoRA 微调并保留完整训练记录。
- 能为一个假想项目设计完整的数据 + 训练方案。
