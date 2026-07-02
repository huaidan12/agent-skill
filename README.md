# agent-skill

一个 Claude Code 的**基础版自定义命令集**。项目命名叫 "skill"，但从 Claude Code 的机制上讲，它其实是 **Slash Command（斜杠命令）**——两者概念不同，见下文说明。

## Skill vs Command：一个常见误区

| | Skill | Slash Command |
|---|---|---|
| 存放位置 | `~/.claude/skills/<name>/SKILL.md` 或项目 `.claude/skills/` | `~/.claude/commands/*.md` 或项目 `.claude/commands/` |
| 触发方式 | 由模型根据 `description` **自主判断**是否加载调用 | 用户在对话框输入 `/<name>` **显式触发** |
| 结构 | 目录形式，可携带 `scripts/`、`references/` 等资源 | 单个 markdown 文件 |
| 定位 | 模型的一种"能力包" | 提示词模板（prompt template） |

本项目里的 `.claude/commands/*.md` 只有 frontmatter + prompt 正文，走的是 Command 机制。虽然文件名里带 "skill" 字样，但它并不会像真正的 skill 那样被模型主动识别调用，必须由用户敲 `/weekly-report-skill` 或 `/java-review` 才会生效。

## 目录结构

```
agent-skill/
├── .claude/
│   └── commands/
│       ├── weekly-report-skill.md   # /weekly-report-skill
│       └── java-review.md           # /java-review
├── pom.xml                          # 保留的空 Maven 壳，暂无实际 Java 代码
└── src/main/java/org/example/Main.java
```

## 命令说明

### `/weekly-report-skill`

将日常琐碎的开发流水账，重构为结构化的企业级周报。

- **角色**：资深研发总监
- **能力**：分类归纳 → 价值提炼 → Markdown 模板化输出
- **参数**：`[输入你的本周工作要点]`
- **输出板块**：本周核心产出 / 技术优化与问题解决 / 下周工作规划

### `/java-review`

对本地 Java 代码进行严格的 Code Review。

- **角色**：大厂经验的 Java 首席架构师
- **审查优先级**：致命性能 → NPE 与异常处理规范 → 业务逻辑 → 常规性能优化
- **参数**：`[文件路径 / Git 分支 / 留空则默认审查 git diff --cached]`
- **约束**：只出审查报告与重构建议，不直接改文件

## 使用方式

项目级命令会在打开本项目（或在项目根目录下启动 `claude`）时自动加载。

```bash
cd /path/to/agent-skill
claude
```

在对话框输入：

```
/weekly-report-skill 本周主要修了 xxx bug，参加了 yyy 会议 ...
/java-review src/main/java/xxx/YourService.java
```

## 如果想把它做成"真正的 Skill"

要让模型能自主识别调用，需要迁移到 skill 目录结构：

```
.claude/skills/
└── weekly-report/
    └── SKILL.md         # 顶部 frontmatter 里的 description 要写清楚"何时触发"
```

`SKILL.md` 的 `description` 字段决定了模型什么时候会主动加载它，因此需要写得足够具体（触发场景、关键词、适用条件）。

## 备注

`pom.xml` 与 `src/` 只是 IDEA 新建 Maven 项目留下的默认骨架，本项目**当前不含任何可运行的 Java 代码**，实体价值全在 `.claude/commands/` 下。
