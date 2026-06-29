# API Auto Test Framework (接口自动化测试框架)

## 1. 项目背景 (Why this project?)
在手工测试期间，我发现重复的回归测试占据了大量时间，且人为校验接口数据极易出错。
本框架旨在解决**接口冒烟测试**与**线上环境基础巡检**问题，确保核心业务主流程不受代码变更影响。

## 2. 架构设计思路 (Design Philosophy) —— 为什么这样写？

### 为什么选 Pytest 而不是 Unittest？
- **Fixture机制**：能够清晰划分“测试前置条件（如登录获取Token）”与“测试后清理”，代码复用性远高于Unittest的setUp/tearDown。
- **插件生态**：集成`pytest-xdist`（并行执行）和`allure-pytest`（美观报告）极简，符合我追求的**极简高效**原则。

### 为什么封装 Requests？
- 原生requests每次请求都要写`get/post`和`headers`，存在大量重复冗余。
- 我对其进行了**统一会话管理（Session）**封装，自动处理鉴权（Token刷新）和超时重试，让测试用例（Case）只关注**业务数据（入参与出参）**，做到“用例与底层实现解耦”。

### 分层设计模式 (Layered Architecture)
- **数据层 (Data)**：采用 YAML 文件管理测试数据，支持多环境配置（Dev/Test/Prod），非技术人员也可维护。
- **用例层 (Cases)**：基于 Pytest 参数化，一条函数即可覆盖多种入参组合。
- **公共层 (Common)**：封装通用的断言工具（如JSON Schema校验），确保返回字段类型和必填项符合预期。

## 3. 快速开始 (Quick Start)
```bash
# 1. 安装依赖
pip install -r requirements.txt

# 2. 运行所有用例并生成报告
pytest ./testcases/ --alluredir=./reports
allure serve ./reports
