# API · 大白话学习笔记

> 这份笔记把"用 API 调用 AI"从原理到实操讲清楚，配你的真实练习记录和"换别家"的通用方法。
> 一句话记住 API：**它是帮你跟某个服务（比如 AI）打交道的「服务员」——你点单（发请求），它上菜（回结果）。**

---

## 目录

1. [什么是 API（大白话）](#1-什么是-api大白话)
2. [一次 AI 调用的完整流程](#2-一次-ai-调用的完整流程)
3. [三个高频词：请求 / 响应 / API key](#3-三个高频词请求--响应--api-key)
4. [token 与计费](#4-token-与计费)
5. [实战复盘：第一次调用 Claude](#5-实战复盘第一次调用-claude)
6. [代码骨架（四步）](#6-代码骨架四步)
7. [安全铁律](#7-安全铁律)
8. [换一家 AI 怎么做](#8-换一家-ai-怎么做)
9. [怎么找包名 / base_url / 模型名](#9-怎么找包名--base_url--模型名)
10. [飞书等"工具平台"和"AI 大脑"的区别](#10-飞书等工具平台和-ai-大脑的区别)
11. [踩过的坑](#11-踩过的坑)
12. [小抄总结](#12-小抄总结)

---

## 1. 什么是 API（大白话）

**API = 餐厅的服务员。**

你去餐厅，不会冲进厨房自己做菜，你只要跟**服务员**说"我要番茄炒蛋"，他就跑去厨房把菜端给你。API 就是这个服务员：你（你的程序）有需求，不用自己实现复杂功能，只要按规矩对 API 说"我要 XX"，它就去后台把结果取来给你。

放到 AI 上：那个庞大的 AI 模型跑在远方服务器，你电脑里没有它。你通过 **AI 的 API** 跟它对话——把话递过去，它把回答送回来。

> 全称 Application Programming Interface（应用程序接口），不用记英文。记住：**API = 帮你跟某个服务打交道的中间人。**

---

## 2. 一次 AI 调用的完整流程

```
①你的程序  ──点单(请求)──►  ②AI的API  ──►  ③AI模型思考
                                                   │
④你拿到回答  ◄──上菜(响应)──  AI的API  ◄─────────┘
```

一句话：**你递一段 JSON 过去 → AI 想 → 它递一段 JSON 回来。**

**你发的"点单"长这样**（就是一段 JSON，你现在能读懂）：

```json
{
  "model": "claude-haiku-4-5",
  "max_tokens": 200,
  "messages": [
    { "role": "user", "content": "用一句话介绍你自己" }
  ]
}
```

| 字段 | 大白话 |
|---|---|
| `model` | 用哪个 AI 模型（指定哪位厨师）|
| `max_tokens` | 最多回多长（封顶，防止烧钱）|
| `messages` | 你要说的话（一个清单 `[ ]`，能装多轮对话）|
| `role` | 谁说的（`user`=你 / `assistant`=AI）|
| `content` | 具体说的内容 |

**它回的"上菜"也是 JSON**：

```json
{
  "content": "你好！我是 Claude……",
  "usage": { "input_tokens": 8, "output_tokens": 20 }
}
```

`content` 是 AI 的回答；`usage` 是这次用量（套娃 `{ }`，关系到花多少钱）。

> 💡 **这就是为什么先学 JSON**——请求和响应全是 JSON。

---

## 3. 三个高频词：请求 / 响应 / API key

| 词 | 大白话 |
|---|---|
| **请求 request** | 你递过去的"点单"（发出的 JSON）|
| **响应 response** | 它端回来的"菜"（返回的 JSON）|
| **API key 密钥** | 你的"会员卡/付款码"——证明你是谁、按你账户计费 |

API key 像 GitHub 的 token：一串证明身份的钥匙（`sk-ant-...`）。用 AI 的 API 要花钱（按 token 计费），所以要 key 来识别身份、结账。

---

## 4. token 与计费

- **token ≈ AI 眼里的"字数单位"**（大致一个英文词或一两个汉字算一个，不用纠结精确）。
- 你发的话算 **input tokens**，AI 回的话算 **output tokens**。
- **API 按 token 收费**：发得多、回得长，花得多。
- `max_tokens` 能给回答"封顶"，练习时设小一点（比如 200）+ 问短问题 = **省钱**。
- 练习用最便宜的模型（如 Claude Haiku），一次调用通常**不到 1 美分**，$5~$20 能练成千上万次。

---

## 5. 实战复盘：第一次调用 Claude

### 阶段 A：一次性准备（设好不用重来）

| 步骤 | 做了啥 | 大白话 |
|---|---|---|
| 注册 + 充值 | 在 `console.anthropic.com` 注册、充值 | 办会员卡、存点钱 |
| 生成 API key | 控制台 → Get API key → 生成 `sk-ant-...` | 拿到专属钥匙（复制保存好，只显示一次）|
| 装工具包 | `pip install anthropic` | 装"傻瓜工具箱" |

### 阶段 B：每次写程序调用（日常）

| 步骤 | 命令/代码 | 大白话 |
|---|---|---|
| 设 key | `$env:ANTHROPIC_API_KEY = "你的key"` | 把钥匙放进"当前窗口的空气里" |
| 验证 key | `$env:ANTHROPIC_API_KEY.Length`（看到数字=成功）| 只看长度、不暴露内容 |
| 建文件 | `New-Item hello.py` | 建个空程序文件 |
| 写代码 | `notepad hello.py` 粘贴代码 | 写"点单→上菜"逻辑 |
| 运行 | `python hello.py` | 跑起来，听 AI 回话 |

> ⚠️ 环境变量只在**当前 PowerShell 窗口**有效，关窗口就没了，下次要重设。这是安全特性，练习够用。

---

## 6. 代码骨架（四步）

```python
import anthropic                          # ① 拿出工具箱
client = anthropic.Anthropic()            # ② 建客户端（自动读环境变量里的 key）
message = client.messages.create(         # ③ 点单：发请求
    model="claude-haiku-4-5",             #    用哪个模型
    max_tokens=200,                       #    最多回多长（封顶省钱）
    messages=[
        {"role": "user", "content": "用一句话介绍你自己"}
    ]
)
print(message.content[0].text)            # ④ 上菜：取出回答打印
```

**记住这个四步骨架：拿工具箱 → 建客户端 → 点单 → 取回答。所有 AI 的 API 调用骨架都长这样。**

注意：代码里**完全没有出现 key**——靠 `anthropic.Anthropic()` 自动读环境变量。所以代码能随便分享，里面没有钥匙。

---

## 7. 安全铁律

API key 能花你的钱，等同账号钥匙。务必：

1. **用环境变量传 key，别写死在代码里**（`api_key = "sk-ant-..."` ❌ 危险）。
2. **含 key 的内容绝不截图、绝不发给任何人。**
3. **装 key 的代码别放进连着 GitHub 的 git 项目**——一不小心 `git push`，key 就公开了。所以练习代码放**独立的、非 git 项目**的文件夹。
4. 万一 key 泄露：立刻去控制台**作废（Delete/Revoke）**，再生成新的。
5. 在控制台设个**用量上限**（spending limit），防止代码写错死循环烧钱。

> 三个层次别混淆：
> - **工具包**：`pip install` 装在**电脑的 Python**里，一次装好，所有文件夹通用。
> - **key**：`$env:...` 设在**当前窗口**里，关窗口就没。
> - **代码**：放在某个**文件夹**里。

---

## 8. 换一家 AI 怎么做

**核心真相：所有 AI API 套路一样，换家只改 3 处。** 学会 Claude 这一个，其余都是换汤不换药。

### 通用流程（和调 Claude 一模一样）

```
① 去那家官网注册、（可能）充值
② 在它后台生成 API key
③ pip 装它的工具包
④ 设环境变量(key)
⑤ 写几乎一样的代码，跑
```

### 换家只改这 3 处

| 要改的 | Claude | 换别家 |
|---|---|---|
| ① 工具包 | `pip install anthropic` | 换成那家的包 |
| ② 客户端写法 | `anthropic.Anthropic()` | 换成那家的写法 |
| ③ 模型名 | `claude-haiku-4-5` | 换成那家的模型名 |

> `messages` 那段"点单"几乎所有家都通用（`role`+`content`），因为大家都跟着 OpenAI 的格式走。

### 具体几家

**ChatGPT（OpenAI）** — 注册 `platform.openai.com`（国内注册/付费较麻烦，需外币卡）
```python
from openai import OpenAI            # pip install openai
client = OpenAI()                    # 读环境变量 OPENAI_API_KEY
resp = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role":"user","content":"你好"}]
)
print(resp.choices[0].message.content)
```

**Kimi（月之暗面）/ DeepSeek / 智谱 / 通义** — 国产，国内注册付费顺畅，常送免费额度。
**大多兼容 OpenAI 格式**：复用 `openai` 包，只改 `base_url` + 模型名：
```python
from openai import OpenAI
client = OpenAI(
    api_key="...",                          # 或用环境变量
    base_url="https://api.moonshot.cn/v1"   # ← 指向 Kimi（各家不同）
)
resp = client.chat.completions.create(
    model="moonshot-v1-8k",                 # ← 那家的模型名
    messages=[{"role":"user","content":"你好"}]
)
print(resp.choices[0].message.content)
```

> **规律：国产模型大多"假装自己是 OpenAI"（兼容 OpenAI 格式）**，所以学会 OpenAI 那套写法 = 一把钥匙开一堆门，换家只需改 `base_url` + 模型名。

---

## 9. 怎么找包名 / base_url / 模型名

接任何一家前，要查三样信息：**包名、base_url（如果需要）、模型名**。去哪查：

**万能规律（先猜）**：包名常常就是公司英文名。

| 公司/产品 | 包名 |
|---|---|
| Anthropic（Claude）| `anthropic` |
| OpenAI（ChatGPT）| `openai` |
| 智谱 GLM | `zhipuai` |
| 阿里通义 | `dashscope` |
| Kimi / DeepSeek | 不用单独装，**复用 `openai`** |

**权威做法（要查就查官方文档的 Quickstart 页）**：
- 搜索引擎搜 `公司名 API quickstart` 或 `公司名 API 文档 python`
- 或从你注册的控制台后台找 `Docs / 文档 / API Reference` 入口
- 那一页通常同时给你三件套：**装包命令 + 示例代码 + 模型名列表**

> 例：Anthropic 文档在 `docs.anthropic.com`，装包命令、示例、所有模型名都写得清清楚楚。

**⚠️ 认准官方包名**：偶有蹭名字的假包，以官方文档写的包名为准，装完确认 `Successfully installed`。

---

## 10. 飞书等"工具平台"和"AI 大脑"的区别

容易混的概念，分清楚：

- **飞书 / Slack / 微信** = **工具平台**，它们的 API 是用来"操作软件功能"的（发消息、读表格、建文档），**不是和 AI 对话**。
- **Claude / Kimi / GPT** = **AI 大脑**，它们的 API 才是"和 AI 对话"。

所谓"**在飞书里用 AI**"= 把"工具"和"大脑"接起来：飞书机器人负责收发消息，背后调用 AI 模型的 API 生成回答。这属于"做一个应用"，比较进阶，等学 agent 时会自然接触。

> 现阶段记住：**平台负责收发，大脑负责思考，组合起来才是"在某平台里用 AI"。**

---

## 11. 踩过的坑

| 坑 | 现象 | 自救 |
|---|---|---|
| 文件夹名带空格 | `mkdir`/`cd` 报 `PositionalParameterNotFound` | 路径加引号：`cd "claude API练习"`（或起名别带空格）|
| key 写死在代码里 | 有泄露风险 | 用环境变量 `$env:...` |
| 把 key 代码放进 git 项目 | push 后 key 公开 | 代码放独立非 git 文件夹 |
| 关了窗口 key 没了 | 报缺少 key 的错 | 同一窗口重设 `$env:ANTHROPIC_API_KEY` |
| `pip` 不认识 | 报错 | 改用 `python -m pip install 包名` |

---

## 12. 小抄总结

| 要点 | 内容 |
|---|---|
| API 是啥 | 帮你跟服务（AI）打交道的"服务员" |
| 流程 | 点单(请求 JSON) → AI 想 → 上菜(响应 JSON) |
| 计费 | 按 token，`max_tokens` 封顶省钱 |
| 代码四步 | 装包 → 建客户端 → 点单(messages) → 取回答 |
| 安全 | key 用环境变量、别进 git、泄露就作废重建 |
| 换家 | 只改 3 处：包名、客户端写法、模型名 |
| 国产模型 | 大多兼容 OpenAI，改 `base_url` + 模型名即可 |
| 查信息 | 去官方文档 Quickstart 页找包名/base_url/模型名 |
| 平台≠大脑 | 飞书等是工具平台，Claude 等是 AI 大脑 |

**一句话总结：API = 跟 AI 对话的服务员；点单和上菜都用 JSON；按 token 计费；代码就四步；换家只改三处。**

---

## 🎓 Stage 0 毕业

学到这里，你完成了第一次真实的 AI 调用——CLI、git、JSON、API key 安全、API 全部在这一行 `python hello.py` 的回复里汇合。**Stage 0 正式通关，下一站 Stage 1：LLM 基础。**
