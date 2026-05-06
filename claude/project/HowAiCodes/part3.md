>>> 进阶篇：完整图景 #B21
@enter: zoom-in
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 bold title card on dark background (#0d1117). Large text "进阶篇" in bold white centered vertically. Below it subtitle "现代 AI 编程的完整图景" in blue (#58a6ff). Four topic previews in small muted text below: "上下文管理 / MCP 协议 / 多Agent / 动手实现". Subtle futuristic grid pattern in background. Clean, impactful typography.

--- narration ---
原理讲清楚了
但真实的 AI 编程工具
还要解决很多工程问题
这一段我们聊四个进阶话题


>>> 上下文窗口的天花板 #B22
@enter: fade
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 diagram on dark background (#0d1117). A tall container/glass visual metaphor representing the context window. The container is partially filled with colored layers representing messages (blue layers for assistant, green for user, gray for tool results). The container is almost overflowing at the top. A label showing "200K tokens" at the top edge. Above the container, more messages are trying to pour in but being blocked. Red warning icon. Below: a question mark "怎么办?" in accent blue (#58a6ff). Clean, visual metaphor style.

--- narration ---
第一个问题是上下文窗口
LLM 能处理的文本量有上限
目前最大约 **200K tokens**
听起来很多
但分析一个大项目时
读几十个文件就可能撑满
消息数组不能无限增长
超过上限怎么办？


>>> 上下文压缩 #B23
@enter: fade
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 layered diagram on dark background (#0d1117). A pyramid or funnel structure with 5 layers from top to bottom, each layer narrower than the one above. Layer 1 (widest, top): "裁剪冗余输出" - scissors icon, light blue. Layer 2: "微压缩对话" - compress icon, medium blue. Layer 3: "折叠早期上下文" - fold icon, darker blue. Layer 4: "自动摘要" - document-summary icon, deep blue. Layer 5 (narrowest, bottom): "跨会话记忆" - brain-save icon, darkest blue. Arrows flowing downward through layers. Side label: "信息逐层浓缩, 像一个智能遗忘系统". Clean gradient blue (#58a6ff to #1a3a5c) color scheme.

--- narration ---
Claude Code 用五层压缩策略
第一层裁剪工具输出的冗余部分
比如超长的日志只保留关键信息
第二层微压缩不太重要的对话轮次
第三层折叠最早期的上下文
第四层对整段对话做摘要
第五层把关键信息存为跨会话记忆
每一层都比上一层更激进
像一个智能的遗忘系统
优先保留最近和最重要的信息


>>> MCP：AI 工具的标准接口 #B24
@enter: fade-up
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 architecture diagram on dark background (#0d1117). Center: the MCP protocol shown as a USB-C connector icon in blue (#58a6ff), labeled "MCP - Model Context Protocol". Top half: multiple AI app icons (Claude Code, Cursor, Copilot) all connecting down to the MCP connector. Bottom half: multiple external service icons (database, file system, GitHub, Slack, browser) all connecting up to the MCP connector. The connector acts as a universal bridge. Side annotation: "AI 的 USB-C 接口". Clean, modern tech architecture diagram.

--- narration ---
第二个话题是 **MCP**
全称 Model Context Protocol
你可以把它理解为 AI 的 USB-C
一个统一的工具连接协议
任何 AI 应用
都可以通过 MCP 连接任何外部服务
数据库、文件系统、GitHub
浏览器、Slack 都可以接入
不用每个工具单独适配
一个协议解决所有连接问题


>>> 多 Agent 与后台 Agent #B25
@enter: fade
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 diagram on dark background (#0d1117). Two sections stacked vertically. Top section "多 Agent 协作": A parent agent node at top connecting to three child agent nodes below via branching lines. Each child has its own context window (shown as small containers). Labels: "主Agent分配任务", children labeled "子Agent 1", "子Agent 2", "子Agent 3". Results flow back up. Bottom section "后台 Agent": A cloud icon containing an agent working autonomously. Flow: GitHub Issue icon → cloud agent → code changes → test results → Pull Request icon. All happening without human interaction. Blue (#58a6ff) and white clean diagram style.

--- narration ---
更强大的是多 Agent 架构
主 Agent 把任务拆分
派给多个子 Agent 并行执行
每个子 Agent 有独立上下文
完成后向主 Agent 汇报
最前沿的是**后台 Agent**
你在 GitHub 提一个 Issue
Agent 在云端自动工作
写代码、跑测试、提 PR
你只需要做 Code Review
这已经不是未来
而是正在发生的现实


>>> 动手实现：最小 Agent #B26
@enter: fade-up
@exit: fade
@visual: animation

--- visual ---
全屏深色背景 (#0d1117)，竖屏 9:16 布局。
顶部居中标题 "最小 Agent 伪代码"，字号 44px，粗体白色，距顶部 80px。
标题下方 40px 处出现一个代码块区域，宽度占画布 90%，背景 #161b22，圆角 16px，内边距 24px。
代码使用等宽字体，字号 22px，行高 1.6。

[0s] 代码块淡入，显示以下 Python 伪代码（语法高亮）：

```python
tools = [read_file, write_file, bash]
messages = [{"role":"user", "content": 用户输入}]

while True:
    response = call_llm(
        system_prompt,
        tools,
        messages
    )
    messages.append(response)

    if response.has_tool_calls:
        for tool_call in response.tool_calls:
            result = execute(tool_call)
            messages.append(result)
    else:
        print(response.text)
        break
```

[2s] 右侧逐行出现注释标注（字号 18px，颜色 #58a6ff），用细线指向对应代码行：
第1行旁标注 "① 定义工具"
第2行旁标注 "② 初始化记忆"
第4-8行旁标注 "③ 调用 LLM"
第9行旁标注 "④ 追加到记忆"
第11-13行旁标注 "⑤ 执行工具"
第15行旁标注 "⑥ 输出结果"

--- narration ---
理解了原理
自己实现也不难
核心就是一个 while 循环
首先定义可用的工具列表
初始化消息数组
然后循环调用 LLM
把 LLM 的回复追加到消息数组
如果回复包含工具调用
就执行工具并把结果追加
如果是普通文本就输出给用户
不到二十行代码
你就有了一个能用的 Agent


>>> 从玩具到产品 #B27
@enter: fade
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 iceberg diagram on dark background (#0d1117). An iceberg illustration. Above the waterline (small visible portion, about 20%): labeled "Agent 循环" with a simple loop icon, glowing blue (#58a6ff). Below the waterline (large hidden portion, about 80%): multiple labeled layers - "权限与安全控制", "上下文压缩管理", "错误恢复与重试", "并发与性能优化", "工具路由与协议", "持久化与记忆". The waterline is clearly marked. Text at bottom: "核心逻辑只占 1.6%，工程占 98.4%". Clean, dramatic iceberg illustration style.

--- narration ---
但从玩具到产品
差距非常大
有研究分析了 Claude Code 的代码
核心 Agent 循环只占 1.6%
其余 98.4% 都是工程代码
权限控制、安全检查
上下文压缩、错误恢复
并发优化、工具路由
这些才是真正的挑战
但理解了核心原理
你就有了正确的起点


>>> 总结：三个核心洞察 #B28
@enter: fade-up
@exit: fade
@visual: image

--- visual ---
Vertical 9:16 summary card on dark background (#0d1117). Three large numbered insight cards stacked vertically with generous spacing. Card 1: Large "01" in blue (#58a6ff), text "LLM 无状态，Agent 用消息数组模拟状态" in white. Card 2: Large "02" in blue, text "工具调用让 LLM 操作真实世界" in white. Card 3: Large "03" in blue, text "Agent 循环 = while + API + 工具执行" in white. Each card has a dark background (#161b22) with left blue border accent. Bottom: small muted text "理解这些，你就理解了 AI 编程的本质". Clean, impactful summary layout.

--- narration ---
最后总结三个核心洞察
第一 **LLM 是无状态的**
Agent 通过消息数组模拟状态
每次调用都发送完整历史
第二 **工具调用**是关键突破
让只会说话的 LLM
能操作文件、运行命令
第三 **Agent 循环**是核心引擎
本质就是 while 加 API 加工具
理解了这三点
你就真正理解了 AI 编程
去动手实现一个吧
