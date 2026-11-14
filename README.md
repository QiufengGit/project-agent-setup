# Project Agent Setup

两份可以独立使用的轻量项目管理 skill：

| Skill | 用途 | 入口 |
| --- | --- | --- |
| `project-workspace` | 目录归属、文件导航、任务交接与恢复 | [SKILL.md](skills/project-workspace/SKILL.md) |
| `project-notes` | 长期提案、决策理由、替代方案与验证记录 | [SKILL.md](SKILL.md) |

`project-workspace` 约定工作区根层只保留目录和 Markdown，使用 `code/`、`resources/`、`results/`、`scripts/`，各一级目录附 README。任务和实际结果目录以 `YYYY-MM-DD-` 开头；工程内部布局和冻结归档保持各自结构。

## 文件结构与依赖

```text
project-agent-setup/
├── SKILL.md                     # project-notes，保留原安装入口
├── skills/
│   └── project-workspace/
│       ├── SKILL.md
│       ├── agents/openai.yaml
│       └── references/templates.md
├── package.json                 # npm 打包元数据和文件白名单
└── README.md
```

`project-notes` 的正文和模板自包含。`project-workspace` 按需读取包内的 `references/templates.md`，复制或分享时应带上整个目录；它可与 `project-notes` 配合，但没有必须安装的其他 skill。两者均不要求插件、hooks 或额外运行库；安装器本身需要 Node.js/npm。

通用流程保存在 skill 中，具体项目的约定、环境和进度保存在目标项目内。模板中的项目名和日期仅为示例，不要求存在相应工程。仓库不包含实际项目的任务记录、环境配置、数据或实验结果。

## 从 GitHub 安装

在目标项目目录执行，安装工作区管理 skill：

```bash
npx skills@1.7.0 add QiufengGit/project-agent-setup --full-depth --skill project-workspace --agent codex
```

安装长期决策记录 skill，沿用原命令：

```bash
npx skills@1.7.0 add QiufengGit/project-agent-setup --skill project-notes --agent codex
```

根目录保留了 `project-notes` 的 `SKILL.md`，因此发现嵌套的 `project-workspace` 时需要 `--full-depth`。仅列出所有可安装的 skill：

```bash
npx skills@1.7.0 add QiufengGit/project-agent-setup --full-depth --list
```

默认安装到目标项目；多个项目共用时可增加 `--global`。其他 agent 使用安装器支持的 `--agent` 名称。

## 从本地目录安装

在目标项目目录执行，将 `/path/to/project-agent-setup` 替换为本仓库的实际路径：

```bash
npx skills@1.7.0 add /path/to/project-agent-setup --full-depth --skill project-workspace --agent codex
```

也可以直接指定 `/path/to/project-agent-setup/skills/project-workspace`；直接指向 skill 目录时不需要 `--full-depth`。需要 `project-notes` 时将 `--skill` 的值替换为 `project-notes`。

## 使用

- 用 `$project-workspace` 初始化或整理指定工作区，保存中间进展，或从已有任务入口继续。单次问答不需要初始化工作区。
- 用 `$project-notes` 保存有长期价值的提案、实施决策和排查结论。笔记路径为 `.agents/notes/<lifecycle>/<class>/yyyy-mm-dd-topic-title.md`，具体分类见其 skill 正文。

换账号或重连后，任务恢复依赖项目内已经保存的记录；安装 skill 本身不会同步聊天历史或恢复已经退出的训练进程。通用脚本归入目标项目的 `scripts/`，其运行依赖由该目录的 README 说明。

## 可选：npm 本地打包

包名沿用 `project-notes`，文件白名单包含两份 skill；尚未发布到 npm registry。

```bash
npm pack /path/to/project-agent-setup
```

生成 `project-notes-0.1.0.tgz`。在目标项目安装本地归档，再导入需要的 skill：

```bash
npm install --no-save --package-lock=false --ignore-scripts /path/to/project-notes-0.1.0.tgz
npx skills@1.7.0 add ./node_modules/project-notes --full-depth --skill project-workspace --agent codex
```

`npm install` 仅放置包文件；包没有自动安装脚本或命令行入口，不使用 `npx project-notes`。
