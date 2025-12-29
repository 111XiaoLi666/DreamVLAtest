以下是让 `38.pth` 能正确加载的参数修改说明（不改动代码，仅说明修改点）。

目标：让运行时模型结构与 `38.pth` 的参数形状一致（根据你提供的 checkpoint 元信息）。

需要修改的脚本与参数

1) `scripts/LIBERO/DreamVLA/eval_goal.sh`

- `--hidden_dim`
  - 当前：`--hidden_dim 1024`
  - 修改为：`--hidden_dim 384`
  - 对应输出：`hidden_dim: 384`、多处权重形状为 `[..., 384]`

- `--num_resampler_query`
  - 当前：`--num_resampler_query 16`
  - 修改为：`--num_resampler_query 6`
  - 对应输出：`num_resampler_query: 6`、`perceiver_latents_shape: [6, 768]`

- `--transformer_layers`
  - 当前：`--transformer_layers 24`
  - 保持不变（已匹配）
  - 对应输出：`transformer_layers: 24`

- `--transformer_heads`
  - 你的输出没有直接给出 heads 数；该值不会影响权重形状，但会影响推理行为。
  - 建议：确认 38.pth 的训练配置后再调整。
  - 如果没有训练配置，可先保留 `--transformer_heads 16`（因为 384 可以被 16 整除）。

其余与 checkpoint 形状一致的参数（无需改动）

- `--action_pred_steps 3` 对应 `action_pred_token` 的形状 `[1, 1, 3, 384]`
- `--sequence_length 7` 对应 `pos_embed` 的形状 `[1, 7, 1, 384]`

如果你之后要跑 `eval_object.sh / eval_spatial.sh / eval_long.sh` 等脚本，同样需要把这些脚本里的 `--hidden_dim` 和 `--num_resampler_query` 改成上面的值，保持与 checkpoint 一致。
