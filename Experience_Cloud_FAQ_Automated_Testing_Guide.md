# Experience Cloud FAQ Site 自动化 IT 测试攻略

> 目标：用真实浏览器验证访客访问 FAQ 站点的关键路径——首页、搜索、分类、文章详情与公开边界。第一阶段只在 Sandbox / 测试站点执行只读检查，不执行发布、编辑或删除。

## 工具分工

| 工具 | 适合做什么 |
|---|---|
| Google Colab | 维护测试案例 CSV、生成测试数据、汇总结果和快速查看截图 / HTML 报告 |
| Playwright | 打开浏览器、点击页面、输入关键词、断言 URL / 标题 / 正文、保存失败证据 |
| GitHub Actions 或专用 Runner | POC 稳定后的定时与提交后自动执行 |

建议把 Colab 当作“测试数据与结果分析工作台”，而不是长期 CI 运行器：会话和运行环境是临时的，长期定时任务应放在受控的 CI / Runner。

## 最小可行 POC

### 1. 准备测试案例

在 Colab 或仓库维护 `faq_cases.csv`。每一行代表一个访客应能完成的检查：

```csv
case_id,keyword,category_path,expected_title,expected_url_fragment
FAQ-001,保険,商品/保険,保険に関するよくある質問,/s/article/
FAQ-002,住所変更,各種手続き/住所変更,住所変更の手続き,/s/article/
```

分类路径写业务名称；真正用于定位的 URL、链接名或 `data-testid` 要以测试站点的实际页面为准。

### 2. 初始化 Playwright

```bash
npm init playwright@latest
npx playwright test --project=chromium
```

把站点地址放在环境变量中，避免写死在代码里：

```powershell
$env:BASE_URL = 'https://your-sandbox.example.com/s/'
npx playwright test
```

### 3. 编写一个访客路径测试

下面是结构示例。将链接文本、标题和选择器替换成实际 FAQ Site 的可访问元素；优先使用 `getByRole()`、`getByLabel()` 或团队约定的 `data-testid`，不要依赖易变的 CSS 层级。

```ts
import { test, expect } from '@playwright/test';

test('Guest user can find and open an FAQ', async ({ page }) => {
  await page.goto(process.env.BASE_URL!);
  await expect(page.getByRole('main')).toBeVisible();

  await page.getByRole('searchbox').fill('保険');
  await page.keyboard.press('Enter');
  await expect(page.getByRole('link', { name: /保険に関するよくある質問/ })).toBeVisible();

  await page.getByRole('link', { name: /保険に関するよくある質問/ }).click();
  await expect(page).toHaveURL(/\/s\/article\//);
  await expect(page.getByRole('heading', { name: /保険に関するよくある質問/ })).toBeVisible();
});
```

访客测试必须在未登录的新浏览器上下文中运行；不能复用管理员登录态，否则无法验证 Guest User 的真实可见范围。

## 建议优先自动化的 5 类测试

| 编号 | 场景 | 关键断言 |
|---|---|---|
| 1 | FAQ 首页可打开 | 页面主区域、导航与搜索框可见 |
| 2 | 关键字搜索 | 输入关键字后出现预期文章链接 |
| 3 | Category 层级导航 | 点击分类后出现预期子分类或文章 |
| 4 | 文章详情 | URL、文章标题、正文关键字正确 |
| 5 | 公开边界 | 非公开测试文章不能在访客搜索和直接 URL 中访问 |

第 5 类仅针对专门准备的测试数据验证；不要用生产数据做“猜 URL”的权限探测。

## 失败时保留什么证据

在 `playwright.config.ts` 启用失败时的截图、视频和 Trace，并保留 HTML Report：

```ts
use: {
  screenshot: 'only-on-failure',
  video: 'retain-on-failure',
  trace: 'retain-on-failure',
},
reporter: [['html', { open: 'never' }]],
```

执行后用以下命令查看报告：

```bash
npx playwright show-report
```

Colab 可读取结果 CSV，汇总 `case_id`、通过/失败、失败步骤、报告链接和截图路径，形成每日 IT 测试记录。

## 推荐演进顺序

1. 先在 Sandbox 为 5 条高频 FAQ 建立上面的 POC。
2. 用 CSV 扩展到不同 Category、语言和公开状态。
3. 对稳定页面补充截图比对；对页面改版前后保留基线。
4. 将脚本、CSV 和报告说明提交 GitHub。
5. 在 GitHub Actions / 专用 Runner 中按提交后或每日定时运行，并把失败报告作为构建产物保存。

## 安全与运用边界

- 不把 Org 密码、Session、访问令牌写入 Notebook、CSV 或 Git；生产 CI 使用受控的 Secret Store。
- POC 默认只读：不测试发布、删除、Data Category 改动或用户权限改动。
- 分别记录“页面功能失败”“Guest User 权限失败”“选择器因 UI 改版失效”，避免把不同问题混为一类。
- 若站点嵌在官网 iframe 内，额外测试嵌入页的 CSP / Clickjack、Cookie 与移动端视口；文章独立 URL 仍应单独测试。

## 完成标准

一轮测试至少输出：测试案例版本、目标环境、执行时间、通过/失败数、失败截图 / Trace、以及需要 Salesforce 配置团队处理的事项。这样它才是可追踪的 IT 测试，而不只是一次浏览器演示。

参考：[Google Colab](https://colab.research.google.com/)、[Playwright 官方入门](https://playwright.dev/docs/intro)、[Playwright Assertions](https://playwright.dev/docs/test-assertions)。
