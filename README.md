# workTransportation

> 入职后的技术学习、项目复盘与 Salesforce / Experience Cloud 实践记录。
>
> 本仓库中的日记与调查笔记保留了当时的思考过程；涉及权限、安全、生产发布的内容，必须以实际 Org 配置、项目设计书和团队确认结果为准。

## 从这里开始

- [工作记录与技术笔记总结](./工作记录与技术笔记总结.md)：全仓库主题、成果、风险与下一步行动的浓缩版。
- [Experience Cloud FAQ Site 自动化 IT 测试攻略](./Experience_Cloud_FAQ_Automated_Testing_Guide.md)：以 Colab 管理测试数据，以 Playwright 验证访客搜索、分类和文章详情。
- [Salesforce Knowledge Migration Sandbox Runbook](./runBook.md)：4000 条 FAQ 的小批量验证到批量迁移流程。
- [Experience Cloud 速查清单](./Experience_Cloud_速查清单.md)：LWR、共享规则、Guest User 与入职初期排查重点。

## 工作复盘

| 日期 | 记录 | 主题 |
|---|---|---|
| 2026-07-13 | [入职第一天复盘](./2026-07-13_入职第一天复盘.md) | Knowledge 对外公开、iframe + SEO、Guest User、Figma 与 Markdown 工作流 |
| 2026-07-14 | [复盘日报](./2026-07-14_复盘日报.md) | Figma 到 CSS 的还原、Auto Layout、字体与间距、AI 辅助开发边界 |

## Salesforce / Experience Cloud

| 文件 | 内容 |
|---|---|
| [runBook.md](./runBook.md) | Knowledge、Category 与 Experience Cloud FAQ 的 Sandbox 验证和迁移 Runbook |
| [Experience_Cloud_速查清单.md](./Experience_Cloud_速查清单.md) | LWR / Aura、OWD、Sharing Set、Guest User 与 Agentforce 的检查项 |
| [iframe嵌套ExperienceCloud.md](./iframe嵌套ExperienceCloud.md) | iframe 嵌入的原理、CSP / Clickjack / Cookie / sandbox 排查顺序 |
| [Salesforce Data Category Group XML の全体構造まとめ.md](<./Salesforce Data Category Group XML の全体構造まとめ.md>) | Data Category Group XML、层级命名和 `Knowledge__DataCategorySelection` 映射 |
| [FAQ_Knowledge_with_Categories.reportType-meta.xml](./FAQ_Knowledge_with_Categories.reportType-meta.xml) | 以 `Knowledge__ka` 为主对象、关联 `Knowledge__kav` 的 Custom Report Type POC 元数据 |
| [Experience_Cloud_FAQ_Automated_Testing_Guide.md](./Experience_Cloud_FAQ_Automated_Testing_Guide.md) | Colab + Playwright 自动化访客 FAQ IT 测试 POC、测试范围与 CI 演进 |
| [listview確認](./listview確認) | FAQ 大量导入后的 List View、Record Page、Page Layout 与 Record Type 检查思路 |
| [Salesforce 富文本图片迁移标准作业流程](<./Salesforce 富文本图片迁移标准作业流程>) | `ContentVersion` → `ContentDistribution` → HTML 链接替换的双语图片迁移 SOP |

### iframe 资料说明

`iframe嵌套ExperienceCloud.md` 是当前的安全基线：生产环境应仅允许指定官网域名嵌入。`0723 iframe关联确认` 保留的是早期测试记录，其中“允许任意页面嵌入”的做法只可用于隔离测试，不能作为生产配置。

## 前端 / Figma / CSS

| 文件 | 内容 |
|---|---|
| [20260715](./20260715) | Figma Dev Mode、图层、文本与图标尺寸、`span` / `img`、Computed、盒模型与 CSS 优先级问答记录 |
| [0722](./0722) | Knowledge `ArticleBody` CSV 的 RFC 4180 转义与 Data Loader 导入检查点 |
| [0723 _Google Colaboratory](<./0723 _Google Colaboratory>) | Google Colab 与云端 Linux 工作目录说明 |
| [0723 iframe关联确认](<./0723 iframe关联确认>) | iframe 初步测试记录；生产配置请优先参考上面的安全基线 |

## 图片资料

- [Custom Report Type 配置截图](./カスタムレポートタイプ.png)
- [Gemini 使用界面截图](./11111.png)

## 记录原则

1. 原始日记、聊天记录和实验笔记保留上下文，不覆盖历史结论。
2. 将已在 Sandbox 或实际页面验证的结论，与待验证假设分开记录。
3. 权限、Guest User、CSP、Clickjack、SEO 与批量数据迁移的变更，先小范围验证，再扩大执行。
4. AI 可以辅助转换、整理和生成草稿；字段映射、API Name、安全边界和发布判断必须由工程师确认。
