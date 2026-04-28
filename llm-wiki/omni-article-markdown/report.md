# omni-article-markdown 项目评测报告

> **项目仓库：** https://github.com/caol64/omni-article-markdown
> **评测版本：** 0.2.1
> **评测日期：** 2026-04-28
> **评测人：** 闪电 AI 助理 ⚡️💗

---

## 一、精要介绍

**omni-article-markdown（omnimd）** 是一款开源网页转 Markdown 工具，基于 Python 3.13 开发，通过 Reader → Extractor → Parser 三阶段流水线，将任意网页文章高质量转换为 Markdown 格式。支持 34 个专用 Extractor 覆盖主流内容平台，另有 curl / browser / scrollable_browser / feishu / toutiao / zhihu 等专属 Reader，CLI 开箱即用，亦可通过 Docker 部署。

---

## 二、项目概览

| 项目 | 信息 |
|------|------|
| **仓库地址** | https://github.com/caol64/omni-article-markdown |
| **版本** | 0.2.1（pyproject.toml） |
| **开源协议** | MIT |
| **编程语言** | Python 3.13+ ⚠️ |
| **CLI 命令** | `mdcli` |
| **Docker 镜像** | `caol64/omnimd` |
| **Stars / Forks** | 数据未获取，需手动补充（建议访问 GitHub 页面查看） |

**⚠️ Python 3.13 门槛：** 本项目明确要求 Python 3.13 及以上版本，低于此版本将无法安装或运行。安装前请务必通过 `python --version` 确认版本号。

---

## 三、核心特点

1. **专用 Extractor 矩阵：** 内置 34 个专用 Extractor，覆盖 Medium、CSDN、掘金、公众号、知乎、简书、InfoQ 等主流内容平台，针对性提取正文，最大限度保留原文结构与格式。
2. **多模式 Reader 引擎：** 支持 curl（静态页面）、browser（动态渲染）、scrollable_browser（长文章滚动加载）、feishu / toutiao / zhihu（平台专属）六种读取模式，适配不同网站的加载策略。
3. **零配置 CLI：** 一条命令 `mdcli <URL>` 即可完成抓取到转换全流程，无需手动指定网站类型，工具自动识别并选择最优解析策略。
4. **Docker 一键部署：** 提供官方 Docker 镜像 `caol64/omnimd`，无需配置 Python 环境，`docker run` 即可在任意系统运行。
5. **Fallback 降级机制：** 遇到不支持的网站时，自动启用通用 Extractor 降级处理；⚠️ 降级后解析质量可能有所下降，建议优先使用专用 Extractor 支持的平台。

---

## 四、技术架构

```
URL 输入
    │
    ▼
┌─────────────────┐
│     Reader       │  ← 读取网页内容
│ (curl / browser  │
│  / feishu 等)    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Extractor      │  ← 提取正文内容
│ (34 个专用 +     │
│  通用 fallback)  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│     Parser       │  ← 转换为 Markdown
└────────┬────────┘
         │
         ▼
   Markdown 输出
```

**关键文件：**

- `cli.py` — 命令行入口
- `reader.py` — 网页读取层
- `extractor.py` — 正文提取层
- `extractors/*.py` — 34 个专用 Extractor
- `parser.py` — Markdown 转换层
- `pyproject.toml` — 项目配置

**核心依赖：** requests、beautifulsoup4、html5lib、click、click-default-group、pip

**可选依赖：** playwright（需单独安装，用于 browser / scrollable_browser 模式渲染动态内容）

---

## 五、支持网站清单

### 专用 Extractor（34 个）

| 序号 | 网站 | 序号 | 网站 |
|------|------|------|------|
| 1 | Medium | 18 | 少数派 |
| 2 | CSDN | 19 | 语雀 |
| 3 | 掘金 | 20 | 腾讯云 |
| 4 | 公众号 | 21 | 人人都是产品经理 |
| 5 | 知乎（Extractor） | 22 | Jetbrains 博客 |
| 6 | 简书 | 23 | Claude 文档 |
| 7 | Towards Data Science | 24 | Anthropic |
| 8 | Quantamagazine | 25 | Meta 博客 |
| 9 | Cloudflare 博客 | 26 | Android Developers Blog |
| 10 | 阿里云 | 27 | Spring Blog |
| 11 | 微软文档 | 28 | Hackernoon |
| 12 | InfoQ | 29 | 领英博客 |
| 13 | 博客园 | 30 | 华尔街见闻 |
| 14 | 思否 | 31 | 苹果开发者文档 |
| 15 | 开源中国 | 32 | 百家号 |
| 16 | Forbes | 33 | Snowflake 博客 |
| 17 | 知乎专栏 | 34 | Google for Developers |

### 平台专属 Reader（不计入 Extractor 数量）

| 平台 | Reader 类型 | 说明 |
|------|-------------|------|
| 飞书 | feishu | 适配飞书文档特殊协议 |
| 今日头条 | toutiao | 适配头条号内容 |
| X Articles | X Articles | 适配 X 平台文章 |

### ⚠️ 不支持网站说明

对于**不在上述 34+3 列表中的网站**，工具会启用**通用 fallback Extractor** 进行正文提取。由于缺乏针对该网站结构的专项解析，**提取质量可能明显下降**。如需高质量转换，建议优先使用专用 Extractor 支持的平台。

---

## 六、竞品对比

| 特性 | omnimd | Jina AI Reader | Mercury Parser | Readability.js |
|------|--------|---------------|----------------|----------------|
| 专用网站支持 | 34 个 | 在线转换 | 需自建规则 | 无 |
| 本地 CLI | ✅ | ❌ | ✅ | ✅（Node.js） |
| Docker 支持 | ✅ | ❌ | ❌ | ❌ |
| 动态渲染 | ✅（Playwright） | ✅ | ❌ | ❌ |
| Python 实现 | ✅ | API | Node.js | Node.js |
| 知乎支持 | ✅（Extractor） | ❌ | ❌ | ❌ |
| 飞书支持 | ✅（Reader） | ❌ | ❌ | ❌ |
| MIT 协议 | ✅ | ✅ | ✅ | ✅ |

**对比结论：** omnimd 是当前唯一同时具备本地 CLI、Docker 部署、34 个专用 Extractor 且支持知乎/飞书等国内平台的开源方案，综合覆盖度领先。

---

## 七、安装指引

### 前置要求

- **Python 3.13+**（必须）

```bash
# 1. 验证 Python 版本
python --version
# 输出应类似：Python 3.13.3（低于 3.13 则无法使用）

# 2. 安装（pipx 推荐，避免依赖冲突）
pipx install omni-article-markdown

# 或通过 Docker（无需安装 Python）
docker pull caol64/omnimd
```

### 可选依赖：Playwright（动态渲染）

如需使用 `browser` / `scrollable_browser` 模式抓取单页应用（SPA）或懒加载内容，需额外安装 Playwright：

```bash
# 安装 Playwright 及其浏览器
pip install playwright
playwright install chromium
```

> **注意：** Playwright 为可选依赖。仅在抓取需要 JavaScript 渲染的动态页面时才需要安装。基础静态页面抓取无需安装。

---

## 八、使用方法

### 基础用法

```bash
# 单篇转换
mdcli https://example.com/article

# 输出到指定文件
mdcli https://example.com/article -o output.md

# 指定 Reader 模式（一般无需指定，工具自动选择）
mdcli https://example.com/article --reader browser
```

### Docker 用法

```bash
# 单篇转换
docker run --rm -v $(pwd):/workspace caol64/omnimd \
  mdcli https://example.com/article -o /workspace/output.md

# 交互模式（批量处理）
docker run --rm -it -v $(pwd):/workspace caol64/omnimd bash
```

### 批量处理示例

```bash
# 从文件读取 URL 列表，每行一个，批量转换到指定目录
cat urls.txt | while read url; do
  mdcli "$url" -o "./output/$(echo "$url" | md5sum | cut -d' ' -f1).md"
done

# 或使用 xargs 并行（最多 4 个并发）
cat urls.txt | xargs -P4 -I{} mdcli {} -o "./output/{}.md"

# Docker 环境下批量处理
cat urls.txt | while read url; do
  docker run --rm caol64/omnimd mdcli "$url"
done
```

---

## 九、进阶配置

### Reader 模式选择建议

| 场景 | 推荐 Reader |
|------|-------------|
| 静态博客/新闻 | `curl`（默认） |
| 需 JS 渲染的 SPA | `browser` |
| 长文章（滚动加载更多） | `scrollable_browser` |
| 飞书文档 | `feishu` |
| 头条号文章 | `toutiao` |
| X 平台文章 | `X Articles` |

### 环境变量

```bash
# 设置超时时间（秒）
export OMNI_TIMEOUT=30

# 设置 User-Agent
export OMNI_USER_AGENT="Mozilla/5.0 ..."
```

---

## 十、常见问题

**Q：提示 `Python 3.13+ is required` 怎么办？**
A：检查 `python --version`，如低于 3.13，需升级 Python 或使用 Docker 方式运行。

**Q：转换结果正文为空或很少？**
A：可能该网站不在专用 Extractor 支持列表中，走了通用 fallback。建议确认目标网站是否在支持清单（第五章）中。如属于动态加载页面，尝试加 `--reader browser` 参数。

**Q：Playwright 安装失败？**
A：在 macOS 上可尝试 `playwright install chromium --with-deps`；在 Linux 上可能需要额外系统依赖（webkit、ffmpeg 等）。

**Q：如何支持不在列表中的网站？**
A：可参考 `extractors/` 下现有 Extractor 的写法自行编写，通过 PR 贡献或本地使用。

---

## 十一、总结与建议

**优点：**

- 国内罕见的大规模专用 Extractor 矩阵（34 个），覆盖主流内容平台
- 知乎、飞书、今日头条等国内平台专属支持，填补同类工具空白
- CLI + Docker 双部署方式，本地/服务器均可用
- Python 实现，利于二次开发集成

**待改进：**

- 文档中未明确强调 Python 3.13 门槛，初学者易踩坑
- 缺乏对不支持网站 fallback 质量下降的明确提示
- Stars/Forks 等社区活跃度数据未同步，建议补充
- 可考虑增加 Python API 直接调用文档（当前仅 CLI）

**适用场景：**

- 知识库建设、内容备份、离线阅读
- 批量抓取结构化文章（内容农场、分析素材）
- 将国内平台内容迁移至 Hugo/Jekyll 等静态博客

**综合评分：** ⭐⭐⭐⭐（4/5）— 一款专注于内容提取、覆盖度出色的开源工具，推荐知识工作者和内容开发者使用。

---

_报告生成时间：2026-04-28 | 评测人：闪电 AI 助理 ⚡️💗_