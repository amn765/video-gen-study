# 阶段 1 · ViT 与 DiT 骨架（约 4 天）

> 目标：掌握当代视频生成的主干网络 DiT，理解条件注入的几种方式，为读 Sora 系论文铺路。
> 这一阶段把你已会的「扩散训练目标」装进 Transformer 身体里。

主线资料（讲解型）：[The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) + [AI Summer: ViT 十分钟图解](https://theaisummer.com/vision-transformer/) + [DiT 项目主页](https://www.wpeebles.com/DiT)。原论文作对照。

## 任务清单

### ☐ 1-1 Transformer 回顾与手写 attention
- 读 [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) 回顾整体结构；想深挖位置编码就读 [Transformer Architecture: The Positional Encoding](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/)。
- 手写 scaled dot-product attention（含 mask 支持），与 `torch.nn.functional.scaled_dot_product_attention` 对拍数值误差。
- 完成标志：20 行内写对，且能解释为什么要除以 sqrt(d_k)。

### ☐ 1-2 ViT：图像即 token 序列
- 先读讲解：[AI Summer: How the Vision Transformer works](https://theaisummer.com/vision-transformer/)（10 分钟图解版）。
- 对照选读：[ViT 原论文](https://arxiv.org/abs/2010.11929) 第 2-3 节。
- 用 reshape/einsum 亲手写 patchify：把 (B,3,224,224) 变成 (B,196,768)，再写逆操作 unpatchify，验证互逆。
- 思考题：patch size 从 16 减到 8，序列长度和注意力计算量各怎么变？这对视频（多一个时间维）意味着什么？
- 完成标志：patchify/unpatchify 互逆验证通过。

### ☐ 1-3 ⭐ DiT 精读
- 先看 [DiT 项目主页](https://www.wpeebles.com/DiT)（结构图与 scaling 曲线一目了然），再读 [DiT 原论文](https://arxiv.org/abs/2212.09748)，配合官方仓库 [facebookresearch/DiT](https://github.com/facebookresearch/DiT) 的 models.py 一起看。
- 弄清三种条件注入方案：

| 方案 | 做法 | 代表工作 |
|------|------|----------|
| in-context tokens | 条件拼进序列 | DiT 变体 |
| cross-attention | 每 block 加交叉注意力 | LDM / SD |
| adaLN-Zero | 用条件回归 LN 的调制参数 | DiT 默认 |

- 重点理解 adaLN-Zero 为什么把调制 MLP 初始化成全零（每个 block 初始等价于恒等映射，稳定训练）。
- 完成标志：能复述 DiT 论文的消融结论——adaLN-Zero 为什么又省参数又效果好。

### ☐ 1-4 ⭐ 手写一个 DiT block
- 用 PyTorch 实现：无仿射 LayerNorm → adaLN 六路调制 → MHSA → 门控残差 → MLP。骨架：
  ```python
  shift_msa, scale_msa, gate_msa, shift_mlp, scale_mlp, gate_mlp = \
      self.adaLN_modulation(cond).chunk(6, dim=1)
  x = x + gate_msa.unsqueeze(1) * self.attn(modulate(self.ln1(x), shift_msa, scale_msa))
  x = x + gate_mlp.unsqueeze(1) * self.mlp(modulate(self.ln2(x), shift_mlp, scale_mlp))
  ```
- 验证：调制层零初始化 + cond 任意时，block(x) 应等于 x（恒等性测试）。
- 完成标志：前向跑通 + 恒等性验证通过。

### ☐ 1-5 SiT：把你学过的流匹配装进 DiT
- 先读讲解：[MIT 6.S184: Introduction to Flow Matching and Diffusion Models](https://diffusion.csail.mit.edu/) 的课程 notes（flow matching 从 ODE 到训练目标的完整推导，风格和你学过的流匹配学习站一致）。
- 对照选读：[SiT 原论文](https://arxiv.org/abs/2401.08740)。
- 回忆你图谱里的 rectified flow：x_t = (1-t)·x_0 + t·x_1，目标速度 v = x_1 - x_0；SiT 正是用 DiT 回归这个速度场。
- 思考题：eps-prediction、x-prediction、v-prediction 三种参数化在同一模型下如何互相换算？
- 完成标志：能写出三种参数化的换算公式，并说明 v-prediction 在高分辨率级联中为何更稳。

### ☐ 1-6 自测：口述题
1. ViT 如何把一张图变成序列？位置信息从哪来？
2. adaLN 与 cross-attention 注入条件的本质区别是什么？
3. 为什么说 DiT 的 scaling 曲线是 Sora 故事的前提？

## 完成标志
- attention、patchify、DiT block 三个手写组件全部验证通过。
- 能不看代码复现 DiT block 的完整数据流。
