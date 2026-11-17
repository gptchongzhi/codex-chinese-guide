# Codex 中文教程：安装、登录、实战示例、额度档位与防封号规范（2026 年 11 月更新）

> 第一次使用 OpenAI **Codex**（CLI / IDE 扩展），或遇到额度不足、需要手机号验证、不知道选 **Plus / Pro100 (10x) / Pro200 (20x) / Pro500 (50x)** 哪一档？本文保留了完整的 **CSV 零基础实操案例**，并更新了 **2026 年 11 月最新会员档位、排查清单与 6 条实测防封号红线**。
>
> 🕐 **最后核对日期：2026-11-18** · 建议 **Star ⭐ 收藏** 备查 · 维护者：[Ai66.org 教程团队](https://ai66.org/?utm_source=github&utm_medium=codex_chinese_guide)（独立中文教程，与 OpenAI 无隶属关系）

## 目录

- [1. 选择 Codex 使用入口与 2026-11 会员档位](#choose-entry)
- [2. 安装与登录（ChatGPT 订阅 vs API Key）](#install-login)
- [3. 第一次实战：CSV 渠道汇总（含示例文件）](#csv-practice)
- [4. 怎样让 Codex 少返工](#task-prompts)
- [5. 用 AGENTS.md 保存项目约定](#project-instructions)
- [6. Codex 额度、会员档位与 API 的区别](#usage-billing)
- [7. 常见问题排查清单](#troubleshooting)
- [8. Codex / ChatGPT 防封号 6 条安全红线（实测避坑）](#anti-ban)
- [9. 完成任务后的验收](#review-results)
- [10. 相关教程与维护说明](#related-guides)

<a id="choose-entry"></a>
## 1. 选择 Codex 使用入口与 2026-11 会员档位

| 你习惯的工作方式 | 从哪里开始 |
| --- | --- |
| 在终端里操作项目、运行脚本 | Codex CLI，按第 2 节安装 |
| 在 VS Code 中查看和修改代码 | 从 [官方 IDE 扩展页面](https://learn.chatgpt.com/docs/codex/ide) 进入对应安装入口 |
| 已有能正常使用的 Codex 桌面客户端 | 打开独立练习文件夹，直接使用第 3 节任务 |

**Codex 不需要单独购买独立会员**，只要你的 ChatGPT 账号开通了对应订阅即可直接使用：

| 套餐档位 | 参考价格（支付宝/微信） | Codex 额度倍率 | 适用开发场景 |
| --- | --- | --- | --- |
| **ChatGPT / Codex Plus** | ¥178 起/月 | 标准个人额度 | 日常写脚本、改 Bug、轻中度代码辅助 |
| **ChatGPT / Codex Pro100** | ¥880 起/月 | **10 倍 Pro 额度** | 全职主力开发：日常多文件修改与单测 |
| **ChatGPT / Codex Pro200** | ¥1680 起/月 | **20 倍 Pro 额度** | 重度 Agent 并发、长时间自动化编码 |
| **ChatGPT / Codex Pro500** | ¥4080 起/月 | **50 倍 Pro 额度** | 极限高频开发、大规模重构与科研长任务 |

👉 套餐详情与自助充值入口：**[ChatGPT / Codex 会员套餐与价格](https://ai66.org/chatgpt?utm_source=github&utm_medium=codex_chinese_guide)** · **[ChatGPT Plus 充值与购买](https://ai66.org/chatgpt-plus-chongzhi?utm_source=github&utm_medium=codex_chinese_guide)**

<a id="install-login"></a>
## 2. 安装与登录

### 2.1 安装 Codex CLI

macOS / Linux 的官方独立安装命令如下：

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

Windows 用户在 [官方 CLI 安装页面](https://learn.chatgpt.com/docs/codex/cli) 切换到 Windows 选项，使用对应命令；不要将上面的 shell 命令直接粘贴到 PowerShell。

安装后重新打开终端，检查命令是否可用：

```bash
codex --version
```

### 2.2 选择登录方式（务必区分 OAuth 与 API Key）

一般个人用户使用 ChatGPT 账号登录，直接消耗会员内置 Codex 额度：

```bash
codex login
```

按浏览器页面完成登录，再返回终端检查当前认证方式：

```bash
codex login status
```

> **重要提醒：** 使用 `codex login`（ChatGPT 订阅）与设置 `OPENAI_API_KEY` 是两条完全不同的计费路径。如果本地环境变量里配置了 `OPENAI_API_KEY`，CLI 可能会按 API 余额单独扣费，而不是使用你的 Plus/Pro 套餐额度。

### 2.3 在练习目录启动

第 3 节练习需要 Python 3。先执行 `python3 --version` 确认环境，然后创建独立练习目录：

```bash
mkdir codex-csv-demo
cd codex-csv-demo
codex
```

输入 `/permissions` 可以查看和调整当前目录读写权限。只分析项目时可选择只读；进行下面练习时，允许在练习目录内写文件和运行 Python。

<a id="csv-practice"></a>
## 3. 第一次实战：CSV 渠道汇总

这个练习只使用 Python 3 标准库，无需第三方依赖。

### 准备四条演示记录

在练习目录创建 `sample_orders.csv`（或直接下载仓库内置的 [CSV 示例文件](examples/sample_orders.csv)）：

```csv
order_id,channel,status,amount
DEMO-001,zhihu,paid,19.90
DEMO-002,github,paid,5.50
DEMO-003,zhihu,paid,10.10
DEMO-004,github,pending,99.00
```

### 把这段任务发给 Codex

```text
请实现一个适合初学者阅读的 Python 3 CSV 汇总工具。

输入：当前文件夹中的 sample_orders.csv。
允许新建 summarize_orders.py 和 test_summarize_orders.py，
运行时生成 summary.csv；测试可以在临时目录构造演示数据。
保留原始 CSV，不读取练习以外的真实业务数据，不安装第三方依赖。

需求：
1. 使用 csv 读取文件，只统计 status 严格等于 paid 的记录。
2. 按 channel 分组，统计订单数和金额总和，每条符合条件的记录算一单。
3. 金额必须是有限十进制数，从原始字符串构造 Decimal，不先转 float。
4. 输出列为 channel,order_count,total_amount；渠道按字母升序，金额保留两位小数。
5. 没有 paid 记录时仅输出表头。缺列或 paid 行金额无效时，
   给出能定位问题的提示，以非零状态退出，不输出部分汇总结果。
6. 提供并实际运行以下命令，生成 summary.csv 并报告内容；无法运行时说明原因：
   python3 summarize_orders.py sample_orders.csv --output summary.csv
7. 用 unittest 验证正常汇总、过滤非 paid 行、没有 paid 行、缺列和无效金额。
   运行 python3 -m unittest；不能运行时如实说明。

完成后告诉我新增了哪些文件、如何运行、测试结果和未解决的问题。
```

### 用具体结果验收

打开生成的 `summary.csv`，预期内容与仓库内置的 [预期结果文件](examples/expected_summary.csv) 一致：

```csv
channel,order_count,total_amount
github,1,5.50
zhihu,2,30.00
```

<a id="task-prompts"></a>
## 4. 怎样让 Codex 少返工

写任务时，固定提供四样信息：**要完成什么、看哪些文件、保留哪些行为、怎样算完成。**

```text
请帮我理解 [仓库路径] 中的 [目标功能]，这次只读，不修改文件或安装依赖。
先读取 README、依赖声明和 [相关目录]，定位入口及主要函数。
给出一次典型操作的调用顺序、现有启动方法与测试命令，并附文件路径和行号。
区分源码证明的事实与推断；没有实际运行的命令，不要写成运行成功。
```

<a id="project-instructions"></a>
## 5. 用 AGENTS.md 保存项目约定

将项目级约定写进根目录的 `AGENTS.md`，Codex 每次启动会自动读取：

```markdown
# 项目约定

- 这是一个 Python 3 CSV 汇总练习，使用标准库实现。
- sample_orders.csv 是演示输入，保持原样。
- 金额从字符串转换为 Decimal，输出保留两位小数。
- 修改筛选或汇总规则后，运行 python3 -m unittest。
- 完成时说明改动文件、测试结果和仍未解决的问题。
```

<a id="usage-billing"></a>
## 6. Codex 额度、会员与 API 的区别

- **怎样查看剩余额度？** 在 Codex CLI 活动会话中输入 `/status`，或在 ChatGPT 网页端用量面板查看（注意区分上下文占用与周/5小时用量重置时间）。
- **手机号验证要求：** 免费版与部分 Plus 账号首次接入 Codex 可能需要手机号验证；Pro100 / Pro200 / Pro500 档位通常免去此限制。详见 **[Codex 手机号验证说明](https://ai66.org/codex-shouji-yanzheng?utm_source=github&utm_medium=codex_chinese_guide)**。
- **开完会员仍无额度？** 按 **[Codex 登录与权益排查清单](https://ai66.org/codex-login-check?utm_source=github&utm_medium=codex_chinese_guide)** 逐项检查是否登录成另一个小号、或被 `OPENAI_API_KEY` 覆盖。

<a id="troubleshooting"></a>
## 7. 常见问题排查清单

| 现象 | 优先检查 | 有帮助的证据 |
| --- | --- | --- |
| `codex` 命令不存在 | 安装是否完成，PATH 是否生效 | `codex --version` 输出 |
| 网页已登录，CLI 仍要求登录 | 浏览器授权是否回到原终端，CLI 当前认证方式 | `codex login status` 结果 |
| 已有会员，却提示额度不足 | 当前账号邮箱、工作区、是否误用 `OPENAI_API_KEY` | 限额提示原文与 `/status` 输出 |
| 文件能读取，却不能修改 | 当前目录与 `/permissions` 写入权限设置 | `/status` 工作目录与权限状态 |
| 连接超时或客户端一直加载 | 网络代理节点与客户端版本 | 哪些入口正常、报错时间 |

<a id="anti-ban"></a>
## 8. Codex / ChatGPT 防封号 6 条安全红线（实测避坑）

最近有不少用户担心高频跑 Codex 会不会触发 OpenAI 封号。我们课题组几十号人高频使用 Codex 至今未遇到过封号，核心在于严格避开以下 6 个雷区：

1. **拒绝技术漏洞充值与低价 Team 黑号**：部分渠道利用 Bug 或黑卡拉低价 Team/代充，一旦账单审计必封；选择正规卡密充到自己账号最稳。
2. **严禁任何形式的接口反代（如 Sub2API 等）**：把网页或客户端订阅额度反代成 API 并发调用，极易被系统识别并直接封号。
3. **不要多人共用一个账号**：多 IP、多设备同时高频并发请求极易触发共享风控。
4. **不要生成违法违规内容**：避免高频发送涉政、色情、暴力、侵权等明确违禁提示词。
5. **Pro 用户不要转卖官方赠送的免费 API 额度**：防止买家拿去反代或做数据蒸馏，导致主账号被连坐封号。
6. **尽可能固定一个支持地区的干净 IP 节点**：不要频繁跨国切换地区节点。

<a id="review-results"></a>
## 9. 完成任务后的验收

在 Codex CLI 会话中可使用以下内置斜杠命令辅助验收：
- `/diff`：查看当前未提交改动
- `/review`：请求代码审查
- `/model`：切换当前可用模型与推理强度

最后人工核对三件事：改动范围是否越界、已知输入输出是否吻合（CSV 练习核对 3 单 / 35.50）、测试是否真正跑通。

<a id="related-guides"></a>
## 10. 相关教程与维护说明

- 🏠 **Ai66 官网（ChatGPT / Codex / Claude / Gemini / Grok 充值）**：https://ai66.org/?utm_source=github&utm_medium=codex_chinese_guide
- 🔧 **Codex 登录与权益排查**：https://ai66.org/codex-login-check?utm_source=github&utm_medium=codex_chinese_guide
- 📱 **Codex 手机号验证说明**：https://ai66.org/codex-shouji-yanzheng?utm_source=github&utm_medium=codex_chinese_guide
- 🛡️ **GPT 代充安全须知与发票说明**：https://ai66.org/gpt-daichong?utm_source=github&utm_medium=codex_chinese_guide
- 📊 **2026 四大 AI 会员价格总表**：https://ai66.org/ai-huiyuan-jiage?utm_source=github&utm_medium=codex_chinese_guide
- 📖 **姊妹仓（ChatGPT Plus / Pro 充值与防封号指南）**：https://github.com/gptchongzhi/chatgpt-plus-daichong-guide

### 更新记录

- **2026-11-18**：升级为 2026 年 11 月最新版，新增 Plus / Pro100 (10x) / Pro200 (20x) / Pro500 (50x) 档位对照、OAuth vs `OPENAI_API_KEY` 排查与 6 条实测防封号红线，保留完整 CSV 实战与 `AGENTS.md` 教程。
- **2026-09-10**：整理安装与登录方法，加入 CSV 汇总练习、任务模板、项目约定示例、额度与常见问题排查。
