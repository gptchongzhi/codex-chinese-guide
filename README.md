# Codex 中文教程：安装、登录、实战示例与额度排查（2026）

第一次使用 Codex，可以从一个明确的小任务开始：让它读取一份 CSV，生成汇总脚本，再用你能人工算清的数据检查结果。本教程按“选入口 → 登录 → 完成任务 → 验收 → 排查问题”的顺序，带你走完这个过程。

适合刚接触 AI 编程、需要处理数据或维护小项目的读者。命令主要以 Codex CLI 为例；使用 VS Code 的读者可以在编辑器中完成相同练习。

维护者：Ai66.org 教程团队。本文为独立中文教程，与 OpenAI 无隶属关系，文末含维护者的服务与相关教程入口。

**资料核对日期：2026-09-10。** 安装与命令依据文内官方资料，套餐和可用功能以当前账号显示为准。

## 目录

- [1. 选择 Codex 使用入口](#choose-entry)
- [2. 安装与登录](#install-login)
- [3. 第一次实战：CSV 渠道汇总](#csv-practice)
- [4. 怎样让 Codex 少返工](#task-prompts)
- [5. 用 AGENTS.md 保存项目约定](#project-instructions)
- [6. Codex 额度、会员与 API 的区别](#usage-billing)
- [7. 常见问题排查](#troubleshooting)
- [8. 完成任务后的验收](#review-results)
- [9. 相关教程与维护说明](#related-guides)

<a id="choose-entry"></a>
## 1. 选择 Codex 使用入口

| 你习惯的工作方式 | 从哪里开始 |
| --- | --- |
| 在终端里操作项目、运行脚本 | Codex CLI，按下一节安装 |
| 在 VS Code 中查看和修改代码 | 从 [官方 IDE 扩展页面](https://learn.chatgpt.com/docs/codex/ide)进入对应安装入口 |
| 已有能正常使用的 Codex 桌面客户端 | 打开独立练习文件夹，直接使用第 3 节任务 |

CLI 可以在当前项目中读取文件、编辑代码和运行本机工具。因此，先准备一个专门的练习目录，更容易看清输入、输出和改动范围。[Codex CLI 官方说明](https://learn.chatgpt.com/docs/codex/cli)

使用 VS Code 时，安装扩展后打开项目与 Codex 侧栏；找不到侧栏，可在命令面板中执行 `Codex: Open Codex Sidebar`。下面的中文任务可以直接发到侧栏，终端安装命令则只在需要 CLI 时使用。[IDE 扩展使用说明](https://learn.chatgpt.com/docs/codex/ide)

<a id="install-login"></a>
## 2. 安装与登录

### 2.1 安装 Codex CLI

macOS / Linux 的官方独立安装命令如下。它会下载并运行 OpenAI 的安装脚本：

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

Windows 用户在 [官方 CLI 安装页面](https://learn.chatgpt.com/docs/codex/cli)切换到 Windows 选项，使用对应命令；不要将上面的 shell 命令直接粘贴到 PowerShell。

安装后重新打开终端，检查命令是否可用：

```bash
codex --version
```

能显示版本号，说明终端找到了 CLI。若提示找不到命令，先检查安装是否完成、终端是否重新打开，以及安装目录是否已加入 PATH。

### 2.2 选择登录方式

一般个人用户可以先使用 ChatGPT 账号登录：

```bash
codex login
```

按浏览器页面完成登录，再返回终端。检查当前认证方式：

```bash
codex login status
```

**使用 ChatGPT 登录与使用 API Key 是两条计费路径。** 前者使用对应账号的订阅权益；后者按 API 计费。使用 API Key 不会自动扣除 ChatGPT 套餐内的用量，部分依赖 ChatGPT 工作区或云端的功能也可能不同。[官方身份验证说明](https://learn.chatgpt.com/docs/auth)

如果准备使用已有会员，先确认客户端与购买会员时是同一账号。团队用户再核对工作区；登录成功本身不能证明已经获得工作区中的全部权限。

### 2.3 在练习目录启动

第 3 节练习需要 Python 3。启动 Codex 前，在普通终端执行 `python3 --version` 确认已安装；Windows 如果使用 `py -3`，用 `py -3 --version` 检查，并将后文任务中的 `python3` 相应替换。

创建一个名为 `codex-csv-demo` 的新文件夹。确认当前位置是准备放练习文件的位置后，在终端依次执行：

```bash
mkdir codex-csv-demo
cd codex-csv-demo
codex
```

如果这个目录已经存在，直接进入，先检查里面的文件。接下来发送的是自然语言任务，不是终端命令。

输入 `/permissions` 可以查看和调整权限。只分析项目时可选择只读；进行下面的练习时，需要允许在练习目录内写文件和运行 Python。任务文字里的“只修改这些文件”表达工作范围，实际权限仍由客户端的权限配置控制。[权限说明](https://learn.chatgpt.com/docs/agent-approvals-security)

<a id="csv-practice"></a>
## 3. 第一次实战：CSV 渠道汇总

这个练习的脚本只使用 Python 3 标准库，不需要安装第三方依赖。完成上节的 Python 检查后，准备输入文件，再把任务发到 Codex 会话中。

### 准备四条演示记录

用文本编辑器在练习目录创建 `sample_orders.csv`，复制以下内容。仓库也提供了可下载的 [CSV 示例文件](examples/sample_orders.csv)。所有记录均为虚构练习数据。

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

打开生成的 `summary.csv`，预期内容为：

```csv
channel,order_count,total_amount
github,1,5.50
zhihu,2,30.00
```

人工核对：只统计三单，总金额 **35.50**；`pending` 的 99.00 不进入结果。也可以与仓库中的 [预期结果文件](examples/expected_summary.csv)比较。

如果输出不符，继续发送实际差异，例如：

```text
生成结果把 github 统计为 2 单、104.50，预期是 1 单、5.50。
请检查是否把 pending 记录也计入，只修改状态筛选相关逻辑，
添加能复现这个错误的测试，再重新运行验证。
```

这是一个错误情形示例，只有实际出现该差异时才使用。练习的重点是学会给出明确输入、限定处理规则，并用可检查的结果确认完成。

<a id="task-prompts"></a>
## 4. 怎样让 Codex 少返工

写任务时，提供四样信息：**要完成什么、看哪些文件、保留哪些行为、怎样算完成。** 下面是两个可按实际项目填写的模板。

### 读懂陌生仓库

```text
请帮我理解 [仓库路径] 中的 [目标功能]，这次只读，不修改文件或安装依赖。
先读取 README、依赖声明和 [相关目录]，定位入口及主要函数。
给出一次典型操作的调用顺序、现有启动方法与测试命令，并附文件路径和行号。
区分源码证明的事实与推断；没有实际运行的命令，不要写成运行成功。
```

适合刚接手项目时使用。把方括号换成真实路径和功能；先选一个功能，避免第一次就要求解释所有文件。

### 修复一个可复现的错误

```text
请检查 [脚本路径] 的状态筛选逻辑。
实际问题：输入同时包含 paid、unpaid、pending，unpaid 也进入了汇总。
预期：只有严格等于 paid 的记录被统计。
只修改该脚本和相关测试，保持命令参数、输出列及原有排序规则不变。
先确认能复现，再修改；加入包含这三种状态的回归用例。
报告修复前后结果及原有相关测试结果。不能复现时说明证据，不强行修改。
```

这个模板展示了如何描述一个假设的筛选错误。真实项目中，把现象、输入和预期换成你实际观察到的情况。

同一问题需要继续修正时，尽量补充新证据：具体报错、失败输入、实际输出。仅反复说“还是不对”，会让定位问题缺少依据。

<a id="project-instructions"></a>
## 5. 用 AGENTS.md 保存项目约定

当一个项目会反复使用 Codex，可以将稳定的约定写进项目根目录的 `AGENTS.md`。Codex 启动时会读取适用的指令文件；已有文件时先阅读并合并需要的内容。[官方 AGENTS.md 指南](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

例如，前面的 Python 练习可以使用：

```markdown
# 项目约定

- 这是一个 Python 3 CSV 汇总练习，使用标准库实现。
- sample_orders.csv 是演示输入，保持原样。
- 金额从字符串转换为 Decimal，输出保留两位小数。
- 修改筛选或汇总规则后，运行 python3 -m unittest。
- 完成时说明改动文件、测试结果和仍未解决的问题。
```

将这个文件放在对应项目中，再启动新会话。它适合保存项目规则；当前任务的临时要求仍写在任务里。例如，“只解释代码”不必变成以后所有任务都只读的永久约定。

<a id="usage-billing"></a>
## 6. Codex 额度、会员与 API 的区别

### Codex 必须先买 Plus 才能用吗？

截至核对日期，官方列出的 Codex 套餐包括 Free、Go、Plus、Pro 及组织套餐，额度和功能不同。先检查已有账号的可用功能，再决定是否需要更高用量。[官方套餐说明](https://learn.chatgpt.com/docs/pricing)

### 怎样查看 Codex 剩余额度？

在账号用量面板查看；Codex CLI 的活动会话中可输入 `/status`。注意区分上下文占用与账号用量限制，它们不是同一个指标。

任务大小、模型与工具使用会影响消耗，不能把套餐估算次数当成固定保证。官方还说明本地与云端使用共享套餐用量，可能存在每周限制；重置时间应看当前账号显示。[官方用量说明](https://learn.chatgpt.com/docs/pricing)

### 搜索“Codex 充值”时，先确认哪件事？

先确定你要增加的是 ChatGPT 套餐权益、账号可购买的额外用量，还是 API 用量。然后核对当前登录方式与计费对象。选择 API Key 就应查看 API 平台计费，不应仅凭 ChatGPT 的会员付款记录判断 API 是否可用。[登录与计费路径](https://learn.chatgpt.com/docs/auth)

<a id="troubleshooting"></a>
## 7. 常见问题排查

先保存**报错原文、发生时间、客户端入口**，再按现象检查。表中的方向用于缩小范围，不代表已经查明原因。

| 现象 | 优先检查 | 有帮助的证据 |
| --- | --- | --- |
| `codex` 命令不存在 | 安装是否完成，当前终端是否能找到安装目录 | 安装结束提示、`codex --version` 的输出 |
| 网页已登录，CLI 仍要求登录 | 浏览器流程是否返回客户端，CLI 当前认证方式 | `codex login status` 的结果 |
| 已有会员，却提示额度不足 | 当前账号、工作区、登录方式和用量面板 | 限额名称与实际显示的重置时间 |
| 文件能读取，却不能修改 | 当前目录与写入权限是否符合任务 | `/status` 中的工作目录、`/permissions` 中的权限 |
| 能运行 Codex，但 Python 命令失败 | 本机是否安装 Python，任务使用的命令是否正确 | Python 版本命令与错误原文 |
| 连接超时或客户端一直加载 | 故障影响范围、客户端版本，以及能否访问官方服务 | 哪些入口正常、哪些失败，发生时间是否一致 |
| 代码改完，但结果不符合要求 | 输入、筛选规则、输出与验收条件是否一致 | 最小复现输入、实际结果与预期差异 |

认证与权限相关入口见 [身份验证文档](https://learn.chatgpt.com/docs/auth)和 [权限文档](https://learn.chatgpt.com/docs/agent-approvals-security)。只提供排查所需信息，截图遮住隐私；不要公开 API Key、密码或本地认证文件。

连接失败、权限不足与用量耗尽需要分别定位。看到“不能用”时，先找到具体提示，再决定是处理环境、调整权限，还是等待额度恢复。

<a id="review-results"></a>
## 8. 完成任务后的验收

“Codex 说完成了”之后，再检查三件事：

1. **改动范围：** 实际修改了哪些文件，原始输入与无关功能是否保留。
2. **行为结果：** 用已知输入运行，输出是否符合约定；CSV 练习就核对三单、35.50 和过滤条件。
3. **验证证据：** 测试是否真正运行，失败项与未完成项是否写清楚。

在 CLI 中，`/diff` 用于查看当前改动，`/review` 可请求代码审查，`/model` 用于选择当前可用模型与推理强度。这些命令在 Codex 会话里输入，不是在普通 shell 里输入；审查也不能代替实际运行验证。[官方命令参考](https://learn.chatgpt.com/docs/developer-commands?surface=cli)

对于实际项目，建议在开始前保存可恢复的版本，完成后再决定是否提交和发布。第一次练习做到“文件可运行、结果可核对、差异可解释”，就已经建立了以后处理更复杂任务的基本方法。

<a id="related-guides"></a>
## 9. 相关教程与维护说明

本仓库由 Ai66.org 教程团队维护，主要整理 Codex 的使用方法、练习和问题排查。

- 登录或权益仍有疑问，可继续阅读 [Ai66 Codex 权益核对教程](https://ai66.org/codex-login-check?utm_source=github&utm_medium=referral&utm_campaign=seo_growth_202609&utm_content=codex_guide_troubleshooting)。
- 如已确认需要了解会员服务，可查看 [Ai66 会员商品与开通说明](https://ai66.org/chatgpt-plus-chongzhi?utm_source=github&utm_medium=referral&utm_campaign=seo_growth_202609&utm_content=codex_guide_membership)。具体适用条件、价格与交付以商品页为准。
- Plus 开通与续费相关内容见维护者的 [ChatGPT 充值教程仓库](https://github.com/gptchongzhi/chatgpt-plus-daichong-guide)。

发现教程错误，欢迎通过 Issue 提供文档位置、客户端版本和脱敏后的复现步骤。示例数据只用于学习，不代表真实订单、商品价格或客户使用结果。

### 更新记录

- 2026-09-10：整理安装与登录方法，加入 CSV 汇总练习、任务模板、项目约定示例、额度与常见问题排查。
