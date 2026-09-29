# Project Agent Setup

按生命周期和决策类别维护项目的 `.agents/notes/`。skill 名称为 `project-notes`，入口为 [SKILL.md](SKILL.md)。

## 文件结构

```text
project-agent-setup/
├── SKILL.md       # 标准 Agent Skills 入口，含 name 和 description
├── package.json   # npm 打包元数据和文件白名单
└── README.md      # 安装与使用说明
```

正文和笔记模板自包含，不依赖插件、hooks 或额外运行库。导入 skill 后，只有在用户要求记录或整理项目决策时才维护目标项目中的笔记。

## 从 GitHub 导入

在目标项目目录执行：

```bash
npx skills@1.7.0 add QiufengGit/project-agent-setup --skill project-notes --agent codex
```

也可以使用 npm：

```bash
npm exec --yes --package=skills@1.7.0 -- skills add QiufengGit/project-agent-setup --skill project-notes --agent codex
```

仓库入口直接提供标准的 SKILL.md；安装后 skill 名称仍为 project-notes。

## 从本地目录导入

在要使用该 skill 的目标项目目录执行。将 `/path/to/project-notes` 替换为本 skill 的实际绝对目录；不要把源目录当作另一份安装的目标目录。

```bash
npx skills@1.7.0 add /path/to/project-notes --agent codex
```

仅查看可导入的 skill：

```bash
npx skills@1.7.0 add /path/to/project-notes --list
```

## 通过 npm exec 导入

与上述 npx 命令等价：

```bash
npm exec --yes --package=skills@1.7.0 -- skills add /path/to/project-notes --agent codex
```

需要其他 agent 时，使用安装器支持的 `--agent` 名称。默认安装到目标项目；需要全局安装时显式增加 `--global`。

## npm 打包与安装

本包可以本地打包，无须发布到 registry：

```bash
npm pack /path/to/project-notes
```

生成 `project-notes-0.1.0.tgz`。要通过 npm 安装该归档并导入 agent，在目标项目执行：

```bash
npm install --no-save --package-lock=false --ignore-scripts /path/to/project-notes-0.1.0.tgz
npx skills@1.7.0 add ./node_modules/project-notes --agent codex
```

`npm install` 只负责放置包文件，随后用 skills 安装器将其导入 agent。包没有自动安装脚本或命令行入口，因此不使用 `npx project-notes`。本地归档可直接使用；未发布到 npm registry，不能把裸包名当作已发布的安装地址。

## 使用

在支持命名调用的 agent 中调用 `$project-notes`，或要求 agent 使用 project-notes 记录提案、实现决策、验证结果或归档旧决策。

生成的笔记采用 `.agents/notes/<lifecycle>/<class>/yyyy-mm-dd-topic-title.md`。生命周期为 `proposed`、`implemented`、`rejected`、`archived`；类别为 `feature`、`bug-fix`、`simplification`、`architecture`、`process`、`testing`。

包内未附带具体项目的笔记或实验结果。笔记路径相对于目标项目，安装位置可以位于其他项目、全局目录或 `node_modules`。
