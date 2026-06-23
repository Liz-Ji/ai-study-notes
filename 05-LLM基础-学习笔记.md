# LLM 基础 · 大白话学习笔记（Stage 1）

> 这份笔记把 LLM 的核心原理和概念用大白话讲清楚，配你的实战记录。
> 一句话记住 LLM：**它是一个读遍海量文字、靠「猜下一个词」来接龙的超级高手。**

---

## 目录

1. [LLM 到底是什么（接龙原理）](#1-llm-到底是什么接龙原理)
2. [三大核心概念：token / context window / temperature](#2-三大核心概念)
3. [token：文字积木块](#3-token文字积木块)
4. [context window：AI 的工作台](#4-context-windowai-的工作台)
5. [temperature：脑洞旋钮](#5-temperature脑洞旋钮)
6. [各家模型怎么分、怎么选](#6-各家模型怎么分怎么选)
7. [价格：同一家也分高中低档](#7-价格同一家也分高中低档)
8. [实战：调用第 2 个 LLM（DeepSeek）](#8-实战调用第-2-个-llmdeepseek)
9. [代码追问解惑](#9-代码追问解惑)
10. [API vs 网页聊天：为什么要写代码](#10-api-vs-网页聊天为什么要写代码)
11. [Stage 1 自我检查](#11-stage-1-自我检查)
12. [小抄总结](#12-小抄总结)

---

## 1. LLM 到底是什么（接龙原理）

**LLM = 一个「超级文字接龙高手」。** 全称 Large Language Model（大语言模型），不用记英文。

- 你手机打字会自动猜下一个字（"今天天气真" → "好"），**LLM 干的本质一样，只是猜得极聪明。**
- 它**一个 token 一个 token 地往后接**，滚雪球一样接出一整段话/一段代码。它不是"一下子想好整段"，而是不停猜"下一个最可能是啥"。
- 它为什么准？因为它**读过海量文字**（书、网页、代码……几乎整个互联网），学到了"词和词之间的规律"。

**关键认知：LLM 不是真的"理解"或"知道"，它在做极高级的"概率预测"。**
- 它说"中国的首都是北京"，不是因为它"知道"，而是因为它读过的文字里，这后面接"北京"概率最高。
- 这就是为什么它有时会**一本正经说错话**（叫"幻觉"）——它是在猜最可能的词，不是查证真相。猜得通常很对，但不保证 100% 对。

| 你以为 LLM 在干嘛 | 它实际在干嘛 |
|---|---|
| 思考、理解你的问题 | 猜"下一个 token 最可能是啥" |
| 一下子想好整段回答 | 一个 token 一个 token 接龙 |
| 知道事实、查资料 | 凭"读过的海量文字"算概率 |

---

## 2. 三大核心概念

教材强调这 3 个词贯穿后面所有阶段，必须吃透：

| 词 | 中文 | 一句话 |
|---|---|---|
| **token** | 词元 | 模型算文字长度和费用的基本单位（中文 1 字 ≈ 1.5~2 token）|
| **context window** | 上下文窗口 | 模型一次能看多少 token |
| **temperature** | 随机度 | 控制回答更稳定还是更发散（0=最稳，1=最有创意）|

---

## 3. token：文字积木块

**token = LLM 眼里的"文字积木块"。** 它不按"字/词"看文字，而是切成一块块 token 来处理、接龙。

**怎么切（记手感，不用记精确数）：**
- 英文：一个词 ≈ 1 token，长词会切成几块（`unbelievable` → un/believ/able）
- 中文：一个汉字 ≈ 1~2 token（比英文更"费"token）
- 标点、空格也各算一点

**token 和钱：**
- 你发的话 → input tokens；AI 回的话 → output tokens
- 账单 = (input + output) 的 token 数 × 单价
- `max_tokens` = 给**输出**封顶，省钱

---

## 4. context window：AI 的工作台

**context window（上下文窗口）= AI 的"工作台大小"，一次最多能看多少 token。**

- 工作台就那么大，你给的内容（问题 + 对话历史 + 文档）加起来不能超上限。
- 超了：要么报错，要么"忘掉"最前面的内容（积木摆不下，最早的被挤掉）。
- 窗口越大 = 一次能塞的内容越多。有的模型号称"能读整本书"，就是窗口特别大。

> 参考（会变）：Claude 旗舰 ~1M、GPT ~400k、Gemini ~2M、Kimi 1M+。

---

## 5. temperature：脑洞旋钮

**temperature = 控制 AI 回答"多稳定 vs 多发散"的旋钮（0~1）。**

接上接龙原理：AI 猜下一个词时，心里有一排候选词带着概率（如"好"60%、"不错"25%…）。temperature 决定它挑词时**多保守还是多大胆**：

| temperature | 像炒菜 | AI 表现 |
|---|---|---|
| 低（0~0.3）| 小火慢炖 | 总挑最可能的词，**稳定、保守、可复现** |
| 高（0.7~1.0）| 大火爆炒 | 敢挑冷门词，**有创意、多样、爱跑题** |

**什么时候用哪个：**

| 任务 | temperature | 为什么 |
|---|---|---|
| 提取信息/分类/写代码/算数 | 低（0~0.3）| 要准确稳定，别瞎发挥 |
| 写诗/起名/头脑风暴/创意 | 高（0.7~1.0）| 要多样有灵感 |
| 一般聊天问答 | 中（0.5~0.7）| 平衡 |

**它解释的现象：** 同一个问题 AI 每次答得不一样，就是因为默认 temperature 不为 0，挑词带点随机。

**代码里怎么用**（又是改那个"点单"请求）：
```python
message = client.messages.create(
    model="claude-haiku-4-5",
    max_tokens=200,
    temperature=0.2,        # 想稳定调低，想发散调高
    messages=[{"role": "user", "content": "..."}]
)
```

---

## 6. 各家模型怎么分、怎么选

**第一刀：按"在哪跑"分两大阵营**

| 阵营 | 大白话 | 代表 |
|---|---|---|
| 云端商业模型 | 跑别人服务器、付费调 API、不用好电脑 | Claude、GPT、Gemini、DeepSeek、Kimi |
| 开源本地模型 | 下载到自己电脑跑、免费离线隐私 | Llama、Qwen、Gemma、Mistral |

> 比喻：云端=打车（按次付费）；本地=买车（前期要好电脑，之后随便开不花钱）。

**第二刀：云端按国别**——美国系（Claude/GPT/Gemini，最强但国内访问付费麻烦）、中国系（DeepSeek/Kimi/Qwen/GLM，中文强、国内方便、常送额度）。

**关键认知：不存在"最好的模型"，只有"最适合某任务的"。**

| 你要干啥 | 选谁 |
|---|---|
| 第一次学、教材最全 | Claude |
| 写长文/改代码 | Claude |
| 读 PDF/图/影音 | Gemini 或 Kimi |
| 中文 + 想省钱 | DeepSeek |
| 中文 + 超长文档 | Kimi（窗口超大）|
| 免费/离线/隐私 | Llama、Qwen、Gemma（用 Ollama 本地跑）|

**记住一句话：贵旗舰聪明但费钱，便宜/小模型够用又省钱；学习实验用便宜的，重要任务才上旗舰。**

---

## 7. 价格：同一家也分高中低档

**每家都有"高中低"三档，越聪明越贵。** 以 Claude 为例：

| 档次 | 型号 | 特点 | 跑 1000 次约花费 |
|---|---|---|---|
| 🏆 旗舰 | Opus | 最强、难任务 | ~$1.27 |
| ⚖️ 均衡 | Sonnet | 够聪明、性价比 | ~$0.76 |
| 🐎 轻快 | Haiku | 快、便宜、简单任务 | ~$0.25 |

**关键对比（自我检查③）：** Opus 比 Sonnet 贵约 1.7 倍，Sonnet 比 Haiku 贵约 3 倍。同样的活 Haiku 花 2 毛 5、Opus 花 1 块 2，**差约 5 倍**。所以**练习/跑量用 Haiku，关键难题才上 Opus**，省一大笔。

> 价格会变，查实时价去官方定价页（如 anthropic.com/pricing）。

---

## 8. 实战：调用第 2 个 LLM（DeepSeek）

验证了"换家只改 3 处"。DeepSeek 兼容 OpenAI 格式，所以**复用 openai 包**。

```python
from openai import OpenAI
import os

client = OpenAI(
    api_key=os.environ["DEEPSEEK_API_KEY"],   # 从环境变量取 key
    base_url="https://api.deepseek.com"        # ← 指向 DeepSeek（建客户端时给）
)

response = client.chat.completions.create(
    model="deepseek-chat",                     # ← DeepSeek 的模型名
    max_tokens=200,
    messages=[{"role": "user", "content": "用一句话介绍你自己"}]
)

print(response.choices[0].message.content)
```

**对比 Claude 版，只有 3 处变（★）：**

| | Claude | DeepSeek |
|---|---|---|
| ① 包 | `import anthropic` | `from openai import OpenAI` ★ |
| ② 客户端 | `anthropic.Anthropic()` | `OpenAI(base_url="...deepseek...")` ★ |
| ③ 模型名 | `claude-haiku-4-5` | `deepseek-chat` ★ |
| 点单 messages | — | **完全没变** |

> DeepSeek 国内注册付费方便、常送免费额度、是 cloud 里最便宜之一。

---

## 9. 代码追问解惑

**Q：`import os` 和 `import OpenAI` 啥关系？**
A：import = 把工具箱拿来用，可同时拿多个。`OpenAI` 负责调 AI；`os`（operating system）负责"从环境变量里取钥匙"。`os.environ["DEEPSEEK_API_KEY"]` = 去系统的"空气"里把你设的 key 取出来。

**Q：为什么要"建客户端"？指向 DeepSeek 不是 model 体现了吗？**
A：不是。分清两件事——
- `base_url`（在客户端里）= **打给哪家公司**（DeepSeek 的服务器地址）
- `model`（在请求里）= **要这家的哪个型号**（deepseek-chat）

打电话比喻：建客户端 = 拨通 DeepSeek 总机（`base_url` 是号码、`api_key` 是会员卡）；model = 接通后说"转接 deepseek-chat"。客户端 = 你和 AI 之间"已拨通、验过身份的专线电话"。

---

## 10. API vs 网页聊天：为什么要写代码

**如果只是想"聊天"，直接用网页/App 比写代码方便一万倍。** API 的价值不在聊天，而在**让程序自动、批量、按你的逻辑去用 AI**：

| 网页聊天 | API（写代码）|
|---|---|
| 手动一问一答 | 一次处理 1000 个问题（批量分类等）|
| 只有你本人能聊 | 做成 App/网站给别人用 |
| AI 只能"说" | 让 AI 自动读文件/改文件/调工具 = **agent** |
| 聊完就没 | 接进工作流，自动/定时跑 |

**一句话：网页聊天是"你亲自跟 AI 对话"；API 是"你写好规则，让程序替你成千上万次指挥 AI 干活"。**

而整个学习地图的终点是做 **agent（智能体）**——能自己读资料、做决定、调工具的程序，底层全靠 API。今天的 `hello.py` 就是 agent 世界的第一块砖。

> 而且代码写一次存成文件，以后 `python 文件名` 反复跑，不用每次重敲。

---

## 11. Stage 1 自我检查

教材毕业标准（4 条全过 = 进 Stage 2）：

- [x] 写一个 5 行 Python 脚本调用 Claude API ✅（hello.py）
- [x] 理解基础概念、能数 token ✅
- [x] 比较 Claude Sonnet vs Opus 的 per-token 价格 ✅（Opus≈Sonnet×1.7）
- [x] 体验至少 2 个不同的 LLM ✅（Claude + DeepSeek）

**🎓 四条全过，Stage 1 毕业，下一站 Stage 2 提示工程。**

---

## 12. 小抄总结

| 概念 | 一句话 |
|---|---|
| LLM | 读遍海量文字、靠猜下一个 token 接龙的高手；会"幻觉" |
| token | 文字积木块（接龙/计费/限长都按它）|
| context window | AI 工作台大小，一次能看的 token 上限 |
| temperature | 脑洞旋钮：低=稳、高=野 |
| max_tokens | 给输出封顶、省钱 |
| 选模型 | 没有最好只有最合适；学习用便宜的、关键用旗舰 |
| 三档价格 | Opus > Sonnet > Haiku，差约 5 倍 |
| 换家 | 只改 3 处：包名、客户端、模型名；国产多兼容 OpenAI |
| API 的意义 | 让程序批量、自动地指挥 AI 干活（agent 的地基）|

**一句话总结：LLM 靠 token 接龙，工作台是 context window，脾气由 temperature 调；学会用 API 指挥它，就是通往 agent 的第一步。**
