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

## 脚本能力探测

Anthropic 官方 `skill-creator` 当前只把以下脚本视为官方能力：

```text
validate  -> scripts/quick_validate.py
package   -> scripts/package_skill.py
```

官方当前没有 `scripts/init_skill.py`。不要在本 skill 中把 `init_skill.py` 写成官方能力，也不要要求官方安装结果包含它。

在校验或打包前建立脚本能力矩阵：

1. 收集候选官方 creator 根目录：当前已加载的官方 skill、工具发现结果、用户提供的位置、或从官方仓库安装得到的目录。
2. 分别查找官方当前提供的脚本：`scripts/quick_validate.py` 与 `scripts/package_skill.py`。
3. 记录每个脚本的具体路径和来源。只要本次任务需要的官方脚本存在，即可继续；不要因缺少 `init_skill.py` 阻塞。
4. 如果本地系统内置 creator 提供 `init_skill.py`，只能把它作为“非官方兼容补位”。使用前说明来源；默认优先手动创建最小 skill 结构。

示例能力矩阵：

```text
validate  -> /path/to/skill-creator/scripts/quick_validate.py  # 官方当前版本
package   -> /path/to/skill-creator/scripts/package_skill.py   # 官方当前版本
init      -> manual minimal structure                         # 官方无 init 脚本
```

当前已知事实（2026-05-04 核对）：Anthropic 官方 GitHub `skills/skill-creator/scripts/` 下有 `quick_validate.py` 和 `package_skill.py`，没有 `init_skill.py`。

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

官方仓库当前版本没有 `scripts/init_skill.py`；这不是安装失败。新建 skill 时默认手动创建最小结构。

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

- 官方创建与迭代方法。
- 目录结构要求。
- 校验规则。
- 打包流程。

## 必须交接的步骤

创建或大幅重构 skill 前：

1. 加载官方 `skill-creator` 的 `SKILL.md`。
2. 把用户需求映射到官方步骤：
   - 理解具体使用例子。
   - 规划可复用内容。
   - 手动创建最小 skill 结构。
   - 编辑 skill。
   - 打包 skill。
   - 迭代改进。
3. 保留官方要求。只有当用户给出的环境约束让某一步不适用时，才跳过并说明原因。

## 新建 skill 结构

官方当前没有初始化脚本。需要创建新 skill 时，手动创建最小目录结构：

```text
{skill_name}/SKILL.md
{skill_name}/references/   # 仅在确有长资料时创建
{skill_name}/scripts/      # 仅在确有确定性脚本时创建
{skill_name}/assets/       # 仅在确有模板或素材时创建
```

手动创建后必须用官方 `quick_validate.py` 校验。只有当最终 skill 确实需要时，才保留 `scripts/`、`references/` 或 `assets/`。

若用户明确要求使用系统内置 `init_skill.py`，必须说明它不是官方当前仓库能力，而是兼容补位。

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

- 使用了脚本能力矩阵中的哪些官方脚本；若用了非官方兼容补位，明确说明。
- 修改了哪些文件。
- 校验结果。
- 生成的包路径，如果已打包。
- 只有在阻塞安装或真实使用时才提出开放问题。
