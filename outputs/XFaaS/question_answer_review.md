# Question-Answer Review

Source PDF: `XFaaS.pdf`

Citation format: `[p. X, Section Y, Block P-0XX]`.

---

# Q1. What tradeoffs should be considered when splitting an application into separate serverless functions?

## Relevance judgment

Relevant.

## Short answer

把应用拆成多个 serverless functions 时，核心取舍不是“函数越小越好”，而是在开发便利性、隔离和弹性之外，同时评估冷启动/初始化开销、峰值容量浪费、跨区域调度复杂性、下游服务过载风险、内存局部性、配额/截止时间/关键性设置，以及是否能接受延迟执行。论文显示，FaaS 把资源供给责任从用户转移给平台，若函数调用间歇且延迟要求严格，平台可能承担高额空闲和过度配置成本；若函数可延迟、可排队、可设置 SLO 和关键性，平台就能用时间转移和全局调度提高利用率。 [p. 1, Section 1 Introduction, Block P-002] [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-004] [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-005] [p. 2, Section 1.2 Solutions for Extra Hardware Costs, Block P-008]

## Evidence table

| Claim | Citation | Evidence from paper | How it supports the answer |
|---|---|---|---|
| 拆分为 FaaS 会把过度配置成本从用户侧转移到平台侧。 | [p. 1, Section 1 Introduction, Block P-002] | The ease of use of FaaS may come at the expense of extra hardware costs for cloud providers. Before FaaS, users carried the cost of underutilized VMs; with FaaS, cloud providers bear the cost of over-provisioned resources. | 说明拆分决策需要考虑资源责任和成本归属，而不只是开发便利性。 |
| 冷启动和空闲等待之间存在直接取舍。 | [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-004] | Lengthy cold-start time is a significant FaaS challenge because initialization and shutdown steps are overheads borne by the provider, while only invocation does chargeable work. If the idle wait is too long, containers waste r... | 函数越细、调用越间歇，初始化和保温策略越影响延迟与资源浪费。 |
| 高峰值波动会导致按峰值配置浪费，配置不足又会过载。 | [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-005] | High load variance creates a tradeoff between extra hardware cost from over-provisioning and overload during surges. XFaaS sees a peak-to-trough ratio as high as 4.3, so provisioning for peak demand would create severe waste. | 说明拆分后触发模式和负载形态会影响平台容量规划。 |
| 函数可能把压力转移给下游服务，静态并发限制不足。 | [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-006] | FaaS functions can overload downstream services. Meta experienced outages when spikes from non-user-facing functions overloaded a social-graph database. Static per-function concurrency limits are insufficient because limits tha... | 说明函数边界之外的依赖也必须纳入拆分取舍。 |
| 通用 worker 受内存限制，必须用局部性组约束函数分布。 | [p. 9, Section 4.5.2 Locality Groups, Block P-026] | A true universal worker is infeasible because keeping every function's JIT code in every worker's memory would exceed memory capacity, and memory-hungry functions may also exhaust memory. XFaaS partitions functions and workers... | 说明函数数量、代码/JIT 缓存和内存局部性会影响平台效率。 |
| 预留配额与机会式配额体现延迟保证和成本之间的取舍。 | [p. 10, Section 4.6 Quotas and Utilization Control, Block P-027] | XFaaS supports reserved quota and opportunistic quota. Reserved quota provides guaranteed capacity; opportunistic quota can be delayed until off-peak hours. This creates a direct tradeoff between latency guarantees and lower ha... | 说明每个函数应根据业务时限选择容量模式。 |

## Detailed answer

首先，要评估冷启动和保温成本。论文把函数生命周期拆成初始化、调用和关闭阶段，并指出只有调用阶段是真正可计费工作，初始化和关闭相关步骤则是平台开销；等待时间太长会浪费容器资源，等待时间太短又会让下次请求重新经历初始化。 [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-004]

其次，要评估负载是否尖峰化以及是否能被推迟。XFaaS 的接收调用峰谷比可达 4.3；若按峰值配置，会严重浪费。论文的解决方案是让函数携带截止时间、关键性、配额和未来开始时间，并在容量不足时推迟可延迟或低关键性函数。 [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-005] [p. 2, Section 1.2 Solutions for Extra Hardware Costs, Block P-008] [p. 4, Section 2.4 Terminology, Block P-014]

第三，要评估下游依赖。即使 FaaS 平台自身能处理负载，函数也可能压垮数据库、缓存等下游服务；因此需要并发限制、背压和自适应速率控制，而不是只按函数本身配置静态并发。 [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-006] [p. 3, Section 1.2 Solutions for Extra Hardware Costs, Block P-009] [p. 10, Section 4.7 Adaptive Concurrency Control, Block P-028]

第四，要评估函数粒度对运行时共享、JIT 缓存和内存的影响。XFaaS 想近似每个 worker 可执行任意函数，但真正的通用 worker 会受到 JIT 代码和函数内存需求限制，因此需要把函数和 worker 划分为局部性组。 [p. 2, Section 1.2 Solutions for Extra Hardware Costs, Block P-007] [p. 9, Section 4.5.2 Locality Groups, Block P-026]

## Blocks to reread

- [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-004]
- [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-005]
- [p. 2, Section 1.1 Risk of Extra Hardware Costs, Block P-006]
- [p. 9, Section 4.5.2 Locality Groups, Block P-026]

## Uncertainty

The paper answers this question well from the platform/operator perspective. It gives less direct guidance on application-level software design boundaries beyond the implications of trigger type, latency tolerance, resource usage, and downstream dependencies.

---

# Q2. In what cases is deploying an application in a serverless fashion a poor fit, and in what cases is it a good fit?

## Relevance judgment

Relevant.

## Short answer

根据本文，要求亚秒级响应、直接处于用户交互关键路径、必须严格容量保障且已有成熟自动部署能力的场景，不适合用 serverless functions 作为主要执行方式；Meta 的信息流展示、搜索排序、视频播放属于这类例子。更适合 serverless 的场景是异步、事件驱动、队列/日志/存储/定时器/编排触发、可设置完成时限、可接受秒级到 24 小时延迟、可通过排队和机会式配额平滑到低峰执行的工作负载，例如推荐更新、日志处理、生产力自动化、通知活动和数据转换。 [p. 5, Section 3.2 Workload Examples, Block P-016] [p. 5, Section 3.2 Workload Examples, Block P-017] [p. 5, Section 3.2 Workload Examples, Block P-018] [p. 3, Section 2.2 Spiky Load, Block P-012]

## Evidence table

| Claim | Citation | Evidence from paper | How it supports the answer |
|---|---|---|---|
| 亚秒级用户交互请求是 serverless 的较差适配场景。 | [p. 5, Section 3.2 Workload Examples, Block P-016] | XFaaS workloads seldom handle user-facing interactive requests demanding sub-second response times, such as newsfeed display, search-result ranking, or video playback. At Meta, those requests are traditionally handled by long-r... | 论文直接列出 Meta 很少用 XFaaS 处理这类请求。 |
| 当必须精细容量规划且部署自动化已存在时，serverless 的两个常见优势会减弱。 | [p. 5, Section 3.2 Workload Examples, Block P-017] | The paper identifies two common benefits of serverless functions: pay-as-you-go with no upfront capacity planning, and streamlined deployment where developers write code while the platform handles deployment. For user-facing re... | 解释为什么 Meta 的强用户体验场景不依赖 FaaS。 |
| 异步推荐、日志处理、自动化、通知和数据转换是更适合的例子。 | [p. 5, Section 3.2 Workload Examples, Block P-018] | Good XFaaS examples include asynchronous recommendations after user events, log processing that is not on the critical path but still has an SLO, productivity automation triggered by events, notification campaigns, and Morphing... | 给出论文中的正面 serverless 工作负载样例。 |
| 队列、定时器、存储和编排触发通常比直接 RPC/HTTP 更能容忍延迟。 | [p. 3, Section 2.2 Spiky Load, Block P-012] | XFaaS clients submit function calls in a highly spiky manner. One key insight is that some functions do not need immediate execution; functions triggered by queues, timers, storage, orchestration, and event-bridge workflows ten... | 提供判断是否适合 serverless 的触发方式标准。 |
| 机会式配额可把函数推迟到低峰执行并降低峰值容量。 | [p. 12, Section 5.3 Time-Shifting Computing, Block P-031] | Opportunistic-quota functions can be deferred to off-peak hours. The CPU consumption of reserved and opportunistic functions almost exactly complements each other, showing that XFaaS schedules opportunistic functions during off... | 说明可延迟工作负载为何适合 serverless 平台优化。 |

## Detailed answer

不适合的情况主要是交互关键路径和严格低延迟场景。论文明确说 XFaaS 很少处理要求亚秒级响应的用户交互请求，例如 newsfeed display、search result ranking、video playback；这些请求在 Meta 通常由长期运行服务处理，因为 serverless functions 在这些场景中没有明显优势。 [p. 5, Section 3.2 Workload Examples, Block P-016]

论文还解释了原因：serverless 常被称赞的两个优势是按需付费/无需预先容量规划，以及平台自动化部署；但对于 Meta 级别的用户体验，仍需要精细容量规划，而自动化部署本身也可以由 Conveyor 这样的系统完成。 [p. 5, Section 3.2 Workload Examples, Block P-017]

适合的情况是异步、事件驱动和可延迟工作。论文列出的典型工作负载包括接受好友请求后异步生成推荐、日志事件处理、规则触发的自动化任务、通知活动，以及运行数分钟的数据转换函数。 [p. 5, Section 3.2 Workload Examples, Block P-018]

更一般地，理想候选通常由队列、定时器、存储、编排或事件桥触发，而不是由直接 HTTP/RPC 触发；它们更可能没有用户在同步等待结果，因此平台可以用截止时间、关键性和机会式配额把执行平滑到低峰期。 [p. 3, Section 2.2 Spiky Load, Block P-012] [p. 12, Section 5.3 Time-Shifting Computing, Block P-031]

## Blocks to reread

- [p. 5, Section 3.2 Workload Examples, Block P-016]
- [p. 5, Section 3.2 Workload Examples, Block P-017]
- [p. 5, Section 3.2 Workload Examples, Block P-018]
- [p. 12, Section 5.3 Time-Shifting Computing, Block P-031]

## Uncertainty

The paper gives strong evidence from Meta's private-cloud context. Applying the same fit criteria to a public-cloud application requires checking whether that application has similar latency, trigger, trust, isolation, and capacity characteristics.

---

# Q3. What characteristics would make an application ideal for serverless deployment?

## Relevance judgment

Partially relevant.

## Short answer

本文没有给出一个正式的“ideal serverless application”定义，但可以从 XFaaS 的设计和工作负载归纳出理想特征：事件驱动、异步、不在亚秒级用户交互关键路径上；可表达完成时限、关键性和资源配额；能容忍排队、重试和机会式低峰执行；资源需求可被平台调度和局部性组管理；不会无约束压垮下游服务，或能够响应背压；最好还具有尖峰负载，从而能通过时间转移、跨区域调度和吞吐优化获得明显硬件利用率收益。 [p. 3, Section 2.2 Spiky Load, Block P-012] [p. 4, Section 2.4 Terminology, Block P-014] [p. 5, Section 3.2 Workload Examples, Block P-018] [p. 14, Section 8 Conclusion, Block P-041]

## Evidence table

| Claim | Citation | Evidence from paper | How it supports the answer |
|---|---|---|---|
| 理想 serverless 工作负载通常不是直接 RPC/HTTP 同步请求，而是更能容忍延迟的事件触发工作。 | [p. 3, Section 2.2 Spiky Load, Block P-012] | XFaaS clients submit function calls in a highly spiky manner. One key insight is that some functions do not need immediate execution; functions triggered by queues, timers, storage, orchestration, and event-bridge workflows ten... | 论文把触发方式和延迟容忍度联系起来。 |
| 理想函数应能声明运行时、关键性、开始时间、完成时限、配额、并发限制和重试策略。 | [p. 4, Section 2.4 Terminology, Block P-014] | A function has configurable attributes such as name, arguments, runtime, criticality, execution start time, completion deadline, resource quota, concurrency limit, and retry policy. Completion deadlines can range from seconds t... | 这些属性让平台进行调度、限流和成本优化。 |
| 异步推荐、日志处理、自动化、通知和数据转换体现了适合 XFaaS 的应用形态。 | [p. 5, Section 3.2 Workload Examples, Block P-018] | Good XFaaS examples include asynchronous recommendations after user events, log processing that is not on the critical path but still has an SLO, productivity automation triggered by events, notification campaigns, and Morphing... | 这些例子共同体现了事件驱动和非同步关键路径特征。 |
| 理想应用还要适配 worker 内存和局部性约束。 | [p. 9, Section 4.5.2 Locality Groups, Block P-026] | A true universal worker is infeasible because keeping every function's JIT code in every worker's memory would exceed memory capacity, and memory-hungry functions may also exhaust memory. XFaaS partitions functions and workers... | 函数规模和内存行为会影响能否高效部署。 |
| XFaaS 的结论强调无冷启动、低峰执行和下游服务影响。 | [p. 14, Section 8 Conclusion, Block P-041] | XFaaS enables workers to serve requests for almost any function without cold start through proactive code deployment, cooperative JIT, cross-function runtime sharing, and locality groups. It also postpones delay-tolerant comput... | 总结了理想应用应配合的平台优化方向。 |

## Detailed answer

理想 serverless 应用首先应是事件驱动且异步的。论文认为，队列、定时器、存储、编排和事件桥触发的函数通常比直接 HTTP/RPC 触发函数更能容忍延迟，因为它们不一定对应一个正在等待即时响应的交互用户。 [p. 3, Section 2.2 Spiky Load, Block P-012] [p. 13, Section 6 XFaaS and Public Cloud, Block P-037]

其次，它应能把业务需求转化为平台可调度的属性：完成时限、关键性、资源配额、并发限制、重试策略和可选的未来开始时间。这样平台才能在容量不足时推迟低关键性或可延迟工作，并优先执行关键函数。 [p. 4, Section 2.4 Terminology, Block P-014] [p. 14, Section 6 XFaaS and Public Cloud, Block P-038]

第三，它应允许平台做成本优化。若应用的工作能通过机会式配额被移到低峰期，XFaaS 就能让机会式函数和预留函数的 CPU 消耗互补，从而降低峰值容量需求。 [p. 12, Section 5.3 Time-Shifting Computing, Block P-031]

第四，它不应无控制地放大下游压力。理想的 serverless 应用要么对下游服务影响小，要么能配合背压和并发控制；XFaaS 的生产事故表明，自动减速和排队可在下游恢复后继续处理积压。 [p. 12, Section 5.5 Protecting Downstream Services, Block P-033] [p. 13, Section 5.5 Protecting Downstream Services, Block P-034]

## Blocks to reread

- [p. 3, Section 2.2 Spiky Load, Block P-012]
- [p. 4, Section 2.4 Terminology, Block P-014]
- [p. 5, Section 3.2 Workload Examples, Block P-018]
- [p. 14, Section 8 Conclusion, Block P-041]

## Uncertainty

This answer is an interpretation grounded in the paper's workload examples and system design, because the paper does not explicitly define a universal checklist for an ideal serverless application.
