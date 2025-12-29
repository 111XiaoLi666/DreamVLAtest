# DreamVLA 中文使用说明（LIBERO-GOAL 测试）

本 README 介绍如何在本仓库中使用 **LIBERO-GOAL** 进行测试评估，并简要说明项目结构与关键脚本。

## 项目结构

- `assets/`：项目示意图与动画资源。
- `data_info/`：数据集相关的元信息与转换结果索引（例如 `libero_goal_converted.json`、`ep_start_end_ids.npy`）。
- `data_process/`：数据预处理与特征提取脚本（CoTracker / SAM / DINOv2 等）。
- `docs/`：安装、运行与数据处理说明文档。
- `models/`：模型主体与模块实现（`dreamvla_model.py`、`vit_mae.py`、`perceiver_resampler.py` 等）。
- `scripts/`：训练/微调/评测脚本入口（LIBERO 与 CALVIN 的各类实验）。
- `utils/`：通用工具与数据处理/评测逻辑（参数解析、分布式、LIBERO 评测等）。
- `train.py`：训练主入口。
- `eval_libero.py`：LIBERO 评测主入口（配合 `scripts/LIBERO` 使用）。
- `eval_calvin.py`：CALVIN 评测主入口。
- `dist_utils.py`：分布式辅助函数（部分数据处理脚本会复用）。
- `requirements.txt`：Python 依赖。

## LIBERO-GOAL 测试流程

以下流程以 `scripts/LIBERO/DreamVLA/eval_goal.sh` 为入口。请根据你本地的路径更新脚本内相关参数。

### 1) 环境与 LIBERO 安装

参考 `docs/LIBERO_INSTALL.md` 创建 conda 环境并安装 LIBERO 依赖。

### 2) 准备模型与数据

- **LIBERO 数据**：下载并解压 LIBERO 数据集，将路径填入脚本中的 `--libero_path`。
- **ViT MAE 预训练权重**：下载后设置 `vit_checkpoint_path`。
- **DreamVLA checkpoint**：评测脚本需要一个 checkpoint 目录作为参数。

### 3) 配置并运行评测

编辑 `scripts/LIBERO/DreamVLA/eval_goal.sh`：

- `vit_checkpoint_path`：指向 `mae_pretrain_vit_base.pth`。
- `--libero_path`：指向 LIBERO 数据根目录。
- `pthlist`：要评测的 checkpoint id 列表（脚本内默认 `("14")`）。

执行评测（传入 checkpoint 目录）：

```bash
bash scripts/LIBERO/DreamVLA/eval_goal.sh /path/to/checkpoints/goal
```

日志输出位置：

- `eval_libero/<checkpoint_dir_name>/<ckpt_id>.log`

### 4) 可选：数据特征准备

如果你在训练或评测中启用了动态轨迹/SAM/DINOv2 等特征（如 `--load_track_labels`、`--load_sam_features`），请先按 `docs/LIBERO_RUN.md` 中的步骤完成数据转换与特征提取：

- CoTracker 动态轨迹：`data_process/cotrack_extractor_libero.py`
- SAM 特征：`data_process/sam_extractor_libero.py`
- DINOv2 特征：`data_process/dino_extractor_libero.py`

这些脚本会在转换后的数据目录下生成对应的特征文件。

## 重点脚本说明

- `scripts/LIBERO/DreamVLA/eval_goal.sh`：LIBERO-GOAL 评测脚本入口。
- `eval_libero.py`：评测入口逻辑，调用 `utils/eval_utils_libero.py`。
- `utils/eval_utils_libero.py`：LIBERO 评测核心实现（任务循环、动作推理与环境交互）。
- `utils/arguments_utils.py`：统一的命令行参数解析。
- `data_process/*_libero.py`：LIBERO 数据特征与转换相关脚本。

如需完整的训练/微调流程，请参考 `docs/LIBERO_RUN.md` 中的详细说明。
