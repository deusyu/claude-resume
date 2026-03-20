# claude-resume

[English](README.md) | 中文

AI 驱动的简历管理系统 — 一份素材，N 份定制简历。

> 不只是一个 Skill，而是一个用 Claude Code Skill 构建完整工作流的示范项目。

基于 [Claude Code](https://claude.ai/code) Skill 体系，从结构化经历素材出发，根据不同目标岗位自动生成定制化 LaTeX 简历。

## 特性

- **素材驱动** — 经历、技能、荣誉存储在 Markdown 文件中，与简历呈现分离
- **AI 定制化** — 根据 JD 自动选择经历、调整呈现角度、重排优先级
- **多岗位适配** — 同一段经历在不同方向下有不同包装（后端/云原生/AI 方向等）
- **LaTeX 输出** — 专业排版，编译为 PDF，基于 [billryan/resume](https://github.com/billryan/resume) 模板

## 快速开始

### 前置条件

- [Claude Code](https://claude.ai/code) CLI
- XeLaTeX（macOS: `brew install --cask mactex`）

### 使用

```bash
# 1. 克隆项目
git clone https://github.com/deusyu/claude-resume.git
cd claude-resume

# 2. 启动 Claude Code
claude

# 3. 初始化素材库（二选一）
/init                          # 问答引导，一步步填写
/init <粘贴你的简历或经历文本>    # 自动解析结构化

# 4. 生成简历
/generate-resume 后端开发工程师

# 或指定 JD 文件
/generate-resume jobs/target-position.md

# 5. 编译 PDF
make en
```

## 目录结构

```
claude-resume/
├── experiences/                 # 经历素材库（你的数据在这里）
│   ├── profile.md               #   个人信息 + 教育经历
│   ├── work/                    #   工作详历（按公司分文件）
│   ├── projects.md              #   个人项目
│   ├── skills.md                #   技能清单
│   └── honors.md                #   荣誉 + 量化指标
├── jobs/                        # 目标岗位 JD
│   └── _template.md             #   JD 格式模板
├── output/                      # 生成的 PDF
├── .history/                    # .tex 历史备份
├── .claude/commands/            # Claude Code Skills
│   ├── init.md                  #   /init — 初始化素材库
│   ├── generate-resume.md       #   /generate-resume
│   └── add-experience.md        #   /add-experience
├── resume.tex                   # 当前 LaTeX 源文件
├── resume.cls                   # LaTeX 样式类
└── Makefile                     # 构建命令
```

## Skill 说明

### `/init [可选：粘贴简历文本]`

初始化 Skill。两种模式：

- **无参数** → 问答引导，一次问一个问题（姓名 → 教育 → 工作 → 项目 → 技能 → 荣誉）
- **带文本** → 解析你的现有简历或 LinkedIn 导出，自动生成 `experiences/` 结构化文件

### `/generate-resume [岗位名称或JD文件路径]`

核心 Skill。读取全部素材 → 分析岗位需求 → 选择和重组经历 → 生成 `resume.tex` → 编译 PDF。

示例：
```
/generate-resume AI 后端工程师
/generate-resume jobs/bytedance-backend.md
/generate-resume  （不带参数会询问目标岗位）
```

### `/add-experience [描述]`

用自然语言添加或更新经历素材。

示例：
```
/add-experience 我最近做了一个开源 RAG 项目，用 LlamaIndex 实现的，已经有 200 star 了
/add-experience 拿到了 AWS Solutions Architect 认证
```

## 自定义

### 经历素材格式

工作经历文件使用 YAML frontmatter + Markdown：

```markdown
---
company: 公司名
period: 2024/01 -- 至今
department: 部门
role: 角色
tags: [go, kubernetes, distributed-system]
---

# 项目名称
> 时间 | 角色

## 项目背景
...

## 核心工作
...

## 量化成果
...

## 技术栈
...
```

`tags` 字段帮助 AI 在生成时匹配岗位需求。

### LaTeX 样式

修改 `resume.cls` 可自定义布局、字体、间距。当前基于 [billryan/resume](https://github.com/billryan/resume)。

## 致谢

- LaTeX 模板基于 [billryan/resume](https://github.com/billryan/resume) 和 [imtsuki/resume](https://github.com/imtsuki/resume)
- 简历生成由 [Claude Code](https://claude.ai/code) Skill 驱动

## License

[MIT](LICENSE)
