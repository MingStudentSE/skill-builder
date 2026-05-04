# 官方 skill-creator 桥接说明

使用本文件确保 `skill-builder` 调用官方 `skill-creator`，而不是重新发明一套创建器。

## 核心原则

不要在 skill 中写死用户本机绝对路径。

需要使用官方创建器时，通过以下方式之一定位：

- 当前环境已安装的 `skill-creator` skill。
- 当前工具/插件暴露的 skill 发现能力。
- 用户显式提供的官方 `skill-creator` 位置。
- 当前项目中已经解析出的相对路径或环境变量。

如果无法定位 Anthropic 官方 `skill-creator`，不要直接用相似流程替代。先按“缺失时安装”从官方仓库下载安装到当前 skills 目录；只有在无法联网、无法确定安装目录或用户拒绝安装时，才说明阻塞点，并请求用户提供官方 `skill-creator` 文件夹或安装位置。

## 脚本能力探测（不要假设同目录）

官方 `skill-creator` 的脚本集会随版本与运行环境变化；本地环境也可能同时存在多个 `skill-creator` 变体。不要假设 `init_skill.py`、`quick_validate.py`、`package_skill.py` 一定位于同一个目录，也不要把某个目录缺少单个脚本误判为整个 creator 不可用。

在创建、校验或打包前，逐个探测所需脚本：

1. 收集候选 creator 根目录：
   - 当前环境已加载或可发现的 `skill-creator` skill 目录。
   - 当前产品或系统内置的 `.system/skill-creator` 目录。
   - 用户显式提供的官方 `skill-creator` 目录。
   - 从官方仓库临时下载或安装得到的目录。
2. 对每个需要的能力分别寻找脚本，而不是只选一个 `{skill_creator_dir}`：
   - 初始化：查找 `scripts/init_skill.py`。
   - 快速校验：查找 `scripts/quick_validate.py`。
   - 打包：查找 `scripts/package_skill.py`。
3. 为本次任务建立“脚本能力矩阵”，记录每个能力使用的具体脚本路径与来源。
4. 优先使用 Anthropic 官方仓库当前版本中存在的脚本；如果官方当前版本缺少某项能力，但系统内置 creator 提供该脚本，可以把系统内置脚本作为兼容补位，并在汇报中说明。
5. 只要某个任务需要的脚本存在，即可继续该任务；不要因为其它非必需脚本缺失而阻塞。
6. 如果多个候选目录都提供同一脚本，优先级为：用户明确指定目录 > 当前已加载的官方 skill > 官方仓库当前版本 > 系统内置兼容目录。若来源不确定，先说明判断依据。

示例能力矩阵：

```text
init      -> /path/to/.system/skill-creator/scripts/init_skill.py        # 兼容补位
validate  -> /path/to/skill-creator/scripts/quick_validate.py            # 官方当前版本
package   -> /path/to/skill-creator/scripts/package_skill.py             # 官方当前版本
```

当前已知事实（2026-05-04 核对）：Anthropic 官方 GitHub `skills/skill-creator/scripts/` 下有 `quick_validate.py` 和 `package_skill.py`，没有 `init_skill.py`；某些系统内置 `skill-creator` 可能有 `init_skill.py` 与 `quick_validate.py`，但没有 `package_skill.py`。因此桥接流程必须按脚本逐个探测。

## 缺失时安装

如果当前环境没有 Anthropic 官方 `skill-creator`，本 skill 必须优先引导或执行从官方仓库安装官方版本，再继续创建、优化、校验或打包 skill。不要用非官方来源或“最接近”的本地流程替代，除非用户明确要求。

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

5. 校验安装结果时不要要求所有脚本同时存在于同一目录；改为建立脚本能力矩阵。至少确认：

```text
skill-creator/SKILL.md
skill-creator/scripts/quick_validate.py
skill-creator/scripts/package_skill.py
```

如果官方仓库当前版本没有 `scripts/init_skill.py`，不要判定安装失败。只有在本次任务确实需要初始化新 skill 时，才继续从其它官方/系统内置候选目录寻找 `init_skill.py`，或改用手动创建最小结构并说明原因。

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

初始化是可选能力，必须先在能力矩阵中确认 `init_skill.py` 的实际位置。不要默认它存在于官方仓库当前版本的 `skill-creator/scripts/` 中。

若找到初始化脚本，在目标父目录执行：

```bash
python3 {init_script_path} {skill_name} --path {output_directory}
```

若没有找到初始化脚本，但需要创建新 skill，则手动创建最小目录结构：

```text
{skill_name}/SKILL.md
{skill_name}/references/   # 仅在确有长资料时创建
{skill_name}/scripts/      # 仅在确有确定性脚本时创建
{skill_name}/assets/       # 仅在确有模板或素材时创建
```

手动创建后仍必须用能力矩阵中的 `quick_validate.py` 校验。

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
python3 {quick_validate_script_path} {skill_directory}
```

把 warning 当作优化建议，把 error 当作阻塞问题。

## 打包命令形态

从任意目录运行：

```bash
python3 {package_script_path} {skill_folder} {optional_output_directory}
```

除非用户指定其他位置，否则在 skill 附近使用 `dist/` 作为输出目录。

## 汇报格式

汇报时包含：

- 使用了脚本能力矩阵中的哪些脚本，以及哪些能力缺失或由兼容脚本补位。
- 修改了哪些文件。
- 校验结果。
- 生成的包路径，如果已打包。
- 只有在阻塞安装或真实使用时才提出开放问题。
