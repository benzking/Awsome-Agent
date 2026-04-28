# GitHub 项目分析 SOP · 标准作业流程

> 版本：v1.0
> 创建日期：2026-04-28
> 适用场景：分析任意 GitHub 项目，输出标准化报告并存入知识库

---

## 一、流程总览

```
① 接收任务        →  用户发送 GitHub 项目链接
       ↓
② 获取项目        →  克隆或 Web 获取 README
       ↓
③ 深度阅读        →  抓取项目文档 + 关键源码
       ↓
④ 撰写报告        →  按模版输出 markdown 报告
       ↓
⑤ 文件组织        →  按目录结构存放
       ↓
⑥ 更新索引        →  README 作为项目索引
       ↓
⑦ 提交推送        →  Commit + Push 到 GitHub
```

---

## 二、详细步骤

### Step 1：接收任务

用户发送 GitHub 项目链接，格式如：
```
https://github.com/{owner}/{repo}
```

**提取关键信息：**
- `owner` = 项目所有者
- `repo` = 仓库名称
- 规范 slug 格式：`{repo}` 转小写，多个单词用 `-` 连接

---

### Step 2：获取项目

#### 方式 A：Git Clone（推荐用于完整分析）

```bash
# 清理旧目录（如果存在）
rm -rf /path/to/{repo-slug}
# 浅克隆（--depth 1）避免网络超时
git clone --depth 1 https://github.com/{owner}/{repo}.git
```

#### 方式 B：Web Fetch（克隆失败时备选）

```bash
# 抓取主页（英文版优先）
web_fetch(url="https://github.com/{owner}/{repo}")
web_fetch(url="https://github.com/{owner}/{repo}/blob/main/README_EN.md")
```

---

### Step 3：深度阅读

**抓取优先级（按顺序）：**

| 优先级 | 文件 | 原因 |
|--------|------|------|
| ⭐⭐⭐ | `README.md` / `README_EN.md` | 项目概述 |
| ⭐⭐ | `SKILL.md` / `CLAUDE.md` / `agent.py` / `main.py` | 入口逻辑 |
| ⭐⭐ | `package.json` / `setup.py` / `requirements.txt` | 依赖环境 |
| ⭐ | `examples/` 目录 | 快速上手 |

**若克隆失败：** 用 `web_fetch` 获取 README 内容，结合 GitHub 页面标题和描述补充信息。

---

### Step 4：撰写报告

**输出路径：**
```
/home/gly/.openclaw/workspace/Awsome-Agent/llm-wiki/{repo-slug}/report.md
```

**报告结构（按模版）：**

```
一、精要介绍（200字以内）
二、项目概览（Stars / Forks / 语言 / 协议 / 标签）
三、应用标签分类
四、核心特点（3-5 个要点）
五、工作流程（Mermaid 流程图或有序列表）
六、应用场景（3-5 个典型场景）
七、对比同类热门项目（横向对比表格）
八、安装指引（环境要求 + 步骤 + 快速验证）
九、项目结构（目录树）
十、关键文件清单
```

---

### Step 5：文件组织规范

**目录结构：**

```
Awsome-Agent/
├── README.md              # 项目索引（所有分析报告的入口）
├── github-analysis-template.md  # 模版文件（固定存放）
└── llm-wiki/              # 知识库根目录
    └── {repo-slug}/       # 每个项目一个文件夹
        └── report.md      # 分析报告（固定命名）
```

**命名规范：**
- 文件夹：`{repo-slug}` 小写（例：`knowledge-pipline`）
- 报告：`report.md`（固定，不可自定义）
- 模版：`github-analysis-template.md`（固定）

---

### Step 6：更新 README 索引

每次新增报告，必须同步更新仓库根目录的 `README.md`：

```markdown
## 📚 分析报告

### {项目名}

**{一句话介绍核心功能}** — {简要特点}。

👉 [查看完整分析报告](./llm-wiki/{repo-slug}/report.md)
```

---

### Step 7：提交并推送

```bash
cd /home/gly/.openclaw/workspace/Awsome-Agent

# 添加所有变更
git add -A

# 提交（规范 commit message）
git commit -m "docs: add {repo-slug} analysis report"

# 推送（处理网络抖动）
GIT_TERMINAL_PROMPT=0 git push -u origin main
```

**Commit Message 规范：**
- `docs: add {repo-slug} analysis report` — 新增报告
- `docs: update {repo-slug} report` — 更新报告
- `docs: restructure` — 目录重组（如本次）

---

## 三、格式要求汇总

| 内容 | 格式要求 |
|------|---------|
| 精要介绍 | ≤200字，含项目定位、核心功能、目标用户 |
| 项目概览 | 表格形式，字段固定（地址/协议/Stars/Forks/语言/标签） |
| 应用标签 | 从 Agent / Plugin / Skill / MCP / Tool / 应用 等选取 |
| 核心特点 | 3-5 个要点，每点 ≤1 行 |
| 工作流程 | Mermaid 或有序列表，不超过 10 步 |
| 应用场景 | 3-5 个，每个场景一句话描述 |
| 对比表格 | 3-5 个竞品，维度包括定位/能力/运行方式/存储/LLM支持/安装成本 |
| 安装指引 | 环境要求 → 安装步骤 → 快速验证 |
| Commit Message | `类型: 简短描述`，类型用 docs/feat/fix 等 |

---

## 四、关键教训（从实操中提炼）

> ⚠️ 以下问题曾在操作中遇到，需特别注意：

1. **克隆超时/EOF**
   - 原因：网络不稳定、仓库过大
   - 解决：使用 `--depth 1` 浅克隆，或改用 `web_fetch`

2. **文件已存在**
   - 原因：重复克隆到同一路径
   - 解决：先 `rm -rf {dir}` 再克隆

3. **未推送到远程就结束**
   - 要求：每次完成操作后，必须 `git push` 到 GitHub

4. **README 未同步更新**
   - 要求：新增报告后，必须同步更新 README 索引

5. **目录结构混乱**
   - 解决：严格按 `llm-wiki/{repo-slug}/report.md` 存放

---

## 五、检查清单

每次分析完成后，逐项核对：

```
☐ GitHub 仓库已获取（clone 或 web_fetch）
☐ README 已阅读（英文优先）
☐ 报告已写入 llm-wiki/{repo-slug}/report.md
☐ README.md 索引已更新（含一句话介绍 + 链接）
☐ git add + commit 已执行
☐ git push 已成功
```

---

_闪电 ⚡️ SOP v1.0 | 2026-04-28_