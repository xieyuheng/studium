# Uncoupling

2020-09-17

今天半夜看了 GOTO 2018 • Uncoupling • Michael Nygard

coupling 的定义是限制系统的自由度。
比如膝盖，限制腿弯曲的自由度于一个平面之内。

其中提到了分析 coupling 的四种角度。
提到了 coupling 对于完成系统的功能来说是必要的。
（否则不同的部件就不构成系统了）

有趣的观点是，interface 与 data type 数量越少，
整个软件系统，越可以灵活复合。

提到了用 interface 来代替模块之间的直接依赖。

我可以在我的语言实现项目中考验一下这些想法。
- 记得我之前是从 pettern matching 换成了 class method，
  然后又换回了 pettern matching。
  语言实现不适合用 interface 吗？

2020-09-18

不行，在写解释器的时候，我们还是应该坚持以数据为中心。
数据与处理它的函数之间的 coupling，带来了一些 API 表层的便利，但是得不偿失。
