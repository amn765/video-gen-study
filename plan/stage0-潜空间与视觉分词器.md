# 阶段 0 · 潜空间与视觉分词器（约 4 天）

> 目标：搞懂现代视频生成为什么几乎都在「压缩潜空间」里做扩散，并亲手训练一个小 VAE。
> 你已经在知识图谱里掌握了 DDPM 与流匹配的训练目标；这一阶段解决的是「在什么空间里生成」的问题。

主线资料（讲解型）：[Jaan Altosaar: VAE 教程](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/) + [Stanford CS236 课程 notes](https://deepgenerativemodels.github.io/) + [Lilian Weng: From Autoencoder to Beta-VAE](https://lilianweng.github.io/posts/2018-08-12-vae/)。原论文作对照选读。

## 任务清单

### ☐ 0_1 VAE 原理精读
- 先读讲解：[Jaan Altosaar 的 VAE 教程](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)（神经网络与概率图模型双视角，比原论文友好得多），配合 [CS236 课程 notes](https://deepgenerativemodels.github.io/) 的 VAE 部分。
- 对照选读：原论文 [Auto-Encoding Variational Bayes](https://arxiv.org/abs/1312.6114)，只看第 2 节的核心推导。
- 画一张图：编码器输出均值与方差 → 重参数化采样 → 解码器；标出重构损失与 KL 散度各自作用在哪。
- 思考题：KL 项把后验强行拉向标准高斯，为什么反而让「从先验采样生成」成为可能？
- 完成标志：能口述重参数化技巧解决了什么问题（梯度无法穿过采样节点）。

### ☐ 0_2 ⭐ 手写训练一个 MNIST VAE
- 用 PyTorch 从零写，不复制现成教程；骨架如下，亲手补全：
  ```python
  mu, logvar = encoder(x).chunk(2, dim=1)
  z = mu + torch.exp(0.5 * logvar) * torch.randn_like(mu)
  x_hat = decoder(z)
  loss = F.mse_loss(x_hat, x) - 0.5 * torch.mean(1 + logvar - mu**2 - logvar.exp())
  ```
- 训练 10 个 epoch，保存两张网格图：原图 vs 重构图；从先验随机采样的生成图。
- 卡住时回头查 CS236 notes 的 VAE 章节，不要直接抄现成实现。
- 完成标志：重构图可辨认数字，采样图能看出「像数字」，并能解释两者差距的来源。

### ☐ 0_3 VQ-VAE 与 VQ-GAN：离散化与感知质量
- 先读讲解：[Lilian Weng 的 VAE 长文](https://lilianweng.github.io/posts/2018-08-12-vae/) 后半部分的 VQ-VAE 章节，把离散 codebook 的直觉建立起来。
- 对照选读：[VQ-VAE 原论文](https://arxiv.org/abs/1711.00937) 第 2 节、[Taming Transformers (VQ-GAN)](https://arxiv.org/abs/2012.09841) 的感知损失 + PatchGAN 判别器部分。
- 亲手填完这张对比表：

| 维度 | 连续 VAE | VQ-VAE | VQ-GAN |
|------|----------|--------|--------|
| 潜变量形式 | 连续向量 | 离散索引 | 离散索引 |
| 损失特点 | 重构+KL | 重构+commitment | 感知损失+对抗 |
| 重建观感 | 偏模糊 | 取决于解码器 | 细节锐利 |

- 思考题：为什么「感知损失 + 对抗损失」能让重建从模糊变锐利？
- 完成标志：能讲清 codebook 坍塌是什么、VQ-GAN 用了哪些手段缓解。

### ☐ 0_4 LDM 范式：把扩散搬进潜空间
- 先读图解：[The Illustrated Stable Diffusion](https://jalammar.github.io/illustrated-stable-diffusion/)（jalammar 出品，整链路图解），再看代码向讲解 [The Annotated Diffusion Model](https://huggingface.co/blog/annotated-diffusion) 里 autoencoder 的角色。
- 对照选读：[LDM 原论文](https://arxiv.org/abs/2112.10752) 第 3 节和图 2 的两阶段结构。
- 搞清压缩率 f=4 / 8 / 16 的权衡：压缩越狠训练越便宜，但重建上限越低。
- 完成标志：能解释「感知压缩」与「语义压缩」的分工，以及为什么第一阶段自编码器决定了整个系统的画质上限。

### ☐ 0_5 视频 VAE：从图像压缩到时空压缩
- 本主题暂无优质讲解站，直接读论文（都比较短）：[MAGVIT](https://arxiv.org/abs/2212.05199) 的 3D-VQ 部分、[MAGVIT-v2](https://arxiv.org/abs/2310.05737)（重点：tokenizer 质量如何反超生成主干成为瓶颈）。
- 读 [CogVideoX 论文](https://arxiv.org/abs/2408.06072) 中 3D Causal VAE 一节：时间维为什么要「因果」（只用过去的帧）、为什么首帧单独处理。
- 思考题：视频 VAE 时间维压缩比（如 4 倍）为什么不能像空间维（8x8）一样激进？
- 完成标志：能画出现代视频生成栈的三层结构：像素 → 时空潜空间 → DiT，并标注每层压缩率。

### ☐ 0_6 自测：口述题
合上资料，能流畅回答：
1. VAE 的两项损失各自负责什么？去掉 KL 会怎样？
2. 为什么生成模型偏爱潜空间而不是像素空间？
3. 视频 VAE 的「因果性」满足的是什么部署需求？

## 完成标志
- 手写 VAE 训练跑通，产出重构/采样对比图。
- 能把「像素 → 潜空间 → 生成网络」的整条链路讲给一个没读过论文的人听。
