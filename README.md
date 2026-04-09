---
title: "我自己写的一款工具: LeakDetector"
date: 2026-01-31 17:15:00
tags: [工具, 渗透测试, Python]
categories: [作品]
excerpt: 一款专为红队渗透测试人员和安全研究员设计的自动化信息泄露侦察工具。
---

# LeakDetector

![License](https://img.shields.io/badge/license-MIT-blue.svg) ![Python](https://img.shields.io/badge/python-3.8%2B-blue) ![Platform](https://img.shields.io/badge/platform-Windows-lightgrey)

**LeakDetector** 是一款基于 Bing 搜索引擎高级语法（Dork）结合 Playwright 的信息泄露特征检测工具。支持针对指定目标的自动化敏感信息挖掘分析。

> **免责声明**
> 本工具仅供授权的安全测试、漏洞挖掘和企业内部安全审计使用。严禁用于非法入侵、数据窃取或任何未经授权的恶意行为。

---

## 核心特性

### 1. Dork 侦察策略
内置六层侦察策略 (P1-P6)：
* **API & 配置泄露**: 检测 Swagger UI, Spring Boot Actuator, `.env`, `application.yml` 等路径暴露。
* **信息文件挖掘**: 检索 `xlsx`, `docx`, `pdf` 等文档，并识别内部包含的“身份证”、“手机号”、“工资”等特征词。
* **后台与组件识别**: 匹配常见 OA 系统 (泛微/致远)、后台管理入口及 Jenkins/GitLab。
* **漏洞特征探测**: 检索 SQL 注入报错页面、文件上传入口及 Webshell 特征。

### 2. 浏览器渲染与抓取引擎
集成 Playwright 内核：
* 支持调用 GUI 窗口人工介入完成 Turnstile / ReCAPTCHA 验证，缓解 IP 请求封禁限制。
* 支持动态渲染页面内容的完整抓取。

### 3. 内容分析与提取
针对检索结果进行内容级验证解析：
* **结构化文件读取**: 支持下载并读取 Excel/CSV/PDF 内容（含 Excel 多 Sheet 提取）。
* **敏感信息正则匹配**: 内置身份证号、手机号、邮箱等隐私数据提取正则。
* **风险评估**: 依据提取的信息类型及数量做危险等级标记。

### 4. 并发架构
* 基于 `ThreadPoolExecutor` 支持 10-50 线程并发执行。
* 搜索与文件解析过程通过 Pipeline 流水线分离处理。

### 5. 关键词管理配置
* 支持 GUI 管理并编辑检测关键词及策略分类。
* 预设分类涵盖：敏感入口、教育数据、商业机密、个人隐私、调试信息、凭证Token。
* 自定义配置自动持久化至 `keywords.json`。

### 6. 数据处理
* 能够自动补充下载初始阶段未成功建立连接的文件。
* 支持结果合并、去重处理与字段过滤能力。

---

## 测试策略(Dork Strategies)定义

系统将搜索语法切分为 6 级：

| 级别 | 范围 | 特征描述 | 示例语法 |
| :--- | :--- | :--- | :--- |
| **P1** | **API与中间件** | 常见配置与接口 | `inurl:swagger`, `inurl:actuator/env`, `filename:pom.xml` |
| **P2** | **高敏文件** | 检索含有 PII 的表格与文档 | `filetype:xlsx "通讯录"`, `filetype:xlsx "身份证"` |
| **P3** | **管理系统** | 企业后台、OA、SSO | `inurl:login`, `inurl:seeyon`, `inurl:weaver` |
| **P4** | **特定行业系统** | 教育/政务等特征 | `"教务系统"`, `filetype:xlsx "学号"` |
| **P5** | **漏洞技术细节** | 报错与可疑扩展名 | `inurl:php?id=`, `intext:"sql syntax near"`, `ext:sql` |
| **P6** | **云与运维基础设施** | DevOps及凭据文件 | `inurl:jenkins`, `filename:id_rsa`, `filename:web.config` |

---

## 快速运行 (EXE版)

可直接运行编译好的 `LeakDetector.exe`。

### 1. 启动
运行 `LeakDetector.exe` 进入 GUI。

### 2. 参数设置
1. **输入目标**: 填入需检测的目标根域名，支持换行批量导入。
    > *示例*: `example.com`
2. **选择模式**:
    * **Standard**: 侧重文档泄露与信息提取检索。
    * **Enterprise**: 侧重管理后台、中间件资产暴露检验。
    * **All**: 执行所有 P1-P6 策略层级。
3. **执行**: 点击开始按钮。

### 3. 浏览器辅助模式
在发包过快触发 Bing 防机器人验证时适用：
1. 勾选 `显示浏览器` 设置。
2. 内部驱动拉起可视化的 Chrome/Edge 浏览器窗口。
3. 遇到验证拦截时程序挂起等待，在浏览器内手动验证通过后点击弹窗的继续执行。

---

## 补充说明

项目代码与界面预览可参考 GitHub 仓库指引。

**作者**: bug_001
[仓库地址](https://github.com/cbbzx12/LeakDetector)
