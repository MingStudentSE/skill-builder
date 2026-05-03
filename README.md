# skill-builder

`skill-builder` 是一个用于设计、创建、审查、改进和打包 Agent Skills 的元技能。它会先按照三份参考资料提炼出的标准做需求澄清、模式选择和质量把关，再调用 Anthropic 官方 `skill-creator` 完成初始化、校验和打包。

## 适用场景

- 把重复工作流做成新的 skill
- 创建或改进 `SKILL.md`
- 优化 skill 的触发描述和反触发边界
- 整理 `references/`、`scripts/`、`assets/` 的内容分层
- 审查一个 skill 是否符合渐进式披露、安全和可移植性要求
- 检查本机是否已有官方 `skill-creator`，缺失时引导安装官方版本

## 目录结构

```text
./
├── SKILL.md
├── README.md
└── references/
    ├── official-skill-creator-bridge.md
    ├── skill-builder-standards.md
    ├── source-map.md
    └── source-books/
```

## 安装

本仓库根目录就是 skill 本体。将整个仓库目录复制到你的 Codex skills 目录，或按你当前 Agent 环境的 skills 安装方式导入。

示例命令形态：

```bash
cp -R . {skills_directory}/skill-builder
```

其中 `{skills_directory}` 替换成你自己的 skills 目录。

## 重要说明

本仓库不提交打包产物。需要 zip 时，请在本地通过官方 `skill-creator` 打包流程生成。

`skill-builder` 不内嵌官方 `skill-creator`。如果当前环境没有 Anthropic 官方 `skill-creator`，本 skill 会引导从官方仓库安装：

https://github.com/anthropics/skills/tree/main/skills/skill-creator

## 内容来源

本 skill 参考并整理了：

- 花叔 Claude Skills 白皮书
- Agent Skills 橙皮书
- The Complete Guide to Building Skills for Claude 的中文校订版
- Anthropic 官方 `skill-creator` 工作流

完整参考内容放在 `references/source-books/`，默认使用提炼后的标准文件，需要追溯依据或查原始例子时再读取全文。
