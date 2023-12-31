# 概念

> 在软件测试中：压力测试（Stress Test），也称为强度测试、负载测试。压力测试是模拟实际应用的软硬件环境及用户使用过程的系统负荷，长时间或超大负荷地运行测试软件，来测试被测系统的性能、可靠性、稳定性等。

## 目的

- 通过压测 (模拟真实用户的行为)，测算出机器的性能 (单台机器的 `QPS`)，从而推算出系统在承受指定用户数 (例如 100W) 时，需要多少资源
- 在上线前为了应对未来可能达到的用户数量的一次预估(提前演练)，压测以后通过优化程序的性能或准备充足的机器，来保证用户的体验

## 类型

|                               |                                                               |
|:------------------------------|:--------------------------------------------------------------|
| 压力测试 (Stress Testing)         | 通过强负载(大数据、高并发) 测试系统所能承受的最大压力                                  |
| 并发测试 (Concurrency Testing)    | 通过模拟很多用户同一时刻访问系统或对系统某一个功能进行操作，来测试系统的性能并从中发现问题(并发读写、线程控制、资源争抢) |
| 耐久性测试 (Configuration Testing) | 通过让系统在超负载状态长时间运行发现问题(内存泄漏、数据库连接池不释放、资源不回收)                    |

## 名词

|                                 |                                   |
|:--------------------------------|:----------------------------------|
| 并发 (Concurrency)                | 一个处理器逻辑上同时处理多个任务                  |
| 并行 (Parallel)                   | 多个处理器物理上同时处理多个任务                  |
| QPS (Query Per Second)          | 服务器每秒钟处理请求数量                      |
| TPS (Transaction Per Second)    | 服务器每秒钟处理事务数量                      |
| 请求成功数 (Request Success Number)  | 请求成功的数量，一般条件是 `statusCode == 200` |
| 请求失败数 (Request Failures Number) | 请求失败的数量，一般条件是 `statusCode != 200` |
| 最大响应时间 (Max Response Time)      | 所有请求响应的最大时间                       |
| 最小响应时间 (Min Response Time)      | 所有请求响应的最小时间                       |
| 平均响应时间 (Average Response Time)  | 所有请求响应的平均时间                       |

# 指标

## 性能指标

|                         |                            |
|:------------------------|:---------------------------|
| CUP 利用率 (CPU Usage)     | CPU 利用率 = `1 - 空闲时间 / 总时间` |
| 内存使用率 (Memory usage)    | 使用与空闲各自占比、交换页              |
| IO (Disk input/ output) | 读写速率尽可能 `<= 30%`           |
| 网卡负载 (Network Load)     |                            |

## 访问指标

| 访问                         | 解释                                     |
|:---------------------------|:---------------------------------------|
| PV (页面浏览量 Page View)       | 用户每打开 1 个网站页面，`PV+1`, 同一页面打开多次，`PV 递增` |
| UV (网站独立访客 Unique Visitor) | 相同用户访问，只算 `1 个 UV`, 不管访问多少个页面          |

## 如何计算指标

> 二八原则: 80% 的访问量集中在 20% 的时间 (这段时间就是峰值)

> 公式: (PV * 0.8) / (每天秒数 * 0.2) = 峰值 QPS

假设: 网站每天 UV `100W`，PV `3000W`，峰值 QPS 是多少?

> (30000000 * 0.8) / (86400 * 0.2) ≈ 1389 (QPS)

假设: 单台服务器的的 QPS 是 69，至少需要多少台服务器？

> 1389 / 69 ≈ 20

# 寻找瓶颈 (这里以 CPU 为例)

**常规的操作是把服务器压测挂掉，或者把某个服务压测挂掉，但是事实上，超过一定的延迟之后，即使响应正常，站在用户体验的角度，应用已经等于不可用了。**

1. 压力测试硬件环境尽可能接近生产硬件环境 (尤其是各类参数调优)
2. 压力测试数据尽可能接近生产数据
3. 使用固定 QPS 压测，以阶梯形式逐渐增加压测 QPS，如 1000 -> `每分钟增加 1000 QPS`
4. 压测过程中观察系统的延迟是否异常 
5. 观察系统的 CPU 使用情况 
6. 如果 CPU 使用率在达到一定值之后不再上升，反而引起了延迟的剧烈波动，大概率是发生了阻塞 
7. 如果 CPU 上升较快，未达到预期吞吐就已经过了高水位，则可以重点考察 CPU 使用是否合理

# Go 实现的压测工具

还有使用 `ab`, `wrk` 做压力测试？ 可以换一个更加 ` Go 原生` 的命令行工具: **hey** 。

> `hey` 是一个向 Web 应用程序发送一些压力负载的测试小工具。

```shell
# 安装
$ go install github.com/rakyll/hey@latest

# 查看使用帮助
$ hey --help

# 输出如下
Usage: hey [options...] <url>

Options:
  -n  请求数量，默认 200
  -c  并发数量，默认 50
  ...
  ...
```

# 示例

```shell
# 测试 Go 官网，并发数量 20, 请求数量 200

$ hey -n 200 -c 20 https://go.dev/
# 输出结果如下

# 概述
Summary:
  Total:        10.3690 secs  // 总时间
  Slowest:      6.2163 secs   // 最大响应时间
  Fastest:      0.2779 secs   // 最小响应时间
  Average:      1.0077 secs   // 平均响应时间
  Requests/sec: 19.2883       // 每秒请求数量

# 响应时间分布
Response time histogram:
  0.278 [1]     |
  0.872 [179]   |■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  1.466 [0]     |
  2.059 [0]     |
  2.653 [0]     |
  3.247 [0]     |
  3.841 [0]     |
  4.435 [0]     |
  5.029 [0]     |
  5.622 [0]     |
  6.216 [20]    |■■■■

# 延迟百分位
Latency distribution:
  10% in 0.3719 secs
  25% in 0.3979 secs
  50% in 0.4489 secs
  75% in 0.5019 secs
  90% in 5.8198 secs
  95% in 5.9774 secs
  99% in 6.1932 secs

# 链路耗时详情
Details (average, fastest, slowest):
  DNS+dialup:   0.3829 secs, 0.2779 secs, 6.2163 secs
  DNS-lookup:   0.0228 secs, 0.0001 secs, 0.1513 secs
  req write:    0.0000 secs, 0.0000 secs, 0.0001 secs
  resp wait:    0.3126 secs, 0.2457 secs, 0.6538 secs
  resp read:    0.0149 secs, 0.0005 secs, 0.0648 secs

# 响应状态码分布
Status code distribution:
  [200] 200 responses
```

# Reference 

1. [压力测试 - 百度百科](https://baike.baidu.com/item/%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95/10029152)
2. [go-stress-testing](https://github.com/link1st/go-stress-testing)
3. [压力测试 - 阿里云](https://help.aliyun.com/document_detail/55208.html)
4. [hey](https://github.com/rakyll/hey)