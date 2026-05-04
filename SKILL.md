---
name: skill-builder
description: 用于设计、创建、审查、改进和打包 Claude/Codex Agent Skills。用户想把工作流做成 skill、创建新的 SKILL.md、优化已有 skill、优化触发描述、审查 skill 结构、整理 references/scripts/assets、打包 skill zip，或构建可复用 Agent 能力时，应使用本 skill。
---

# Skill 构建器

## 定位

把本 skill 当作 **设计层 / 架构师 / 质量门**；把官方 `skill-creator` 当作 **工具链 / 校验器 / 打包器 / 评测器**。不要复制官方创建器能力，也不要把设计判断交给创建器。

## 必读

按任务读取：

- `references/skill-builder-standards.md`：设计模式、正文长度、质量门、优化已有 skill 的标准。
- `references/official-skill-creator-bridge.md`：官方 `skill-creator` 脚本能力矩阵、校验和打包流程。
- `references/source-map.md` 与 `references/source-books/`：仅在追溯依据、处理争议或更新标准时读取。

创建、重构、校验或打包前，先加载当前环境中的官方 `skill-creator`。不要在 skill 文件中写死用户本机绝对路径。

## 工作流

### 1. 判断任务类型

- 创建：把重复工作流、领域方法、工具用法做成新 skill。
- 优化：改进已有 `SKILL.md`、触发描述、资源结构、脚本或质量门。
- 审查：检查触发、结构、安全、可移植性和实际可用性。
- 打包：校验并生成可分发 zip。
- 基准测试：用正例和反例优化 `description`。

只有在目标、目标用户或触发示例缺失且无法合理推断时，才问最多三个澄清问题。

### 2. 先做构建 / 优化简报

编辑前形成简报：

- skill 名称与核心任务。
- 触发正例与不触发反例。
- 设计模式：Checklist、Options、Pipeline、Integration、Swarm、Distillation，或组合。
- 是否需要拆分：正文推荐 500-2000 字；超过 2000 字压缩/下沉；超过 3000 字拆分审查。
- 渐进式披露计划：`SKILL.md` / `references/` / `scripts/` / `assets/` 各放什么。
- 安全边界：删除、覆盖、外部 API、发布、凭据。
- 校验与打包计划。

优化已有 skill 时，先读现有文件和被引用资源，判断它属于“压缩、拆分、补质量门、补资源、修触发、修工具链”哪一类；优先做最小有效修改。

### 3. 与官方 skill-creator 配合

按 `references/official-skill-creator-bridge.md` 建立脚本能力矩阵，逐个探测：

```text
init      -> scripts/init_skill.py，如存在
validate  -> scripts/quick_validate.py
package   -> scripts/package_skill.py
```

不要假设三个脚本在同一目录。官方仓库当前版本可能没有 `init_skill.py`；系统内置版本可能没有 `package_skill.py`。

创建新 skill 时：有 `init_skill.py` 就用实际路径初始化；没有则手动创建最小 `SKILL.md` 结构。已有 skill 跳过初始化。

### 4. 编写或改进 skill

把 `SKILL.md` 写给后续 agent 执行，不写成人类说明书。

- Frontmatter：`name` kebab-case，`description` 同时说明做什么和什么时候用。
- `SKILL.md`：只放每次执行必需的核心流程、决策规则、质量门和安全边界。
- `references/`：长标准、例子、rubric、schema、来源说明。
- `scripts/`：确定性、重复性、易出错或外部 API 调用。
- `assets/`：模板、样板工程、字体、图片等输出素材。

不要把 AI 已具备的基础常识写进 skill；只写本任务的标准、偏好、坑点、顺序和边界。

### 5. 质量门

完成前检查：

- 文件夹名与 frontmatter `name` 一致，且为 kebab-case。
- `description` 具体、少于 1024 字符，能抓住正例并避开反例。
- 正文推荐 500-2000 字；超过 2000 字已压缩/下沉，超过 3000 字已拆分审查。
- 工作流足够具体，不是泛泛建议；关键输出有格式或验收标准。
- 被引用资源存在且有用途；无用模板示例已删除。
- 不硬编码 API key、token、secret、私有端点或用户本机绝对路径。
- 不可逆或对外可见操作必须先确认。
- 能与其他 skill 组合使用，不假设自己是唯一能力。

### 6. 校验、打包、汇报

用能力矩阵中的脚本校验：

```bash
python3 {quick_validate_script_path} {skill_directory}
```

需要可分发产物时再打包：

```bash
python3 {package_script_path} {skill_folder} {optional_output_directory}
```

校验失败先修复再汇报。最终只说明：修改的关键文件、设计决策、校验/打包状态、仍需用户决定的事项。除非用户要求，不贴完整 skill 内容。
