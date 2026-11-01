# 谢宇恒

**Agent Runtime / 编程语言与编译器 / 独立开发者**

[xyheme@gmail.com](mailto:xyheme@gmail.com) · [xieyuheng.com](https://xieyuheng.com) · [github.com/xieyuheng](https://github.com/xieyuheng)

## 个人概述

9 年工程经验，长期在完整产品中负责产品设计、技术架构与全栈开发。近两年独立完成两个系统级项目：本地优先的编程智能体 **风铃 / Windbell**，以及带静态类型、编译器、VM 和汇编器的 Lisp 语言族 **x-lisp / meta-lisp**。

我关注 Agent 如何从“单次对话”走向“持续在线、可并发、可恢复”的基础设施：上下文如何组织与缓存、任务如何调度、事件如何路由、异常如何自愈；同时长期实践编译器、类型系统、运行时与 x86-64。擅长从产品体验反推系统架构，并独立把不确定需求推进到可运行、可迭代的完整系统。

**求职方向：** Agent Runtime / Agent 基础设施 / 编程语言与编译器 / 开发者工具

## 与岗位的匹配

- **Agent Runtime：** 从零实现可用的 coding agent，覆盖流式推理、工具调用循环、上下文持久化、turn 重放/幂等、异常恢复、并发锁与多 provider 接入。
- **上下文与成本：** 以 append-only 事件流和确定性请求重建保持 prompt cache 前缀稳定；按轮次统计 cache hit/miss token，并在 UI 中可视化上下文余量。
- **系统底层：** 独立实现 HM 类型推导、ADT / 模式匹配、闭包转换、SSA IR、字节码 VM、TLV/ELF 目标格式与 x86-64 汇编器。

## 项目经历

### 风铃 Windbell — 本地优先的编程智能体｜独立开发｜2026.09 – 至今

[windbell.xieyuheng.com](https://windbell.xieyuheng.com) · [源码](https://github.com/xieyuheng/opera) · TypeScript / Vue / Hono

受 DeepSeek Harness 启发，从零设计并实现一个运行在用户本机、可通过浏览器和手机访问的编程智能体，作为日常开发工具持续使用和迭代。

- **Agent 核心：** 实现完整的流式 ReAct 循环：模型流式输出 reasoning / assistant / tool-call，工具结果回填上下文；支持 bash/PowerShell、超时、取消、输出截断和错误报告。
- **上下文与恢复：** 将 user、assistant、reasoning、tool-call、tool-output 统一建模为 append-only Sign 事件流；实现 turn 输入哈希幂等、已完成 turn 重放、失败重试、中断恢复和孤儿 tool call 自动修复。
- **Prompt Cache 与用量：** 通过确定性消息重建与 append-only 上下文保持稳定缓存前缀；完整解析并统计 input/output/cache-read/cache-miss/cache-write token，按 turn/step 持久化并可视化上下文窗口余量。
- **并发与可靠性：** 使用带 TTL 自动续租的文件锁防止同一 Session 并发写入；通过 NDJSON/SSE 流式传输 turn 事件；Session / workspace 使用纯文本文件数据库，便于 git 同步、备份和回滚。
- **多 Provider 与产品化：** 抽象 DeepSeek / OpenRouter provider，支持模型发现、默认模型、API key 管理和思考开关；提供 Bearer token 鉴权、局域网访问、二维码、Cloudflare Tunnel / IPv6 远程访问，并针对手机操作电脑做专门适配。
- **工程化：** 独立维护 agent core、API、Web、CLI 与文档；CI 覆盖 Linux / Windows，提供 bash / PowerShell 对等脚本和源码式安装流程。

### x-lisp / meta-lisp — 静态类型 Lisp 语言、编译器与 VM｜独立开发｜2025.07 – 至今

[lisp.xieyuheng.com](https://lisp.xieyuheng.com) · [源码](https://github.com/xieyuheng/x-lisp) · TypeScript / C / x86-64

从零实现的一门带静态类型的极简 Lisp，以及配套编译器、SSA IR、字节码 VM、可执行文件格式和 x86-64 汇编器。

- **语言设计：** 实现 Hindley–Milner 风格参数多态类型推导、类型统一与 occurs check、代数数据类型、模式匹配、模块/包系统、闭包、自动 currying、尾调用和 Markdown 文学式编程；同时提供英文与纯中文两套等价语法。
- **编译器：** 设计并实现约 17 个 pass 的编译流水线，覆盖语法展开、模块/ADT 分析、脱糖、类型推断与检查、闭包转换、参数个数限制、控制流显式化（CPS 风格的 basic block 展平）、指令选择、打包与汇编；输出 basic-lisp SSA IR、xvm-lisp 汇编和 TLV 可执行文件。
- **运行时与后端：** 使用 C 实现 mark-sweep GC、连续 frame 栈、闭包/currying 与尾调用的字节码 VM；设计并实现 TLV 可执行格式、loader、xvm-lisp / x86-lisp 汇编器，以及 x86-64 ELF64 relocatable object 输出。
- **性能与工程化：** 使用 perf、IPC、cache-miss 和峰值内存分析自举编译器 workload；定位 GC 为主要瓶颈，将 VM 从 direct threading 重构为 indirect threading，净减约 243 行并消除未对齐指针 UB，基准无性能回退。建立 70+ 单元/快照测试与 Linux / Windows 双平台 CI；目前推进 meta-lisp 自举编译器（WIP）。

### Cicada / PLCT 实验室 — Dependently Typed 程序语言｜远程协作｜2022.08 – 2022.10

基于 Cicada 项目，与四名学生协作探索依赖类型语言实现；通过代码讲解和论文共读，帮助成员理解并实现 dependent type，参与 implicit argument 等高阶特性。

### 量匠科技 Quantisan — 算法与前端｜深圳｜2023 – 2024

使用 Prolog 实现机械零件模型的特征描述与识别，为工序自动分析提供核心算法；实现工厂「工艺库」网站与基于特征识别的工序自动分析网站。

## 工作经历

| 时间 | 公司 / 项目 | 职责与产出 |
| --- | --- | --- |
| 2019.12 – 2021.11 | 风变科技 | 设计交互式课程创作平台「课灵小馆」及其 DSL、在线编辑与预览；设计卡牌系统与「思索」间隔重复项目；负责产品、技术架构与全栈开发，带领 3–4 人团队。 |
| 2021.12 – 2022.07 | XMind / 维格智数 Vikadata | XMind：完成 Markdown + List Item 思维导图产品原型。Vikadata：使用 React 实现维格表日常前端需求。 |
| 2017.10 – 2019.08 | RiceQuant / InBlockchain / 长春理想 / 特航航空 | Python 爬虫、Flask 后端与量化策略；Elixir / Phoenix / GraphQL；Python 数据挖掘与异常检测；Scala / Akka Stream 数据同步与内部函数式编程培训。 |

## 教育

**中山大学** — 数学与应用数学，学士，2015

## 论文与授课

- *Function compose, Type cut, and The algebra of logic*，ICFP Scheme Workshop，2016，日本奈良；*A Recursive Combinatorial Description of cell-complex*，2019。
- 中山大学汇编语言实验课程，2015；课程材料与教学项目开源。

## 技术栈

**语言：** TypeScript、C、Scheme / Lisp、Prolog、Scala、Elixir、Python、SQL  
**Agent：** function calling、SSE / 流式输出、context / prompt cache、事件溯源、幂等与重放、任务恢复、session 并发控制  
**编译器与运行时：** lexer / parser、HM 类型推导、ADT / pattern matching、desugaring、closure conversion、SSA、instruction selection、assembler、ELF / TLV、GC、x86-64  
**Web 与工程：** Vue、React、Node.js、Hono、Vite、Tailwind CSS、Git、GitHub Actions、Linux、Cloudflare
