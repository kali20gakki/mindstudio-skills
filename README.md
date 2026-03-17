# mindstudio-skills

一个面向支持 `SKILL.md` 约定的 AI Agent 的技能仓库，当前重点覆盖 **Ascend / MindStudio Profiling 分析**，并补充少量通用辅助技能。

这个仓库的目标不是堆积提示词，而是把可复用的诊断经验、明确的 SOP、以及必要的脚本工具收敛到同一个技能目录里，让 Agent 在合适场景下稳定触发、稳定执行、稳定输出。

## 特点

- 聚焦真实问题场景，而不是抽象能力描述
- 每个 skill 都有清晰的触发条件、边界和输出要求
- 复杂流程优先固化为脚本，减少 Agent 临场发挥带来的不确定性
- 保持轻量目录结构，便于直接复制、组合或迁移到其他 skill 仓库

## 当前技能

| Skill | 说明 | 典型输入 | 依赖 |
| --- | --- | --- | --- |
| [`cluster-fast-slow-rank-detector`](./cluster-fast-slow-rank-detector/SKILL.md) | 面向 Ascend 集群 Profiling 的快慢卡诊断，区分 Host 下发慢、计算慢、通信慢等瓶颈类型 | 集群 profiling 根目录、慢卡/快卡分析诉求 | `msprof-analyze-advisor`、Python、`pandas` |
| [`mindstudio_profiler_data_check`](./mindstudio_profiler_data_check/SKILL.md) | 对 MindStudio profiler / msprof 采集结果做前置体检，判断是否可进入后续分析 | profiler 结果目录 | `msprof`，离线解析时依赖 `torch_npu` 或 `mindspore` |
| [`op-mfu-calculator`](./op-mfu-calculator/SKILL.md) | 计算 matmul / GEMM / FlashAttention 等算子的 MFU，并给出推导过程 | 算子类型、shape、执行时间、峰值算力 | 无额外脚本依赖 |
| [`github-raw-fetch`](./github-raw-fetch/SKILL.md) | 将 GitHub 文件页链接自动转换为 raw 链接并抓取文件原文 | GitHub `blob` 或 raw 文件链接 | 支持网络抓取的 Agent / 工具 |

## 目录结构

```text
.
├── README.md
├── cluster-fast-slow-rank-detector/
│   ├── SKILL.md
│   └── scripts/
│       ├── compare_api_stats.py
│       ├── compare_op_stats.py
│       └── rank_data_finder.py
├── mindstudio_profiler_data_check/
│   ├── SKILL.md
│   └── scripts/
│       ├── offline_parse_mindspore.py
│       └── offline_parse_pytorch.py
├── op-mfu-calculator/
│   └── SKILL.md
└── github-raw-fetch/
    └── SKILL.md
```

## 快速使用

### 1. 选择一个 skill

每个 skill 都是一个独立目录，至少包含一个 `SKILL.md`。如果你的 Agent 支持本地 skills 目录，通常可以直接把对应目录复制、软链接，或按仓库方式纳入技能搜索路径。

### 2. 确保运行环境可用

部分技能除了说明文档，还会调用本地脚本或外部工具：

- Python 3
- `pandas`
- `msprof`
- `torch_npu` 或 `mindspore`（仅离线解析相关技能需要）

### 3. 用任务语言触发技能

示例：

- “帮我检查这个 MindStudio profiler 数据是否完整可分析”
- “分析这个 Ascend 集群 profiling 目录里的快慢卡问题”
- “根据这个 matmul 的 shape 和耗时计算 MFU”
- “把这个 GitHub 文件页面链接转成 raw content 给我”

## Skill 设计约定

本仓库遵循一种尽量通用的 Agent Skills 组织方式：

- 每个 skill 一个目录
- 必须包含 `SKILL.md`
- `SKILL.md` 使用 YAML frontmatter，至少包含：
  - `name`
  - `description`
- 可选的 `scripts/` 用于承载确定性更强、需要重复执行的辅助工具
- 说明文档优先写清楚触发条件、步骤约束、输出格式，而不是泛泛解释概念

## 新增 Skill 的建议流程

1. 新建一个 kebab-case 目录，例如 `my-new-skill/`
2. 添加 `SKILL.md`，写清楚 `name`、`description`、适用场景和 SOP
3. 如果流程里有稳定可执行的步骤，把它放进 `scripts/`
4. 用一个真实任务做最小验证，确认技能会被正确触发
5. 把新 skill 补充到本 README 的“当前技能”表格中


## 参考的开源项目

这个 README 的组织方式主要参考了几类业界开源项目的写法：项目定位先行、技能列表可扫读、目录结构明确、快速使用路径清晰、扩展规范单独成节。

- Vercel `skills`: <https://github.com/vercel-labs/skills>
- Vercel `agent-skills`: <https://github.com/vercel-labs/agent-skills>
- `claude-skills-base`: <https://github.com/Cam10001110101/claude-skills-base>

如果后续这个仓库继续扩展，可以再补充：

- `CONTRIBUTING.md`
- 技能分类目录（如 `profiling/`、`utility/`、`research/`）
- 自动校验脚本（frontmatter、目录结构、脚本可执行性）
