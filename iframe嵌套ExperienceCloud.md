# iframe 嵌套 Experience Cloud 专题记录

> 本文件持续更新，记录iframe嵌套Experience Cloud相关的技术细节、坑、解决方案。

## 1. 基本原理

官网通过iframe嵌入Experience Cloud页面，让用户体验上"不跳转"，同时通过Knowledge独立URL让搜索引擎仍能收录（详见项目SEO方案）。

```
官网（母页面）
   └── iframe
        └── Experience Cloud 站点（子页面）
```

## 2. Salesforce端（子页面）限制

### Clickjack Protection
- 默认值：不允许任何页面以frame方式显示（`X-Frame-Options: DENY` 或 LWR站点的 `frame-ancestors 'none'`）
- 这是服务端安全机制，跟iframe/HTML代码写得对不对无关，配错了浏览器直接拒绝渲染
- 配置位置：Setup → Digital Experiences → 站点 → Administration → Security → Clickjack Protection
- 正确做法：改为"允许被列出的域名嵌入"，把官网域名加入白名单——**不要设成允许所有域名**，那样等于放弃防护

### Guest User 权限
- iframe内容要正常显示，Guest User Profile需要有对应Knowledge/对象的Read权限
- 排查方法：无痕窗口直接访问Experience Cloud的URL，不经过iframe，看能否正常打开

## 3. 官网端（母页面）限制

| 限制项 | 现象 | 排查方法 |
|---|---|---|
| 母页面CSP的frame-src | 母页面不允许加载该来源的iframe，浏览器拦截 | DevTools → Network → 首页响应头看CSP |
| Mixed Content | HTTPS页面嵌HTTP iframe被block | 确认双方协议一致，都用HTTPS |
| 第三方Cookie限制 | iframe能加载，但涉及登录态/session的功能异常（如刷新状态丢失）| 生产环境的坑，demo阶段测不出来，需真实浏览器环境验证 |
| sandbox属性配置过严 | iframe内交互组件（搜索框、跳转）失效 | 检查`<iframe sandbox="...">`是否遗漏`allow-scripts`等必要值 |

## 4. 排查顺序（遇到问题时）

1. iframe完全空白 → 先查Salesforce端 X-Frame-Options / CSP frame-ancestors
2. iframe能显示但交互/状态不正常 → 查第三方Cookie限制、sandbox属性
3. iframe完全没发出请求 → 查官网自己的CSP frame-src

## 5. 待验证/未解决问题

- [ ] 公司官网当前是否已设置CSP或X-Frame-Options，会不会限制自己嵌入外部iframe
- [ ] 生产环境Guest User权限是否已经放开（尚未验证，只是"据说"）
- [ ] 是否涉及登录态内容嵌入iframe？如果涉及，第三方Cookie问题需要提前评估方案（比如是否需要SameSite=None; Secure的cookie设置）

## 6. Demo跑通步骤（供参考）

1. Developer Edition org，启用Digital Experiences，建LWR站点
2. Security设置里放开Clickjack Protection，加入测试域名白名单
3. 启用Guest User Access，发布一篇Knowledge文章
4. 简单HTML里写 `<iframe src="站点URL"></iframe>` 本地测试
