# 视频生成 学习计划

> 目标：从「已掌握扩散/流匹配理论（DDPM、分数匹配、SDE/ODE、Flow Matching）」到「能读懂视频生成前沿论文并复现核心组件」。
> 总时长：约 6 周（每天 2~3 小时，宁慢勿断）。

## 设计思路

这套计划建立在你已有的生成模型理论底座上，按「先补通用积木 → 再走技术史 → 然后当代主干 → 最后工程与前沿」的顺序排列：

- 阶段 0-2 补齐视频生成之前人人默认你会、但理论图谱里还没有的四块积木：潜空间（VAE/视频 VAE）、DiT 骨架、文本条件与 CFG。
- 阶段 3-4 是主线：先走 U-Net 时代的技术史（VDM → Video LDM/SVD → AnimateDiff），再进入 DiT 时代（Sora → CogVideoX/HunyuanVideo/Wan）。
- 阶段 5-6 转向系统能力：数据管线、训练配方、可控生成、采样加速、评测。
- 阶段 7 站在边界上：世界模型、实时生成、RL 后训练三条前沿线，选一条深挖。

每个阶段难度只比上一个高一点点；每个任务都是可勾选的小目标，且有明确的「完成标志」。动手任务（手写 VAE、DiT block、时间注意力、时空 DiT、LoRA 微调、TeaCache、VBench）是这条路线的骨架——读懂不算会，复现才算。

## 学习路线总览

| 阶段 | 主题 | 时长 | 里程碑 |
|------|------|------|--------|
| 0 | [潜空间与视觉分词器](plan/stage0-潜空间与视觉分词器.md) | 约 4 天 | 讲清「为什么在潜空间生成」，手写 VAE 跑通 |
| 1 | [ViT 与 DiT 骨架](plan/stage1-ViT与DiT骨架.md) | 约 4 天 | 手写 DiT block，理解 adaLN 条件注入 |
| 2 | [条件注入与引导](plan/stage2-条件注入与引导.md) | 约 3 天 | 玩转 CFG，理清三种条件注入机制 |
| 3 | [经典视频扩散方法](plan/stage3-经典视频扩散方法.md) | 约 6 天 | 走完 U-Net 时代，手写时间注意力层 |
| 4 | [DiT 视频时代](plan/stage4-DiT视频时代.md) | 约 7 天 | 读懂 Sora 系开源 SOTA，手写时空 DiT |
| 5 | [数据与训练工程](plan/stage5-数据与训练工程.md) | 约 5 天 | 完成一次视频模型 LoRA 微调 |
| 6 | [采样加速与评测](plan/stage6-采样加速与评测.md) | 约 4 天 | TeaCache 加速实测 + VBench 评测实测 |
| 7 | [前沿方向巡礼](plan/stage7-前沿方向巡礼.md) | 约 4 天 | 选定主攻前沿线，产出深挖笔记 |

## 在线访问（手机也能学）

| 入口 | 网址 |
|------|------|
| 📖 学习站（阅读文档 + 勾选任务） | https://amn765.github.io/video_gen_study/ |
| 📊 打卡面板（进度 / 热力图 / 连续天数） | https://amn765.github.io/video_gen_study/tracker/ |

手机浏览器打开后选「添加到主屏幕」即可当 App 用。两个页面共享同一份进度（同一浏览器内），
换设备用面板里的「导出 / 导入进度」同步。仓库地址：https://github.com/amn765/video_gen_study

## 怎么使用这套计划

1. **打开进度面板**：访问上面的在线网址，或用浏览器打开本地 `tracker/index.html`（双击即可）。
   每完成一个任务就勾选，进度条、热力图、连续天数会实时更新。进度存在浏览器 localStorage 里，
   换浏览器/电脑前记得用「导出进度」备份。
2. **每天的节奏**：打开当前阶段的 markdown → 挑 1~2 个任务 → 动手验证 → 回到面板打勾。
   即使某天没时间，也点一下「今日打卡」保住连续天数。
3. **黄金法则**：
   - 所有代码亲手敲一遍，不要复制粘贴。
   - 每个任务做完后，合上资料口述一遍学到了什么——讲不出来就是没懂。
   - 卡住超过 1 小时就先跳过、做标记，往往学到后面会自然解开。

## 推荐资料（全程通用）

本计划的原则：有优秀讲解型资源的主题，先用讲解材料建立直觉、原论文作对照；没有好讲解的主题（多为最新工作）才直接读原文。

- [MIT 6.S184: Flow Matching and Diffusion Models](https://diffusion.csail.mit.edu/)——衔接你已学的流匹配理论
- [Lilian Weng's Blog](https://lilianweng.github.io/)——生成模型系列长文（VAE、扩散）
- [Jay Alammar's Blog](https://jalammar.github.io/)——Transformer / Stable Diffusion 图解
- [Sander Dieleman's Blog](https://benanne.github.io/)——扩散引导（guidance）讲得最透的博客
- [AI Coffee Break](https://www.youtube.com/@AICoffeeBreak)——论文讲解视频（VDM、Sora 等）
- [Stanford CS236 课程站](https://deepgenerativemodels.github.io/)——深度生成模型系统课程 notes
- [Hugging Face Diffusers 文档](https://huggingface.co/docs/diffusers/index) + [Diffusion Course](https://huggingface.co/learn/diffusion-course)——所有动手环节的官方参考
- [arXiv](https://arxiv.org/) cs.CV 新论文 listing——阶段 7 以后保持跟踪用

## 说明

- 全部参考资料均为互联网公开资源（arXiv 论文、官方文档、项目主页），引用前已逐条校验链接真实性。
- 已部署到 GitHub Pages（GitHub Actions 管道，兼容中文文件名），线上可直接阅读 `plan/` 文档并勾选任务；本地双击 `index.html` 时浏览器会拦截对 `plan/*.md` 的读取（CORS 限制），`tracker/index.html` 无此限制。
