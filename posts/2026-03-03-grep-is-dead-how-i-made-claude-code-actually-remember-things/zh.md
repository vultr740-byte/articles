---
title: "Grep 已死：我是如何让 Claude Code 真的“记住”东西的"
description: "和 Claude Code 的每一次对话都从零开始。下面是我如何用一个本地搜索引擎和一个技能，在你敲下第一个字之前就把完整上下文加载出来。"
layout: default
---
来源（X 文章）：https://x.com/i/article/2028328572272742401

和 Claude Code 的每一次对话都从零开始。下面是我如何用一个本地搜索引擎和一个技能，在你敲下第一个字之前就把完整上下文加载出来。

和 Claude Code 的每一次对话都从零开始。我在 3 周里跑了 700 个 session，而我根本不记得当时发生了什么。我对事情的进展正在失去掌控。

---

我只要打开一个新终端，然后就……现在到底在干嘛？我需要想办法找回所有这些上下文：项目是什么、做过哪些决策。我不能一直从零开始。

更糟的是在 session 进行到一半的时候。当我们在 60% 处撞上上下文上限，就得压缩或交接。然后一半的决策就丢了。或者更糟，如果我想第二天继续，我不记得当时发生了什么。

---

靠在文件里 grep 的旧范式无法扩展。所以我把 QMD 接进了我的 vault——一个由 Shopify CEO Tobias Lutke @tobi 打造的搜索引擎。加拿大制造。

接下来我要展示的整套记忆系统——它是一个技能。2 分钟安装好，Claude Code 就已经知道怎么用它了。

## QMD：你的 Vault 本地搜索引擎

QMD 是一个面向知识库的本地搜索引擎。它会为你的 Obsidian @obsdmd  vault 建索引，并在 1 秒内找到任何东西。

对 vault 里的每个文件夹，我都有一个 QMD collection：笔记、日记条目、sessions、转录文本。一对一映射。对每个 collection 我都可以做聚焦搜索。

---

---

就这样。一个命令，你的 vault 就可搜索了。

但怎么搜？Claude Code @claudeai 默认的搜索方式是蛮力——它会派一个 Haiku 子代理去 grep 每一个文件。我试过（看视频）：用常规方式搜索我关于不同图（graph）方法的笔记。花了 3 分钟。我等得无聊，一边刷 Twitter。一看结果？300 个文件，不怎么样。

QMD 搜索是秒出。结果更好。token 少得多。你也不需要用子代理来做。差别在于：grep 匹配字符串，而 QMD 按相关性排序。

## Grep vs BM25 vs semantic search

QMD 提供三种搜索模式。BM25（`qmd search`）是确定性的全文搜索。像 grep 一样，它匹配精确关键词。不同于 grep，它会给每个文件打分：这个词出现得有多频繁？在所有文档里有多稀有？一篇短笔记里提到 5 次“sleep”，得分会高于一个 10,000 字文件里只出现 1 次“sleep”。没有 AI、没有 embeddings——只有数学。semantic search（`qmd vsearch`）用 embeddings 来找“含义”——即使没有出现完全相同的词，你也能搜一个概念。Hybrid（`qmd query`）把两者结合起来。

我在整个 vault 里搜了 “sleep”。差别如下。

---

Grep 找到 200 个文件。到处都是。它会找出所有包含单词 “sleep” 的文件。它甚至会找到 `sleep()`——一个让代码暂停执行的编程命令。这和真实的睡眠没关系。这就是字符串匹配的问题。

BM25（2 秒）：一篇关于睡眠质量的反思。一个跟踪睡眠碎片化模式的实验。凌晨 3 点被打断的睡眠。好得多。

但 `qmd search "insomnia"` 返回 0 个结果。因为 vault 里根本没有这个词。

semantic search：我输入 `qmd vsearch "couldn't sleep, bad night"`，它找到了我多年前设定的一个睡前纪律目标。它超越关键词，去探索含义。5 个结果里有 4 个根本不包含这些搜索词。Grep 永远找不到它们。

Hybrid（`qmd query "couldn't sleep, bad night"`）：把睡眠质量提升排到 89%，凌晨 3 点睡眠中断排到 51%，健康睡眠优化排到 42%。综合排名最好。

---

从 BM25 开始。快，也能处理结构化笔记。覆盖 80% 的搜索。对转录文本和脑暴记录再加 semantic search——因为你永远不会用那些“精确词”去搜它们。

## semantic search 实际会浮现什么

我在日记里搜：“找出我开心的那些天，以及原因是什么。”

这是个并不简单的查询。Claude 会自适应它，并跑多次搜索：

---

它在几个月的日记里找到了语义相关的连接。

模式是：我最开心的日子，是我发布了某个东西并且睡眠恢复得很好，比如桑拿或睡了 9 小时。

然后它还挖出了一件我完全忘掉的事。去年 10 月我在写博士论文，差点放弃。我需要一天一天地做：来，写点东西。但当时我意识到，我需要穿过不适感，而不是逃去寻求快速修复。

我不记得自己写过这个。我没想到搜索能把它翻出来。但它就在那里——精确引用。

## /recall ——在开始之前加载上下文

`/recall` 是一个构建在 QMD 之上的 Claude Code 技能。它会在你开始工作前加载上下文——你不需要向 Claude 解释你在做什么，只要让它 recall。

它有三种模式：时间（按日期扫描你的 session 历史）、主题（在你的 QMD collections 上做 BM25 搜索）、图（对 sessions 和文件做交互式可视化）。

---

/recall yesterday

/recall topic graph

/recall graph last week

“Yesterday” 从一天里重建了 39 个 sessions：时间线、每个 session 的消息数、每个时段做了什么。

---

“Topic” 在 sessions 和笔记里搜索 “QMD video”。返回了仪表盘、制作计划、待办清单。所有相关文件在 1 分钟内浮现。对比蛮力做法：你告诉 Claude“找出这个项目的所有信息”，它会派 Haiku 去 grep 你的 vault 3 分钟、烧 token、回来还给更差的结果。用 `/recall topic`，我重建了项目的完整状态，然后问：下一步最高杠杆的动作是什么？

“Graph” 打开了我整周的交互式可视化：sessions 是彩色团块，老的更暗，新的用紫色高亮。文件按类型聚类：目标、研究、语音、文档、内容、技能。

举个例子：我在探索午餐地点。我跟 Claude 说“我想吃一顿很棒的午餐”，我们分析了不同的去处。我把它们存成可能想尝试的活动。一周后，我打开 graph，看到那个 session，把那些文件路径复制进 Claude Code，从那里继续。graph 让每一次过去的对话都可恢复。

---

---

## 700 个 sessions，全部可搜索

Claude Code 会把你所有对话以 JSONL 文件形式保存在电脑上。我在过去 3 周有 700 个 sessions。每一个里面都有我之后还会需要的决策、问题、上下文。

原始文件里有工具调用、system prompts、角色等。我们会解析出清晰的 markdown——真正的信号、真正的用户消息——并把这些 embed 进 QMD 索引。

每次 session 结束、我关掉终端时，我有一个 hook 会把这些 sessions 导出并 embed 到 QMD。这样索引永远是最新的。我可以回到任何我做过的事，哪怕是今天的。

---

## 找到你从未行动过的想法

这个最让我意外。我搜：“找出我从未行动过的想法。”

让 Claude 综合原始 QMD 结果非常有用——你自己很难读懂原始结果。Claude 总结了它找到的内容：

- 10 月 19 日——我想做一个博士写作仪表盘，但一直没做  
- 我有一些基于插画的 app 想法，但从未推进  
- 我有个想法录一段关于我完整 Obsidian 工作流的屏幕录制，但从未真正开始  

---

而且这一切都在本地——所有这些 embeddings 都在你的电脑上。

## 工具会变。你的上下文会留下。

你的笔记不再是被动的。它们不再困在 Obsidian 的世界里。它们开始真的做事情。你所有笔记，都是关于你自己的有用上下文，你可以用来实现人生目标。

工具会变。一个月后会有新模型。那又怎样。如果你有上下文，你就能在任何情况下让它运转起来。Claude Code、Codex、Gemini CLI，任何都行。

这层记忆层作为一个技能，贯穿你整个技术栈。我用 Obsidian Sync 让我的 vault 在 Mac 和一台始终在线的 Mac Mini 之间保持同步。在 Mac Mini 上，OpenClaw 24/7 运行。所以我拿起手机，打开 OpenClaw，就能从任何地方拥有同一个 vault、同一个 QMD 索引、同一套技能。

---

## 下一步

下载 `/recall` 技能，把它丢进你的 `.claude/skills/` 文件夹里，你今天就能用上 session pipeline 和 recall（或者让 Claude 帮你做也行 :) ）。

QMD——这一切背后的搜索引擎。作者 Tobias Lutke。

https://github.com/tobi/qmd

观看完整视频——42 分钟演示讲解与现场 demo。

https://youtu.be/RDoTY4_xh0s

Artem
