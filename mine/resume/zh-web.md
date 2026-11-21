# 谢宇恒

**资深前端 / 全栈工程师 · 复杂交互与可视化**

13909580120 · [xyheme@gmail.com](mailto:xyheme@gmail.com) · [xieyuheng.com](https://xieyuheng.com) · [github.com/xieyuheng](https://github.com/xieyuheng)

**学历：** 中山大学 · 数学与应用数学 · 学士 · 2015

## 个人概述

9 年软件研发经验，长期负责完整产品从产品设计、前端交互、后端 API 到工程化落地。做过 CodeMirror 在线编辑器、YAML DSL 实时预览、Canvas 动画、WebGL / three.js 三维可视化，以及 AI 流式会话界面；也能独立完成 Node.js / Python 后端与全栈交付。

曾带领 3–4 人全栈团队，适合需要“能独立把产品做出来”的前端 / 全栈岗位。

## 核心能力

- **前端：** TypeScript、Vue、React、Tailwind CSS、CodeMirror、Canvas、WebGL / three.js；有复杂交互、实时预览、响应式与移动端适配经验。
- **全栈：** Node.js、Hono、Python、Flask、REST API、SQL；能独立完成 Web 应用从 API、数据存储到前端界面的开发。
- **工程化：** Vite、Git、GitHub Actions、Linux / Windows CI、测试与脚本化安装；关注性能、可靠性和可维护性。
- **产品与协作：** 0→1 产品设计、DSL 设计、招聘面试、代码 review、项目管理；带过 3–4 人全栈团队。

## 项目经历

### 风铃 Windbell — 本地优先的编程智能体 · 独立开发 · 2026.09 – 至今

[windbell.xieyuheng.com](https://windbell.xieyuheng.com) · [源码](https://github.com/xieyuheng/opera)

面向日常开发的本地优先 coding agent，使用 TypeScript / Vue / Hono 实现，支持浏览器与手机远程访问；已作为本人日常开发工具持续使用。

- **前端与交互：** 使用 Vue / TypeScript 构建会话、工具调用和上下文用量界面；展示流式 reasoning / tool 事件，支持手机操作适配、二维码与局域网访问。
- **全栈与 API：** 设计 Node.js / Hono API 与 NDJSON / SSE 流式传输；Session 与 workspace 使用纯文本文件数据库，支持 git 同步、备份和回滚。
- **Agent 核心：** 实现流式 ReAct 循环、工具调用与结果回填；支持 bash / PowerShell、超时、取消、输出截断和错误报告。
- **可靠性与恢复：** 将对话和工具事件建模为 append-only 事件流，实现 turn 幂等、重放、失败重试、中断恢复和孤儿 tool call 修复；使用带 TTL 的文件锁防止 Session 并发写入。
- **工程化：** 独立维护 agent core、API、Web、CLI 与文档；约 3.2 万行 TypeScript；CI 覆盖 Linux / Windows，提供 bash / PowerShell 对等脚本和源码式安装流程。

### x-lisp / meta-lisp — 静态类型 Lisp、编译器与 VM · 独立开发 · 2025.07 – 至今

[lisp.xieyuheng.com](https://lisp.xieyuheng.com) · [源码](https://github.com/xieyuheng/x-lisp)

从零实现带静态类型的极简 Lisp、编译器、SSA IR、字节码 VM 与 x86-64 汇编器；约 8 万行 TypeScript / C / Lisp / Markdown，80+ 单元与快照测试。

- **系统与工程能力：** 独立实现 HM 类型推导、17-pass 编译流水线、mark-sweep GC、TLV / ELF 与 x86-64 后端；使用 perf、cache-miss 和内存分析定位性能瓶颈，并维护 Linux / Windows 双平台 CI。

## 工作经历

### 量匠科技（Quantisan） · 算法与全栈 · 深圳 · 2023–2024

面向机械制造企业的智能工厂流程引擎与零件工艺生成系统，团队约 50 人，直接向 CTO 汇报。

- **三维可视化前端：** 基于 STEP 文件与 OpenCascade 提取模型信息，使用 WebGL / three.js 在前端渲染零件模型、识别特征与每一步加工结果预览。
- **全栈交付：** 同时负责算法、后端与前端；使用 Python、TypeScript、Vue、Tailwind CSS、Node.js，实现工厂「工艺库」网站与工序自动分析界面。
- **特征识别：** 使用 Prolog 实现 Attributed Adjacency Graph（AAG）搜索，规则上百条；基于大量真实零件图纸验证，可稳定识别绝大多数加工特征。
- **技术攻关：** 调研大量论文并进行实验验证，解决拓扑 / 几何信息提取和特征识别算法设计问题，推动算法从研究原型落地为可用产品。

### 风变科技 · 产品技术负责人 · 深圳 · 2019.12–2021.11

面向内部老师与外部课程创作者的交互式课程创作平台「课灵小馆」；从 0 到 1 完成产品构思、命名与技术架构，服务 Python 编程、职业生涯规划、企业内训等多领域课程，累计十几位老师使用。

- **编辑器与实时预览：** 设计基于 YAML 的课程 DSL，使用 Vue、Tailwind CSS、CodeMirror、Node.js 实现在线编辑与实时预览；支持顺序、分支、循环和多类互动组件，面向非程序员创作者。
- **复杂内容组件：** 支持选择题、填空题、问答题、幻灯片、角色扮演、立绘、图片热区和 Canvas 动画等课程内容；创作者编写 DSL 后即可实时查看效果。
- **全栈平台：** 基于课程平台设计内部「卡牌」系统与「思索」复习系统，将主动回顾 / 间隔重复用于课程学习流程。
- **团队与交付：** 带领 3–4 人全栈团队，负责产品设计、技术架构、全栈开发、招聘面试、代码 review 与项目管理；采用敏捷开发、结对编程和定期技术分享。

### 早期经历 · RiceQuant · 后端 / 数据工程 · 2017–2018

- 爬虫、Web 后端与量化策略开发：Python、Flask、Scrapy、MongoDB、Pandas。

## 研究与教学经历

### 中国科学院软件研究所 PLCT 实验室 · 依赖类型语言研究合作 · 远程 · 2022

- 以 Cicada 项目为基础，与四名学员协作探索 Dependently Typed 程序语言；通过代码讲解和论文共读，帮助成员理解并实现 dependent type，参与 implicit argument 等高阶特性。

## 论文与授课

- *Function compose, Type cut, and The algebra of logic*，ICFP Scheme Workshop，2016，日本奈良；*A Recursive Combinatorial Description of cell-complex*，2019。
- 中山大学汇编语言实验课程，2015；课程材料与教学项目开源。

## 技术栈

**前端：** TypeScript、Vue、React、Tailwind CSS、CodeMirror、Canvas、WebGL、three.js、响应式布局、移动端适配  
**全栈：** Node.js、Hono、Python、Flask、REST API、SQL  
**工程化：** Vite、Git、GitHub Actions、Linux、Windows、测试、CI  
**其他：** C、Scheme / Lisp、Prolog、Scala、Elixir
