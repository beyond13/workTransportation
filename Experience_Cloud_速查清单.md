# Experience Cloud 速查清单：LWR + 共享规则

## 一、LWR vs Aura：先搞清楚现状

| 维度 | LWR (Lightning Web Runtime) | Aura 模板 |
|---|---|---|
| 组件模型 | 纯 LWC | Aura + LWC 混合 |
| 性能 | 快，服务端渲染 | 较慢 |
| 定制灵活度 | 受限于标准组件+LWC | 更灵活，但技术债重 |
| 新项目推荐 | 是（Salesforce官方主推） | 仅维护老站点 |
| SEO | 原生支持好 | 需额外配置 |

**入职第一周必问**：现有站点是哪种？如果是Aura老站点，是否有计划迁移到LWR？——这直接决定你要学的技术栈重点。

## 二、共享规则（Sharing Rules）—— 高风险区，务必搞懂

### 核心概念
- **Guest User（访客用户）**：未登录用户的默认身份，权限最容易被滥用/配错
- **Sharing Set**：把Portal/Community用户和特定记录关联的机制
- **Share Group**：基于用户资料分组共享
- **OWD (Org-Wide Defaults)**：外部对象的默认共享级别，Experience Cloud场景通常要设为Private，再用规则精细开放

### 排查清单（每次改权限前过一遍）
- [ ] 这个对象的OWD对External用户是什么？
- [ ] Guest User Profile是否有不必要的读写权限？（这是最常见的安全事故来源）
- [ ] 是否用了Sharing Set还是Apex Managed Sharing？两者调试方式不同
- [ ] 字段级安全（FLS）是否和对象级共享分开检查了？两者都要过，缺一不可
- [ ] 是否测试过用实际的External用户账号登录验证，而不是只看管理员视图？

### 常见坑
1. Guest User权限默认过宽，很多历史站点直接给了不必要的对象访问权限
2. 共享规则只解决"能不能看到记录"，不解决"能不能看到字段"——FLS必须单独配置
3. LWR和Aura对Guest User的一些行为（如是否允许写操作）处理方式不同，迁移时容易漏配

## 三、Agentforce / Gemini 相关（了解即可，不用精通）

- **Atlas Reasoning Engine**：Agentforce底层推理引擎，2026年6月起原生支持Gemini 3.5 Flash
- 问清楚团队现状：Agentforce是POC阶段还是生产环境在用
- 如果涉及Experience Cloud + Agentforce结合（如站点内嵌AI客服代理），重点确认数据是否涉及Guest User可访问范围——这是新的安全风险点，因为Agent可能会访问到超出预期的数据

## 四、入职前两周行动项

1. 找到现有站点的Setup → Digital Experiences → 确认模板类型
2. 检查一次Guest User Profile的权限（只看不改，先了解现状）
3. 问清楚团队用的是Sharing Set还是Apex Sharing
4. 如果有Agentforce相关工作，问清楚数据访问边界谁在把关
