# 用户登录接口性能测试项目

## 一、项目简介

本项目基于 Apache JMeter 对用户登录接口进行性能测试，主要覆盖接口请求、请求头配置、CSV 多账号参数化、响应断言、并发压测、聚合报告分析和不同并发梯度对比等场景。

项目通过 CSV Data Set Config 模拟多个用户登录，并结合 Aggregate Report 分析接口在不同并发压力下的平均响应时间、最大响应时间、吞吐量和异常率。

## 二、技术栈

- Apache JMeter
- HTTP Request
- HTTP Header Manager
- CSV Data Set Config
- Response Assertion
- View Results Tree
- Aggregate Report
- Git / GitHub

## 三、项目结构

```text
user-login-performance-test/
│
├── test_plan/
│   └── user_login_performance_test.jmx
│
├── data/
│   └── users.csv
│
├── reports/
│   └── aggregate_report.png
│
├── result_analysis.md
│
└── README.md
```

## 四、测试接口

本项目使用 httpbin.org 提供的 POST 接口作为练习接口，用于模拟用户登录请求。

```text
POST https://httpbin.org/post
```

请求头：

```text
Content-Type: application/json
```

请求体：

```json
{
  "username": "${username}",
  "password": "${password}"
}
```

## 五、测试场景

### 1. 登录接口请求测试

使用 JMeter HTTP Request 发送 POST 请求，验证接口是否能够正常响应。

主要配置：

- 协议：https
- 服务器名称：httpbin.org
- 请求路径：/post
- 请求方法：POST
- 请求体：JSON 格式

### 2. CSV 多账号参数化

使用 CSV Data Set Config 读取账号数据，实现多用户参数化请求。

CSV 示例：

```csv
username,password
test01,123456
test02,123456
test03,123456
test04,123456
test05,123456
```

CSV 配置示例：

```text
Filename：data/users.csv
Variable Names：username,password
Delimiter：,
Ignore first line：True
Recycle on EOF：True
Stop thread on EOF：False
Sharing mode：All threads
```

### 3. 响应断言

使用 Response Assertion 对响应结果进行校验，判断响应内容是否包含指定字段。

断言字段：

```text
username
```

通过断言可以避免只看状态码 200，而忽略响应内容错误的情况。

### 4. 并发压测

通过线程组模拟多个用户并发请求登录接口，并观察不同并发压力下的响应情况。

本项目设计了三组并发梯度：

| 场景 | 线程数 | Ramp-Up | 循环次数 | 总请求数 |
| --- | --- | --- | --- | --- |
| 场景一 | 5 | 5秒 | 2 | 10 |
| 场景二 | 10 | 5秒 | 2 | 20 |
| 场景三 | 20 | 10秒 | 2 | 40 |

## 六、测试结果

不同并发梯度下的聚合报告结果如下：

| 场景 | 总请求数 | 平均响应时间 | 最大响应时间 | 异常率 | 吞吐量 |
| --- | --- | --- | --- | --- | --- |
| 场景一：5线程 | 10 | 2687 ms | 7218 ms | 0.00% | 1.1/sec |
| 场景二：10线程 | 20 | 3012 ms | 20890 ms | 0.00% | 53.2/min |
| 场景三：20线程 | 40 | 3677 ms | 9958 ms | 0.00% | 2.4/sec |

详细测试结果与分析见：

```text
result_analysis.md
```

## 七、结果说明

本次测试中，三组并发场景的异常率均为 0.00%，说明接口在当前测试条件下均能够正常响应，未出现请求失败或断言失败。

从响应时间来看，随着线程数增加，平均响应时间整体有所上升，说明并发压力增加后，接口响应时间存在一定增长。

由于测试接口使用的是公网测试接口 httpbin.org，响应时间可能受到本地网络环境、公网链路和第三方服务稳定性的影响，因此本项目主要用于展示 JMeter 性能测试流程和基础性能指标分析能力，不作为真实业务系统的性能结论。

## 八、项目收获

通过本项目，完成了以下内容：

- 使用 JMeter 创建接口性能测试计划
- 配置 HTTP 请求和请求头
- 使用 CSV Data Set Config 实现多账号参数化
- 使用 Response Assertion 校验接口响应内容
- 使用线程组模拟不同并发场景
- 通过聚合报告分析平均响应时间、最大响应时间、异常率和吞吐量
- 对不同并发梯度下的测试结果进行对比分析
- 编写性能测试结果分析文档

## 九、后续优化方向

- 使用真实业务接口替代公网测试接口
- 生成 JMeter HTML 性能测试报告
- 增加更多并发梯度测试，例如 50、100 线程
- 增加错误请求场景和异常率分析
- 结合服务器资源监控分析 CPU、内存、网络等指标