# 谢宇恒

**编译器 / 程序语言 / 运行时 / 开发者工具**

13909580120 · [xyheme@gmail.com](mailto:xyheme@gmail.com) · [xieyuheng.com](https://xieyuheng.com) · [github.com/xieyuheng](https://github.com/xieyuheng)

**学历：** 中山大学 · 数学与应用数学 · 学士 · 2015

## 个人概述

9 年软件研发经验，近两年独立完成两个系统级项目：带静态类型、编译器、VM 与汇编器的 Lisp 语言族 **x-lisp / meta-lisp**，以及本地优先的编程智能体 **风铃 Windbell**。

长期负责完整产品从产品设计、前端交互、后端 API 到运行时与工程化落地，曾带领 3–4 人全栈团队。具备从语言设计、类型系统、编译流水线、运行时、汇编器到工程化测试的完整实践。

## 核心能力

- **程序语言：** Hindley–Milner 类型推导、类型统一与 occurs check、ADT、模式匹配、模块系统、闭包、currying、尾调用、文学式编程。
- **编译与运行时：** 17-pass 编译流水线、SSA IR、控制流显式化、指令选择、字节码 VM、mark-sweep GC、TLV / ELF、x86-64 汇编器。
- **系统工程：** 能从零实现并调优性能敏感系统；使用 perf、IPC、cache-miss 和内存分析定位瓶颈，持续维护 Linux / Windows CI 与 80+ 测试。
- **产品与全栈：** 具备 TypeScript / Node.js / Vue / React / C / Python 经验；做过 CodeMirror 编辑器、实时预览、Canvas 与 WebGL / three.js 可视化，有产品设计、技术架构和 3–4 人团队带领经验。

## 项目经历

### x-lisp / meta-lisp — 静态类型 Lisp、编译器与 VM · 独立开发 · 2025.07 – 至今

[lisp.xieyuheng.com](https://lisp.xieyuheng.com) · [源码](https://github.com/xieyuheng/x-lisp)

从零实现的一门带静态类型的极简 Lisp，以及配套编译器、SSA IR、字节码 VM、可执行文件格式和 x86-64 汇编器；仓库约 8 万行（TypeScript / C / Lisp / Markdown），80+ 单元与快照测试。

- **语言设计：** 实现 Hindley–Milner 风格参数多态类型推导、类型统一与 occurs check、代数数据类型、模式匹配、模块 / 包系统、闭包、自动 currying、尾调用和 Markdown 文学式编程；同时提供英文与纯中文两套等价语法。
- **编译器：** 设计并实现约 17 个 pass 的编译流水线，覆盖语法展开、模块 / ADT 分析、脱糖、类型推断与检查、闭包转换、参数个数限制、控制流显式化（CPS 风格的 basic block 展平）、指令选择、打包和汇编；输出 basic-lisp SSA IR、xvm-lisp 汇编和 TLV 可执行文件。
- **运行时与后端：** 使用 C 实现 mark-sweep GC、连续 frame 栈、闭包 / currying 与尾调用的字节码 VM；设计并实现 TLV 可执行格式、loader、xvm-lisp / x86-lisp 汇编器，以及 x86-64 ELF64 relocatable object 输出。
- **性能与工程化：** 使用 perf、IPC、cache-miss 和峰值内存分析自举编译器 workload；定位 GC 为主要瓶颈，将 VM 从 direct threading 重构为 indirect threading，净减约 243 行并消除未对齐指针 UB，基准无性能回退；建立 80+ 测试与 Linux / Windows 双平台 CI；目前推进 meta-lisp 自举编译器（WIP）。
- **可维护性：** 为词法、语法、类型、IR、后端等关键阶段建立单元 / 快照测试和 dump 工具，支持逐 pass 观察编译结果，降低语言实现迭代时的回归定位成本。

### 风铃 Windbell — 本地优先的编程智能体 · 独立开发 · 2026.09 – 至今

[windbell.xieyuheng.com](https://windbell.xieyuheng.com) · [源码](https://github.com/xieyuheng/opera)

面向日常开发的本地优先 coding agent，使用 TypeScript / Vue / Hono 实现，支持浏览器与手机远程访问；已作为本人日常开发工具持续使用。

- **Agent Runtime：** 实现完整流式 ReAct 循环、工具调用与结果回填；支持 bash / PowerShell、超时、取消、输出截断和错误报告。
- **上下文与恢复：** 将 user、assistant、reasoning、tool-call、tool-output 建模为 append-only 事件流；实现 turn 输入哈希幂等、已完成 turn 重放、失败重试、中断恢复和孤儿 tool call 自动修复。
- **Prompt Cache 与可靠性：** 通过确定性消息重建和 append-only 上下文保持稳定缓存前缀；统计 input / output / cache token，使用带 TTL 的文件锁防止同一 Session 并发写入，并通过 NDJSON / SSE 流式传输 turn 事件。
- **全栈与前端：** 使用 Vue / TypeScript 实现流式会话与上下文用量界面，支持手机操作适配、二维码和远程访问入口。
- **工程化：** 独立维护 agent core、API、Web、CLI 与文档；约 3.2 万行 TypeScript；CI 覆盖 Linux / Windows，提供 bash / PowerShell 对等脚本和源码式安装流程。

## 工作经历

### 量匠科技（Quantisan） · 算法与全栈 · 深圳 · 2023–2024

面向机械制造企业的智能工厂流程引擎与零件工艺生成系统，团队约 50 人，直接向 CTO 汇报。

- **特征识别：** 基于 STEP 文件与 OpenCascade 提取拓扑 / 几何信息，使用 Prolog 实现 Attributed Adjacency Graph（AAG）搜索，规则上百条；基于大量真实零件图纸验证，可稳定识别绝大多数加工特征。
- **工艺生成：** 识别特征后按规则消除特征并生成工序，支持每一步加工结果的可视化预览；实现工厂「工艺库」网站与工序自动分析界面。
- **端到端交付：** 同时负责算法、后端与前端；技术栈包括 Python、TypeScript、Vue、Tailwind CSS、Node.js，并使用 WebGL / three.js 渲染 STEP 模型与特征。
- **技术攻关：** 调研大量论文并进行实验验证，解决拓扑 / 几何信息提取和特征识别算法设计问题，推动算法从研究原型落地为可用产品。

### 风变科技 · 产品技术负责人 · 深圳 · 2019.12–2021.11

面向内部老师与外部课程创作者的交互式课程创作平台「课灵小馆」；从 0 到 1 完成产品构思、命名与技术架构，服务 Python 编程、职业生涯规划、企业内训等多领域课程，累计十几位老师使用。

- **课程 DSL：** 设计基于 YAML 的课程内容描述语言，通过固定 schema 建模课程结构与内容组件，支持顺序、分支、循环，以及选择题、填空题、问答题、幻灯片、角色扮演、立绘、图片热区和 Canvas 动画等交互形式，面向非程序员创作者。
- **编辑与预览：** 使用 Vue、Tailwind CSS、CodeMirror、Node.js 实现在线编辑与实时预览；创作者编写 DSL 后即可看到课程效果，显著改善课程制作流程并提升内容生产效率。
- **平台与复习系统：** 基于课程平台设计内部「卡牌」系统，以及「思索」复习系统，将主动回顾 / 间隔重复用于课程学习流程。
- **团队与交付：** 带领 3–4 人全栈团队，负责产品设计、技术架构、全栈开发、招聘面试、代码 review 与项目管理；要求成员具备产品能力并各自主导独立模块，采用敏捷开发、结对编程和定期技术分享。

### 早期经历 · RiceQuant · 后端 / 数据工程 · 2017–2018

- 爬虫、Web 后端与量化策略开发：Python、Flask、Scrapy、MongoDB、Pandas。

## 研究与教学经历

### 中国科学院软件研究所 PLCT 实验室 · 依赖类型语言研究合作 · 远程 · 2022

- 以 Cicada 项目为基础，与四名学员协作探索 Dependently Typed 程序语言；通过代码讲解和论文共读，帮助成员理解并实现 dependent type，参与 implicit argument 等高阶特性。

## 论文与授课

- *Function compose, Type cut, and The algebra of logic*，ICFP Scheme Workshop，2016，日本奈良；*A Recursive Combinatorial Description of cell-complex*，2019。
- 中山大学汇编语言实验课程，2015；课程材料与教学项目开源。

## 技术栈

**语言：** TypeScript、C、Scheme / Lisp、Prolog、Scala、Elixir、Python、SQL  
**Agent：** function calling、SSE / 流式输出、context / prompt cache、事件溯源、幂等与重放、任务恢复、session 并发控制  
**编译器与运行时：** lexer / parser、HM 类型推导、ADT / pattern matching、desugaring、closure conversion、SSA、instruction selection、assembler、ELF / TLV、GC、x86-64  
**前端与可视化：** Vue、React、Tailwind CSS、CodeMirror、Canvas、WebGL、three.js  
**后端与工程：** Node.js、Hono、Vite、Git、GitHub Actions、Linux、Cloudflare
