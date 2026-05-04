---
name: skill-builder
description: 用于设计、创建、审查、改进和打包 Claude/Codex Agent Skills。用户想把工作流做成 skill、创建新的 SKILL.md、优化已有 skill、优化触发描述、审查 skill 结构、整理 references/scripts/assets、打包 skill zip，或构建可复用 Agent 能力时，应使用本 skill。
---

# Skill 构建器

## 定位

本 skill 是 **设计层 / 架构师 / 质量门**；官方 `skill-creator` 是 **工具链 / 校验器 / 打包器**。不要复制官方能力，也不要把设计判断交给创建器。

## 必读

按需读取：

- `references/skill-builder-standards.md`：设计模式、500-2000 字质量门、优化已有 skill 的标准。
- `references/official-skill-creator-bridge.md`：官方 creator 的脚本能力矩阵、校验和打包。
- `references/source-map.md`、`references/source-books/`：仅追溯依据或更新标准时读取。

创建、重构、校验或打包前，先加载当前环境中的官方 `skill-creator`；若缺失，按 bridge 从官方仓库安装。不要写死用户本机绝对路径。

## 工作流

### 1. 判断任务

识别是创建、优化、审查、打包还是触发基准测试。只有目标、用户或触发示例无法推断时，才问最多三个澄清问题。

### 2. 先做简报

编辑前形成：skill 名称、核心任务、触发正例/反例、设计模式、是否拆分、资源放置、安全边界、校验/打包计划。

正文推荐 500-2000 字；超过 2000 字先压缩或下沉到 `references/`；超过 3000 字必须拆分审查。

优化已有 skill 时，先读现有 `SKILL.md` 和被引用资源，判断是修触发、压缩正文、拆分 skill、补质量门、整理资源还是修工具链；优先最小有效修改。

### 3. 配合官方 skill-creator

按 bridge 建立脚本能力矩阵，逐个探测：

```text
init      -> scripts/init_skill.py，如存在
validate  -> scripts/quick_validate.py
package   -> scripts/package_skill.py
```

不要假设三个脚本同目录。新建 skill 时，有 `init_skill.py` 就用实际路径初始化；没有则手动创建最小 `SKILL.md`。已有 skill 跳过初始化。

### 4. 编写或改进

`SKILL.md` 写给后续 agent 执行，不写成人类说明书。

- Frontmatter：`name` kebab-case；`description` 说明做什么和什么时候用。
- `SKILL.md`：只放核心流程、决策规则、质量门和安全边界。
- `references/`：长标准、例子、rubric、schema、来源。
- `scripts/`：确定性、重复性、易错或 API 调用。
- `assets/`：模板、样板、字体、图片等素材。

不要写 AI 已具备的基础常识；只写本任务的标准、偏好、坑点、顺序和边界。

### 5. 质量门

检查：名称一致；`description` 具体且 <1024 字符；正文 500-2000 字；资源被引用且有用途；无硬编码密钥、私有端点或本机绝对路径；删除、覆盖、发布、外部 API 等不可逆/对外操作先确认；能与其他 skill 组合。

### 6. 校验和汇报

用能力矩阵中的脚本校验；需要分发时再打包。校验失败先修复。最终只说明关键改动、设计决策、校验/打包状态和待用户决定事项；除非用户要求，不贴完整内容。
