# Salesforce Knowledge Migration Sandbox Runbook v1.0

> Purpose：FAQ Knowledge Migration Sandbox 验证  
> Goal：验证 Excel → Knowledge → Category → Experience Cloud FAQ 页面全流程  
> Scope：4000 条 FAQ 数据导入验证  
> Working Style：Gemini 辅助生成 + Data Loader 导入 + Salesforce 验证

---

# 0. 今日工作目标

今天不追求：

- 4000 条 FAQ 内容全部人工确认
- 正式上线质量

今天目标：

完成：

```
Excel FAQ
 ↓
Mapping
 ↓
Knowledge Import
 ↓
Category Association
 ↓
Experience Cloud FAQ Display
```

确认 Pipeline 可以运行。

---

# 1. 准备资料确认（开始前）

确认已有：

## 输入资料

□ FAQ Excel

内容：

- Question(Q)
- Answer(A)
- Category
- Product
- Scene


□ Category Excel

内容：

- 五棵 Category Tree
- Level 信息


□ Existing Metadata XML

内容：

- Category Group
- Category
- API Name


□ Salesforce Knowledge 字段信息

确认：

- Title
- ArticleBody
- Language
- Category Mapping 字段


---

# 2. Metadata 确认

## Step 2-1

备份旧 Metadata

保存：

```
Metadata_Backup/
```

---

## Step 2-2

确认 Category Group

原则：

第一层 API Name：

必须保持旧 Metadata。

不要修改。

---

## Step 2-3

第二层以后 API Name

今天先确认：

是否存在代码依赖。

确认方法：

搜索：

```
DataCategory

Category API Name

LWC

Apex
```

---

## Decision

如果：

存在代码引用：

保持旧 API Name。

如果：

无代码依赖：

考虑编号规则：

```
SCN_010
SCN_010_020
SCN_010_020_030
```

---

# 3. 建立 Mapping

## 3-1 Field Mapping

创建：

```
FieldMapping.xlsx
```

格式：

| Excel | Salesforce |
|-|-|
| Question | Title |
| Answer | ArticleBody |
| Product | Category |
| Scene | Category |


确认：

Q：

↓

Title


A：

↓

ArticleBody

---

## 3-2 Category Mapping

创建：

```
CategoryMapping.xlsx
```

格式：

| Excel Value | API Name |
|-|-|
| 契約照会 | xxx |
| 商品A | xxx |

原则：

禁止 Gemini 自己猜。

---

# 4. Gemini 处理流程

输入：

```
FAQ Excel

Category Excel

Old Metadata

Field Mapping

Category Mapping
```

要求输出：

---

## Output 1

Knowledge CSV

要求：

- Data Loader 可直接导入
- UTF-8
- 无辅助列


---

## Output 2

Mapping Report

包含：

|项目|结果|
|-|-|
|已匹配|OK|
|无法判断|Need Review|


---

## Output 3

Risk Report

检查：

- API Name
- Category
- 空值
- 重复

---

# 5. 第一轮导入（小批量）

不要直接4000。

先：

```
10～20条
```

覆盖：

□ Scene Tree

□ Product Tree 1

□ Product Tree 2

□ Product Tree 3

□ Product Tree 4


---

# 6. Data Loader Import

导入前：

确认：

□ CSV Encoding UTF-8

□ 字段 Mapping 正确

□ Category API Name 正确


---

导入后确认：

Knowledge：

状态：

Draft

---

# 7. 第一轮验证

后台确认：

## Knowledge

□ Title 正常

□ ArticleBody 正常

□ Category 正常


---

## Experience Cloud

操作：

```
首页

↓

一级分类

↓

二级分类

↓

最终分类

↓

QA

↓

详情
```

确认：

□ 分类按钮正常

□ QA 数量正常

□ Answer 正常显示

---

# 8. Publish 测试

先不要发布全部。

选择：

10～20条。

手工 Publish。

确认：

Guest User:

可以看到。


---

# 9. 批量 Publish

确认测试成功后。

使用：

Apex Anonymous：

```apex
KbManagement.PublishingService.publishArticle(
    KnowledgeArticleId,
    true
);
```

原则：

每批：

50～100条。

不要一次4000。

---

# 10. 最终4000条导入

流程：

```
Generate CSV

↓

Data Loader

↓

Verify

↓

Publish
```

---

# 11. 问题排查

## 页面没有QA

检查：

1. Knowledge 是否 Published

2. Category 是否叶子节点

3. Guest Permission


---

## 分类按钮有，但是没有数据

检查：

1. Leaf Category API Name

2. Knowledge Category Mapping


---

## 页面层级错误

检查：

1. Parent Relationship

2. Metadata XML


---

## 数据乱码

检查：

CSV:

UTF-8


---

# 12. 每日汇报模板

## Completed

- 
- 
- 


## Current Status

Example：

Metadata 70%

Mapping 50%

Import 0%


## Risk

- API Name
- Category Mapping


## Next Action

- 
- 


---

# 13. 项目完成标准

以下全部 OK：

□ Metadata 正常

□ Category Tree 正常

□ Knowledge 导入成功

□ Category 关联成功

□ Experience Cloud 显示正常

□ Guest User 可访问

□ 发布流程确认

□ Rollback 方法确认


---

# Final Principle

AI：

负责：

重复转换。

Engineer：

负责：

规则确认。

不要让 AI 猜。

不要一次性赌4000条。

先验证 Pipeline。

再扩大规模。
