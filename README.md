# repwiki (repowiki-generator)

> 版本以 [`skills/repowiki-generator/VERSION`](skills/repowiki-generator/VERSION) 为准（当前 **1.0.0**）。skill 每次使用前会自检远端最新版，落后时会提示更新。

`repwiki` (repowiki-generator) 是一个专为 AI 编码助手设计的高级 **Agent Skill**。它致力于将任何陌生的代码仓库结构化、自动化地分析并生成两层结构的知识库文档：**Tier 1 知识卡片（速览）** + **Tier 2 深度主题文档（详细分析 + Mermaid 图表）**。

最终还会自动生成一个基于 Docsify 的轻量本地查看器，让你可以通过 `npx docsify serve` 一键预览和阅读生成的完整 Wiki 树。

## 安装

你可以通过 `skills` 包管理器全局安装此 skill：

```bash
npx skills add hwlv/repwiki --skill repowiki-generator -g
```

装好后，在支持 Agent Skills 的 AI 编码工具（例如 Claude Code、Trae 等）中，可以直接指示 Agent：
> 「用 repowiki-generator 分析这个项目并生成 Wiki 知识库」

**更新到最新版**：
- 更新全部全局 skill：`npx skills update -g`
- 仅更新此 skill：`npx skills add hwlv/repwiki --skill repowiki-generator -g`

---

## 它做什么

`repowiki-generator` 通过确定性的阶段化（Phases）流程，安全、完整地分析代码库并生成文档：

| 阶段 | 核心任务 | 说明 |
|---|---|---|
| **Phase 0** | 初始化与自检 | 确定输出目录（默认 `.repowiki/` 或 `.trae/repowiki/` 等），建立输出结构 |
| **Phase 1** | 项目探索 | 检测项目类型与主要开发语言（中文/英文），合并 `.gitignore` 与 `.wikiignore` 过滤规则 |
| **Phase 2** | Tier 1 — 模块发现与知识卡片 | 进行模块划分，生成文件清单 `file_manifest.json`，检查代码覆盖率底线，并动态生成模块知识卡片 |
| **Phase 3** | Tier 2 — 深度主题生成 | 依据业务入口反推进行聚类，在 `entry_clusters.json` 中分类，生成 3-4 张精细 Mermaid 架构图的独立深度文档 |
| **Phase 3.5** | 覆盖率自审计 | 强制性质量门控，生成 `audit_report.json`。如未达到门控指标（例如 top-5 文件 100% 覆盖），将回退重做 |
| **Phase 4** | 查看器生成 | 生成 Docsify 兼容的网页文件（`README.md`、`index.html`、`_sidebar.md`），可一键本地跑服务预览 |
| **Phase 4.5** | 格式校验 | 执行 15 项以上的格式检查（Mermaid 关键字与符号合法性、`file://` 链接有效性等），确保零渲染错误 |

---

## 质量红线与门控

本 Skill 强制执行多项**机械门控与红线规则**：
- **Mermaid 规范**：Mermaid 代码块首行不得使用 ` ```sequenceDiagram ` 而是统一使用 ` ```mermaid ` 并在块内首行声明图表类型。所有特殊符号均须使用引号转义，避免渲染崩溃。
- **覆盖率要求**：项目字节覆盖率 $\ge 70\%$，文件数覆盖率 $\ge 80\%$，Top 5 按字节大小的文件必须 $100\%$ 被读取和分析。
- **引用完整性**：$\ge 10\text{ KB}$ 的源文件在 Tier 2 文档中必须以相对路径 `file://` 的形式被引用和链接，且所有链接必须在项目内真实有效。
- **拒绝占位符**：禁止在生成的文档中使用 `TODO`、`待补充`、`略`、`TBD`、`WIP` 等禁用词。

---

## 预览与部署

生成完成后，你可以直接在输出目录下运行 docsify：

```bash
npx docsify serve .repowiki
```

然后在浏览器中打开 `http://localhost:3000` 即可交互式浏览生成的 Wiki。

## License

[MIT](LICENSE)
