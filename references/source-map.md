# 来源映射

本 skill 基于三份资料和官方 `skill-creator` 流程构建。

三份完整 Markdown 资料已随 skill 打包，位于：

```text
references/source-books/huashu-claude-skills-whitepaper-2025-12.md
references/source-books/agent-skills-orange-book-calibrated.md
references/source-books/complete-guide-building-skills-claude-zh.md
```

官方 `skill-creator` 不在本 skill 中内嵌。需要创建、校验或打包时，通过当前环境已安装的 `skill-creator` 发现并调用。不要把用户机器上的绝对路径写入 skill 文件或打包产物。

如果当前环境没有官方 `skill-creator`，按桥接说明从 Anthropic 官方仓库安装：

```text
https://github.com/anthropics/skills/tree/main/skills/skill-creator
```

## 按来源提炼出的标准

### 花叔 Claude Skills 白皮书

用于提炼：

- 三层渐进式披露。
- `SKILL.md`、`scripts/`、`references/`、`assets/` 的结构。
- `description` 是触发入口。
- 大段内容应从 `SKILL.md` 移到引用文件。
- secret、API key、安装审查等安全提醒。
- 创建检查清单：澄清用例、写清触发场景、组织资源、测试、迭代。

### Agent Skills 橙皮书

用于提炼：

- Skills 是可复用的自然语言工作流。
- 触发设计要同时覆盖直接说法和语义意图。
- 排错时区分“没触发”和“触发后质量不好”。
- `skill-creator` 是 skill 的质量工程体系。
- Create、Eval、Improve、Benchmark 的完整循环。
- 六种设计模式：检查清单型、多方案选择型、流水线型、API 集成型、多 Agent 协作型、思维蒸馏型。

###《构建 Claude Skills 的完整指南》

用于提炼：

- 技术要求：带 YAML frontmatter 的 `SKILL.md`。
- 规划设计：先有具体用例，再写 skill。
- 测试和迭代是必要环节。
- 通过校验后的包进行分发。
- 可组合性和可移植性。
- frontmatter 限制和 `description` 质量规则。

### 官方 Skill Creator

用于提炼：

- 标准创建顺序。
- 新 skill 应使用初始化脚本。
- `SKILL.md` 正文使用命令式/流程式写法。
- 资源放置规则。
- 官方校验和打包脚本。

## 维护说明

任一来源资料更新时，先更新 `references/skill-builder-standards.md`，再视情况修改 `SKILL.md`。完整书稿只作为依据和例子，不应替代可执行标准。
