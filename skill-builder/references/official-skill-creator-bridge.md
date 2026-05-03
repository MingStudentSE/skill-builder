# 官方 skill-creator 桥接说明

使用本文件确保 `skill-builder` 调用官方 `skill-creator`，而不是重新发明一套创建器。

## 核心原则

不要在 skill 中写死用户本机绝对路径。

需要使用官方创建器时，通过以下方式之一定位：

- 当前环境已安装的 `skill-creator` skill。
- 当前工具/插件暴露的 skill 发现能力。
- 用户显式提供的官方 `skill-creator` 位置。
- 当前项目中已经解析出的相对路径或环境变量。

如果无法定位官方 `skill-creator`，先说明阻塞点，再使用用户当前环境中最接近的官方创建流程。

## 缺失时安装

如果当前环境没有 Anthropic 官方 `skill-creator`，先帮助用户安装官方版本，再继续创建 skill。

官方来源：

```text
https://github.com/anthropics/skills/tree/main/skills/skill-creator
```

推荐安装流程：

1. 确认用户允许从 GitHub 获取官方仓库。如果当前环境策略允许联网，可直接继续；如果需要用户确认，先确认。
2. 解析当前运行环境的 skills 安装目录：
   - 优先使用当前产品或工具暴露的 skills 目录。
   - 如果存在明确的环境变量或配置项，使用它。
   - 如果无法判断，询问用户希望安装到哪个 skills 目录。
3. 拉取官方仓库到临时目录或工作区缓存：

```bash
git clone --depth 1 https://github.com/anthropics/skills.git {temporary_directory}/anthropics-skills
```

4. 将官方目录复制到用户的 skills 目录：

```bash
cp -R {temporary_directory}/anthropics-skills/skills/skill-creator {skills_directory}/skill-creator
```

5. 校验安装结果至少包含：

```text
skill-creator/SKILL.md
skill-creator/scripts/init_skill.py
skill-creator/scripts/quick_validate.py
skill-creator/scripts/package_skill.py
```

6. 如果目标目录已有 `skill-creator`：
   - 先检查它是否来自 Anthropic 官方仓库。
   - 不要静默覆盖用户已有内容。
   - 需要更新或替换时，先说明差异并获得用户确认。

如果无法联网，要求用户下载或提供官方 `skills/skill-creator` 文件夹，然后继续安装校验。

## 分工

`skill-builder` 负责：

- 澄清需求。
- 形成 skill 构建简报。
- 选择设计模式。
- 设计触发正例和反例。
- 规划渐进式披露。
- 执行质量 rubric。
- 做安全检查。

官方 `skill-creator` 负责：

- 标准创建流程。
- 初始化脚手架。
- 目录结构要求。
- 校验规则。
- 打包流程。
- 迭代方法。

## 必须交接的步骤

创建或大幅重构 skill 前：

1. 加载官方 `skill-creator` 的 `SKILL.md`。
2. 把用户需求映射到官方步骤：
   - 理解具体使用例子。
   - 规划可复用内容。
   - 初始化 skill。
   - 编辑 skill。
   - 打包 skill。
   - 迭代改进。
3. 保留官方要求。只有当用户给出的环境约束让某一步不适用时，才跳过并说明原因。

## 初始化命令形态

在目标父目录执行：

```bash
python3 {skill_creator_dir}/scripts/init_skill.py {skill_name} --path {output_directory}
```

初始化后替换模板内容，并删除无用示例：

```text
scripts/example.py
references/api_reference.md
assets/example_asset.txt
```

只有当最终 skill 确实需要时，才保留 `scripts/`、`references/` 或 `assets/`。

## 校验命令形态

把 skill 目录作为参数传入：

```bash
python3 {skill_creator_dir}/scripts/quick_validate.py {skill_directory}
```

把 warning 当作优化建议，把 error 当作阻塞问题。

## 打包命令形态

从任意目录运行：

```bash
python3 {skill_creator_dir}/scripts/package_skill.py {skill_folder} {optional_output_directory}
```

除非用户指定其他位置，否则在 skill 附近使用 `dist/` 作为输出目录。

## 汇报格式

汇报时包含：

- 使用了官方创建器的哪个步骤。
- 修改了哪些文件。
- 校验结果。
- 生成的包路径，如果已打包。
- 只有在阻塞安装或真实使用时才提出开放问题。
