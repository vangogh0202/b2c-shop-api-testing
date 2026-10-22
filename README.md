# 华测B2C在线商城（ShopXO）综合测试项目

基于在线 B2C 电商平台 ShopXO 的独立综合测试实战项目，覆盖**功能测试 → 接口测试 → 接口自动化回归 → 性能测试**完整测试链路，测试产物齐全、可复现运行。

## 测试范围与成果

| 项 | 内容 |
|---|---|
| 测试模块 | 登录注册、商品浏览、商品搜索、购物车、下单流程、个人中心、收货地址（7 大模块） |
| 功能测试 | XMind 梳理需求与用例，**设计功能用例 64 条**（7 大模块，等价类 / 边界值 / 场景法 / 错误推测法）；其中 4 条验证点已由接口自动化覆盖可复现，11 条因演示站无对应功能未执行，其余为人工走查并逐条标注 |
| 缺陷管理 | 禅道全生命周期管理，发现并跟踪「收货地址手机号未校验 11 位」等缺陷 |
| 接口测试 | Fiddler Classic 抓包梳理登录、搜索、加购、下单 **4 个核心接口**，Apifox/Postman 调试与参数依赖分析 |
| 接口自动化 | Postman + Newman 回归套件：**6 个请求、15 条断言**，命令行一键执行约 **1.3 秒全绿**，htmlextra 生成 HTML 报告 |
| 性能测试 | JMeter **30 并发、共 300 请求**：搜索平均 103ms、登录平均 96ms、**P95=149ms、错误率 0** |

## 技术栈

XMind ｜ 禅道 ｜ Fiddler Classic ｜ Apifox ｜ Postman ｜ Newman ｜ Apache JMeter ｜ Node.js

## 关键技术点（面试可深讲）

1. **AJAX 请求头**：请求必须携带 `X-Requested-With: XMLHttpRequest`，否则返回"非法访问"（HTTP 状态码仍为 200），定位为前后端鉴权处理差异。
2. **动态参数关联**：购物车记录 id 在下单后失效，必须在每次加购后从购物车/结算页响应中动态提取最新 id；地址 id 从结算页提取；提交订单同样依赖请求头。
3. **业务码断言**：ShopXO 接口业务成功码为 `code=0`（而非仅看 HTTP 200），断言同时覆盖状态码、业务码、提示语与动态参数非空。
4. **正则兼容转义**：页面 JS 模板中引号为转义形式 `\"`，提取 id 的正则需兼容转义字符。

## 目录结构

```
huace-b2c
├── docs/          # 测试用例脑图、功能用例Excel、系统测试报告、项目总结脑图
├── postman/       # Postman 集合与环境变量（含动态提取脚本与断言）
├── newman/        # Newman 依赖声明（newman-reporter-htmlextra）
├── jmeter/        # JMeter 性能测试计划（30并发/300请求）
├── reports/       # Newman HTML 回归报告、JMeter HTML 性能报告
└── README.md
```

## 如何复现运行

### 1. Newman 接口自动化回归

```bash
cd newman
npm install          # newman 与 htmlextra 报告器都已声明在 package.json
npm run regression   # 6 请求 / 15 断言，约 1.5 秒全绿，并导出 HTML 报告
```

等价的手动命令（自行指定报告器与导出路径）：

```bash
npx newman run ../postman/huace-b2c.collection.json \
  -e ../postman/huace-b2c.environment.json \
  -r cli,htmlextra \
  --reporter-htmlextra-export ../reports/newman-回归报告.html
```

> 踩坑记录：若把 newman 装在全局，newman 不会去项目目录解析报告器，会报 `could not find "htmlextra" reporter`。因此 newman 本身也作为 devDependency 装进项目，保证 `npm install && npm run regression` 在任意机器上可复现。

### 2. JMeter 性能测试

```bash
jmeter -n -t jmeter/huace-b2c.jmx -l result.jtl -e -o reports/jmeter-html
```

> 演示环境为公共在线环境，clone 后可直接运行。

## 测试报告产物

- `docs/测试用例脑图.xmind` —— 7 大模块需求与用例设计
- `docs/功能测试用例.xlsx` —— 64 条用例明细（含等价类 / 边界值 / 场景法 / 错误推测法；每条标注验证方式：已实测 / 未执行原因 / 人工走查）
- `docs/系统测试报告.docx` —— 完整系统测试报告
- `docs/项目总结脑图.xmind` —— 项目复盘与技术沉淀
- `reports/newman-回归报告.html` —— 6 请求 15 断言全绿（约 1.3 秒）
- `reports/jmeter-html/index.html` —— 30 并发性能测试报告

## 项目收获

- 打通完整软件测试流程：需求分析 → 用例设计 → 执行 → 缺陷管理 → 报告输出
- 掌握接口测试与接口自动化落地能力（抓包分析、参数关联、断言设计、命令行回归）
- 掌握性能测试基础方法（并发设计、指标解读、报告输出）
