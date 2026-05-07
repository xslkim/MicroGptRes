>>> 开场：AI 编程的困惑 #B01
@enter: fade-up
@exit: fade
@visual: image(./image01.png)

--- visual ---
本地插图 `./image01.png`（与 `part1.md` 同目录；构建直接复制，不参与文生图）。文档参考：开场「AI 编程的困惑」，竖屏 9:16，画中居中 contain。

--- narration ---
你用过 AI 写代码吗
比如 **Claude Code**
你给它一个需求
它就开始读文件、改代码
运行测试、修复错误
一整套操作行云流水
看起来像一个有记忆的程序员
但 **LLM** 是没有记忆的
每次调用都是全新的开始
那它是怎么做到的？
今天我们从底层原理讲清楚


>>> LLM 的本质：无状态函数 #B02
@enter: fade
@exit: fade
@visual: image(./image02.png)

--- visual ---
本地插图 `./image02.png`。文档参考：「LLM 的本质：无状态函数」，竖屏 9:16，画中居中 contain。

--- narration ---
我们从最底层讲起
**LLM** 本质上就是一个函数
给它一段文本输入
它返回一段文本输出
就这么简单
它没有硬盘
也没有数据库
没有任何地方存储记忆
每次调用之间完全独立
上一次说了什么它全忘了


>>> 聊天记忆的真相 #B03
@enter: fade
@exit: fade
@visual: image(./image03.png)

--- visual ---
本地插图 `./image03.png`。文档参考：「聊天记忆的真相」，竖屏 9:16，画中居中 contain。

--- narration ---
那聊天时的"记忆"呢
真相非常简单
**每次调用都发送完整对话**
第一轮只发你的提问
第二轮发上轮问答加新问题
第三轮发所有历史加新问题
就像每次都重新读一遍聊天记录
这就是所谓"记忆"的真相
不是 LLM 记住了什么
而是我们每次都重复告诉它


>>> 工具调用：LLM 的双手 #B04
@enter: fade-up
@exit: fade
@visual: image(./image04.png)

--- visual ---
本地插图 `./image04.png`。文档参考：「工具调用：LLM 的双手」，竖屏 9:16，画中居中 contain。

--- narration ---
但聊天只能生成文字
怎么操作电脑呢
答案是 **工具调用**
LLM 不直接执行任何操作
它只输出一段结构化的指令
比如这样的 JSON 文本
"请写入这个文件"
本质上还是在生成文字
真正执行的是外部程序
程序拿到指令完成动作
再把结果写回消息数组
LLM 看到结果继续思考


>>> System Prompt：Agent 的说明书 #B05
@enter: fade
@exit: fade
@visual: image(./image05.png)

--- visual ---
本地插图 `./image05.png`。文档参考：「System Prompt：Agent 的说明书」，竖屏 9:16，画中居中 contain。

--- narration ---
还有一个关键组件
叫 **System Prompt**
它是每次 API 调用都附带的指令
告诉 LLM 它是谁
有哪些工具可以用
应该遵循什么规则
还包含环境信息
比如操作系统类型
就像给新员工的岗位说明书
**Claude Code** 的 System Prompt
长达几千个 token
定义了它所有的行为规范


>>> Agent 循环：核心引擎 #B06
@enter: fade-up
@exit: fade
@visual: image(./image06.png)

--- visual ---
本地插图 `./image06.png`。文档参考：「Agent 循环：核心引擎」，竖屏 9:16，画中居中 contain。

--- narration ---
把这些拼在一起
就是 **Agent 循环**
用户发出请求
LLM 思考后决定调用工具
程序执行工具返回结果
LLM 看到结果继续思考
可能再次调用工具
也可能认为任务已完成
这个不断循环的过程
就是 AI 编程的核心引擎
说穿了它就是一个 while 循环
没有任何神秘的地方


>>> 五大组件总结 #B07
@enter: fade
@exit: fade
@visual: image(./image07.png)

--- visual ---
本地插图 `./image07.png`。文档参考：「五大组件总结」，竖屏 9:16，画中居中 contain。

--- narration ---
总结一下
AI 编程只需要五样东西
**LLM** 负责思考和决策
**System Prompt** 定义角色规则
**工具** 让 LLM 操作真实世界
**消息数组** 充当对话记忆
**Agent 循环** 把一切串联起来
没有魔法
没有神秘的"智能体"
就是一个循环不断调 API
听起来简单？
接下来我们看真实场景


>>> 过渡：进入实战 #B08
@enter: zoom-in
@exit: fade
@visual: image(./image08.png)

--- visual ---
本地插图 `./image08.png`。文档参考：「过渡：进入实战」，竖屏 9:16，画中居中 contain。

--- narration ---
接下来我们用三个真实场景
追踪 AI 编程的每一步
看看每次 LLM 调用
到底输入了什么
又输出了什么
