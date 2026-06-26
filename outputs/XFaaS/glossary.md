# Glossary

| English | Chinese | Note |
|---|---|---|
| Function-as-a-Service / FaaS | 函数即服务 / FaaS | 本文核心领域术语。 |
| serverless computing | 无服务器计算 | 按无服务器语境翻译。 |
| function call / invocation | 函数调用 | 在本文中二者基本同义。 |
| cold start | 冷启动 | 函数初始化路径带来的延迟和资源开销。 |
| universal worker | 通用工作节点 | 近似每个 worker 可立即执行任意函数的效果。 |
| worker | 工作节点 | 运行函数的物理服务器。 |
| runtime | 运行时 | PHP、Python 等函数执行环境。 |
| namespace | 命名空间 | 隔离的 worker 池和函数集合。 |
| locality group | 局部性组 | 限制 worker 执行的函数子集以降低内存压力。 |
| cooperative JIT compilation | 协作式 JIT 编译 | 跨 worker 共享 profiling/JIT 信息。 |
| time-shift computing | 时间转移计算 | 把可延迟函数推迟到低峰执行。 |
| reserved quota | 预留配额 | 保证容量的配额类型。 |
| opportunistic quota | 机会式配额 | 可推迟到低峰执行的低成本配额。 |
| back-pressure | 背压 | 下游服务过载时返回给 XFaaS 的限速信号。 |
| adaptive concurrency control | 自适应并发控制 | 根据下游健康状态调节函数执行速率。 |
| downstream service | 下游服务 | 函数调用依赖的数据库、缓存等服务。 |
| SLO | 服务级目标 / SLO | 以完成时限或可用性表达的目标。 |
| criticality | 关键性 | 容量不足时决定执行优先级的属性。 |
