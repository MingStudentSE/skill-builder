---
name: skill-builder
description: 用于设计、创建、审查、改进和打包 Claude/Codex Agent Skills。用户想把工作流做成 skill、创建新的 SKILL.md、优化触发描述、审查 skill 结构、整理 references/scripts/assets、打包 skill zip，或构建可复用 Agent 能力时，应使用本 skill。
---

# Skill 构建器

## 目标

创建高质量 Agent Skills：先做需求澄清、模式选择和质量把关，再调用官方 `skill-creator` 完成脚手架、校验和打包。

把本 skill 当作“设计层”和“质量门”。把官方 `skill-creator` 当作“创建、校验、打包”的权威执行流程。

## 必读资料

按任务需要读取这些资料：

- `references/skill-builder-standards.md`：三份资料提炼出的执行标准。
- `references/official-skill-creator-bridge.md`：如何调用官方 `skill-creator` 的桥接流程。
- `references/source-map.md`：资料来源和维护说明。
- `references/source-books/`：三本完整 Markdown 参考书。需要追溯依据、查原始例子、更新标准或处理争议规则时再搜索这些文件。

创建、重构、校验或打包 skill 前，先加载当前环境已安装的官方 `skill-creator` skill，并遵循它的流程。不要在 skill 文件里写死任何用户本机绝对路径。

如果当前环境没有 Anthropic 官方 `skill-creator`，先帮助用户从官方仓库安装：

```text
https://github.com/anthropics/skills/tree/main/skills/skill-creator
```

安装完成并确认 `skill-creator` 可用后，再继续创建、校验或打包目标 skill。

## 工作流

### 1. 判断请求类型

先识别用户意图：

- 创建：把一个工作流、领域方法、工具用法或重复任务做成新 skill。
- 改进：修改已有 `SKILL.md` 或配套资源。
- 审查：检查触发、结构、安全性、可移植性和实际可用性。
- 打包：校验并生成可分发 zip。
- 基准测试：用正例和反例优化 `description` 的触发精度。

只有在 skill 目标、目标用户或触发示例缺失且无法合理推断时，才问最多三个澄清问题。否则先做合理假设并继续推进。

### 2. 形成 skill 构建简报

编辑文件前，先显式或隐式形成一份构建简报：

- skill 名称，使用 kebab-case。
- 核心任务，也就是这个 skill 帮 agent 稳定完成什么。
- 应该触发该 skill 的具体用户说法。
- 不应该触发该 skill 的反例。
- 采用的设计模式：检查清单型、多方案选择型、流水线型、集成型、多 Agent 协作型、蒸馏型，或明确组合。
- 渐进式披露计划：哪些内容放在 frontmatter、`SKILL.md`、`references/`、`scripts/`、`assets/`。
- 正文长度预算：推荐 `SKILL.md` 正文保持在 500-2000 字；超过 2000 字触发压缩/下沉审查，超过 3000 字触发拆分审查。
- 安全和确认边界，尤其是不可逆操作、外部 API、凭据、删除或覆盖文件。
- 校验方案和打包目标。

### 3. 检查并准备官方 skill-creator

调用官方创建器前，先检查当前环境是否已有 Anthropic 官方 `skill-creator`：

- 如果已安装，直接加载并继续。
- 如果未安装，按 `references/official-skill-creator-bridge.md` 的“缺失时安装”流程，从官方 GitHub 仓库安装。
- 如果无法联网或无法确定 skill 安装目录，向用户说明阻塞点，并请求用户提供安装目录或官方 skill-creator 文件夹。

不要使用非官方来源替代 Anthropic 官方 `skill-creator`，除非用户明确要求。

### 4. 调用官方 skill-creator

创建新 skill 时，先按 `references/official-skill-creator-bridge.md` 建立脚本能力矩阵，逐个探测 `init_skill.py`、`quick_validate.py`、`package_skill.py`。不要假设这些脚本都在同一个官方 `skill-creator` 目录里；官方仓库当前版本可能没有 `init_skill.py`，而系统内置版本可能没有 `package_skill.py`。

如果能力矩阵中存在初始化脚本，使用实际脚本路径：

```bash
python3 {init_script_path} {skill_name} --path {output_directory}
```

如果找不到初始化脚本，但需要创建新 skill，手动创建最小 `SKILL.md` 结构；随后仍用能力矩阵中的 `quick_validate.py` 校验。

如果 skill 已存在，跳过初始化，先阅读现有文件。

删除不服务于最终 skill 的模板示例文件。只保留真正会被使用的 `scripts/`、`references/` 或 `assets/`。

### 5. 编写 Skill

把 `SKILL.md` 写给另一个 agent 使用，而不是写成给人浏览的说明书。

正文使用命令式、流程式语言。frontmatter 使用第三人称描述，必须同时说明“做什么”和“什么时候用”。

按披露层级放置内容：

- Frontmatter：简洁名称和触发明确的 `description`。
- `SKILL.md`：核心流程、决策规则、资源链接。
- `references/`：长标准、政策、schema、例子、rubric、来源说明。
- `scripts/`：需要确定性或会反复重写的代码。
- `assets/`：模板、字体、样板工程、图片或最终输出要使用的文件。

不要把大段参考资料直接塞进 `SKILL.md`。能放入引用文件的内容优先放到 `references/`。

### 6. 质量门

完成前逐项检查：

- 目录名和 frontmatter 的 `name` 一致，且均为 kebab-case。
- `description` 具体、少于 1024 字符，并包含触发条件。
- skill 教给 agent 的是非显然流程，而不是泛泛建议。
- 工作流足够具体，执行时不需要重新追问显而易见的问题。
- 配套资源有明确用途，并从 `SKILL.md` 中被引用。
- 已考虑触发正例和不触发反例。
- 长内容已移动到 `references/`，符合渐进式披露。
- `SKILL.md` 正文短而精：推荐 500-2000 字。超过 2000 字时先压缩细节、删除 AI 已具备的基础常识、把长例子/rubric/背景材料移到 `references/`；超过 3000 字时必须触发拆分审查，优先按独立任务阶段拆成多个 skill。只有强耦合且每次执行都必需的核心流程、标准和偏好才留在正文。
- 不硬编码 API key、token、secret、私有端点或用户本机路径。
- 不可逆或对外可见操作必须先确认。
- skill 能与其他 skill 组合使用，不假设自己是唯一被加载的能力。

### 7. 校验和打包

需要快速校验时，使用脚本能力矩阵中实际找到的校验脚本：

```bash
python3 {quick_validate_script_path} {skill_directory}
```

用户要求可分发产物，或任务完成本身意味着交付 zip 时，使用脚本能力矩阵中实际找到的打包脚本：

```bash
python3 {package_script_path} {skill_folder} {optional_output_directory}
```

如果校验失败，先修复错误，再重新校验。不要在校验失败时声称 skill 已可用。

### 8. 汇报结果

最终简洁说明：

- skill 目录。
- 创建或修改的关键文件。
- 校验和打包状态。
- 仍然存在的假设或需要用户决定的事项。

除非用户要求，不要直接贴出完整 skill 内容。
