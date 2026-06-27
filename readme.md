# cpu_stress

多核 CPU 稳定性压测工具，无任何外部依赖。

## 下载
```
wget  https://github.com/cn4096/cpu_stress/raw/refs/heads/main/cpu_stress_arm64
chmod 777 cpu_stress_arm64
```
## 快速开始

```bash
# 跑满所有核，持续 1 分钟（默认）
./cpu_stress

# 压测 10 分钟
./cpu_stress -t 10m

# 持续运行直到手动 Ctrl+C 停止
./cpu_stress -t 0
```

## 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `-t` / `-duration` | `1m` | 压测时长，`0` 表示无限运行直到 Ctrl+C |
| `-workers` | CPU 核数 | 并发 goroutine 数量 |
| `-mode` | `mixed` | 压测模式，见下方说明 |
| `-interval` | `2s` | 状态刷新间隔 |

## 压测模式（-mode）

| 模式 | 特点 |
|------|------|
| `mixed` | 浮点与整数交替，综合压测（推荐） |
| `float` | 浮点密集：sqrt / sin / cos / log / exp |
| `int` | 整数密集：质数筛、Fibonacci |
| `memory` | 内存带宽：16 MiB 大数组跨步访问，绕过 L2 缓存 |

## 运行示例

```
CPU Stress Test
  Go version : go1.24.2
  OS/Arch    : linux/arm64
  CPUs       : 8
  Workers    : 8
  Mode       : mixed
  Duration   : 1m0s

[  30.0s]  remain=30s  workers=8  total_ops=12345678000  rate=421000000 ops/s
```

状态行每隔 `-interval` 秒原地刷新，不会滚动输出。字段含义：

- `remain` — 剩余时间（`-t 0` 时显示 `∞`）
- `total_ops` — 累计完成的计算迭代次数
- `rate` — 最近一个周期的吞吐量（ops/s）

## 常用场景

```bash
# 只用 4 个核压测 30 分钟
./cpu_stress -workers 4 -t 30m

# 专项浮点压测，每 5 秒刷新一次
./cpu_stress -mode float -interval 5s

# 内存带宽压测，不限时长
./cpu_stress -mode memory -t 0

# 快速冒烟，跑 10 秒看看有没有崩溃
./cpu_stress -t 10s
```

## 停止

- 到达 `-t` 指定的时长后自动退出
- 任意时刻按 `Ctrl+C` 立即停止
- 退出时打印累计完成的总迭代次数
