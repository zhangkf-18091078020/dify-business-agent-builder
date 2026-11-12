---
name: dify-business-agent-builder
description: >-
  Builds Yunlan/Dify Agents for business apps (e.g. 对象采集) and public
  capabilities (e.g. 监控). Discovery: Dify module → env UI → if both fail,
  read workspace dify-agent/ capability pack → else template fallback.
  Confirm with human, then Workflow→Tool + Skill + new Agents (01～04).
  Capability packs are temporary under dify-agent/; final assets live in Dify.
  Public builds need Agents visibility + 公共能力目录. Never old studio AGENT;
  never empty-shell tools.
---

# Dify 业务 / 公共 Agent 搭建执行器

**适用谁**：业务同事（对象采集、应用采集…）与**平台同事**（监控、告警、调度、资产…）**同一 Skill**。  
**共同交付**：新版 **Agents**（顶栏 Agents 可见）。公共类还要登记 `catalog/公共能力目录.md`，方便业务同事直达或绑定。

**规则版本**：`20261112b`（改 L1 文件时请更新日期；执行报告须带上此版本 + 本包覆盖项）。

## 执行身份（L0；先认自己是谁）

读本 Skill 的 Cursor / 执行方 AI = **Builder（搭建造物者）**，不是业务执行者，也不是已上线的业务 Agent。

| 身份 | 谁 | 该做什么 | 不该做什么 |
|------|-----|----------|------------|
| **Builder（你）** | Cursor 按本 Skill 执行 | 在 Dify 造 **Workflow→Tool + Skill + 新版 `/agents/` Agent**；临时落盘能力包；人确认矩阵 | 去业务环境（如 222）替用户创建/启动真实采集、迁移等任务；只建 Workflow 就宣布「Agent 已搭好」 |
| **业务 Agent（产物）** | 造完后挂在 Dify `/agents/` 的对话入口 | 用户说「搭建/创建…任务」时调 `create_*` 等业务 Tool | （由 `04` §2.0 约束运行时行为；**不是**你现在要亲自执行的业务动作） |

**语义拆开（强制）**：

- 用户说「搭业务 Agent / 按 Skill 搭建」→ **只做 Builder 造物**；验收以 **Agent URL 含 `/agents/` 且已挂 Skill** 为准。  
- 用户话里的「能创建业务流程 / 搭建采集任务」→ 指产物 Agent **将来**通过矩阵里的 `create_*` 等工具完成；本次 = **实现并挂载这些工具**，**禁止**理解为「现在就去业务系统点创建」。  
- `rules/03`、`04` §2.0 / §2.1 中「立刻调业务 Tool」→ 写入**业务 Skill / Agent 短提示词**时遵守；**Builder 本人不要按业务意图去调业务接口**（除非用户明确授权「自测 create_* / 调试工具」）。  
- Workflow 只是中间件；**没有**新版 Agent + Skill 挂载 = **未完成交付**。

撞到身份混淆 → 停问或自我纠正后继续造物，禁止用业务环境真实任务冒充「搭好了 Agent」。

## 包内目录（先认路）

| 路径 | 给谁 | 内容 |
|------|------|------|
| **本文件** `SKILL.md` | AI | 总流程入口（必须在仓库根） |
| `rules/` | AI | L0/L1：`生成契约`、`01`～`04` |
| `templates/` | AI | 业务/公共能力包**空模板** |
| `dify-agent/` | AI+人 | **临时/回退**能力包（非 Dify 最终产物；白名单见 `04` §6） |
| `catalog/` | AI+人 | 公共能力已上线登记 |
| `examples/` | 参考 | 样板能力包（非运行时必须） |
| `docs/` | 人 | 安装与使用说明 |
| `cursor/` | 人 | 可选复制到业务仓的 Cursor rule |

## 规则分层与覆盖（内置，用户不必手输）

执行前自动按下列优先级合并约定，**不要让用户再粘贴这句口令**：

| 层 | 内容 | 谁改 |
|----|------|------|
| **L0 硬底线** | 不可覆盖，见 `rules/生成契约`「L0」 | 仅改契约时全员生效 |
| **L1 统一规范** | 本文件 + `rules/` + `catalog/公共能力目录` | 改这些文件并推仓库 |
| **L2 包类型** | 业务 vs 公共分支（本文件主流程） | 改本文件 |
| **L3 单包特例** | 当前目标「业务/公共能力包」里的 **「规则覆盖」** 表 | 只改那一个能力包 |

**同名约定冲突时：L3 > L2 > L1；碰 L0 一律拒绝覆盖并停问。**

- 未在 L3 写出的项 → 全部继承 L1，禁止把 01～04 整份抄进能力包。  
- 动手前重新 Read：本文件、`rules/生成契约`、相关 `rules/01`～`04`、**当前目标能力包**（含规则覆盖表）。

## 全员如何吃到某人的修改（多项目；拉下来就能用）

同事打开的是**不同业务仓**。Cursor 会自动读**当前项目**下：

`.cursor/skills/dify-business-agent-builder/`

| 放哪 | 是什么 |
|------|--------|
| **Skill 共享仓**（权威） | 本仓库全套（`rules/`、`templates/`、`catalog/`…）= L1 |
| **本仓 / 各业务仓 / 公共仓** | 子模块挂共享 Skill（业务仓场景）；**工作区项目根** `dify-agent/` **仅** `README.md` + `〈名〉-业务能力包.md`（或公共包）——可作临时落盘与回退源；禁止再落 Skill/短提示词/执行报告/build（见 `rules/04` §6） |
| **可选用户级** | `~/.cursor/skills/...`（所有项目都能用，但要单独 pull） |

**推荐（pull 即生效）：**

1. 各业务仓一次性：`git submodule add 〈共享仓URL〉 .cursor/skills/dify-business-agent-builder`（详见 `docs/INSTALL.md`）。  
2. 同事日常：`git pull` + `git submodule update --init --recursive` → **新开对话**。  
3. 统一规则有人改：推 **Skill 共享仓**；各仓 `git submodule update --remote`（或约定脚本）→ 新开对话。  
4. 可选复制 `cursor/project-rule-dify-agent-builder.mdc` → 业务仓 `.cursor/rules/`，便于搭 Agent 时自动想起本 Skill。  
5. 公共「已上线」以共用 Dify 标签为准；目录文件跟共享仓 `catalog/` 走。

备选：不配子模块时，clone 共享仓到用户级 `~/.cursor/skills/...`，与打开哪个业务仓无关。

## 测出问题改哪个文件（包内）

| 不对劲 | 改这个文件 |
|--------|------------|
| 工作流 / 空参自测 / 空壳 | `rules/01-Workflow规范.md` |
| 发布为工具 | `rules/02-Tool规范.md` |
| Skill 正文 / 路由 / 长思考不调工具 | `rules/03-Skill规范.md` |
| Agents 形态 / 旧版 / 运行时把「搭建业务」理解成自检配置 | `rules/04-Agent规范.md` §2.0 |
| Builder 把「搭 Agent」搞成去业务环境搭流程 / 只交 Workflow | **本文件「执行身份」** + `rules/生成契约`「执行身份」与 L0 |
| 总流程、业务 vs 公共分支 | **本文件** |
| 验收 / 禁止项 | `rules/生成契约.md` |
| 某个**业务**做什么 | `dify-agent/〈业务〉-业务能力包.md`（L3；样板见 `examples/`） |
| 某个**公共**能力做什么 | `dify-agent/〈名〉-公共能力包.md` / `templates/公共能力包模板.md` |
| 公共已上线登记、Agent URL | `catalog/公共能力目录.md` |
| 安装 / 给人说明 | `docs/` |
| 换环境 / 换模型（在 Dify） | `docs/运行画像.md`；Workflow 环境变量见 `rules/01` §4；模型见 `rules/04` §4 |
| 写操作硬契约（保名/状态短路） | `rules/03` §3 |

## 必读顺序

1. 本文件  
2. `rules/生成契约.md`  
3. `rules/01`～`04`  
4. `catalog/公共能力目录.md`  
5. 本对象的能力包：优先工作区 `dify-agent/`；可选对照 `examples/`  
6. 可选：`examples/对象采集-业务能力包.md`（样板，非运行时依赖）  
7. 换环境/换模型时：`docs/运行画像.md`（操作在 **Dify**，不是改本仓文件热切）

## 默认

| 项 | 默认 |
|----|------|
| 环境 | 222（用户可改；监控等可改为监控环境） |
| 运行切换 | **在已搭好的 Dify** 上改：模型 → Agent 配置页；环境 → Workflow「环境变量」后发布（见 `docs/运行画像.md`） |
| Dify | `http://192.168.127.28`（用户可改） |
| Agent | **新版 Agents**（见 `rules/04`；公共/业务都必须） |

## 先判定包类型

| 信号 | 类型 | 落盘（临时；非最终产物） |
|------|------|------|
| 对象采集 / 应用采集 / 对象迁移 / 某业务菜单 | **业务** | **当前工作区项目根** `dify-agent/〈业务〉-业务能力包.md`（模板：`templates/业务能力包模板.md`） |
| 监控 / 告警 / 调度 / 资产平台；用户说「公共」「给别的业务用」 | **公共** | **当前工作区项目根** `dify-agent/〈名〉-公共能力包.md`（模板：`templates/公共能力包模板.md`） |

> 最终产物在 Dify（`01`～`04`）。能力包只服务确认与造物；可删可改。远程/Clawbot 场景工作区往往就是本 Skill 仓，故本仓亦有 `dify-agent/`。

不确定 → **停问用户**：这是业务入口还是公共平台能力？

## 主流程（两类共用；无包时「Dify → 页面 → dify-agent 回退」）

```text
用户：要搭建「某某」Agent（如「要搭建对象采集agent」）
  → 判定业务 or 公共；环境/Dify 默认用本文件「默认」表（用户可改）
  → 工作区已有对应能力包？（含本仓 dify-agent/）
        有 → 校验待确认 → 人确认后按矩阵 + rules/01～04 造
        无 → ① 共用 Dify 取对应模块（Chatflow 等）反推
              ② 否则上【该环境】页面探查汇总
              ③ ①② 都扒不到对应业务流程
                   → 【强制】读当前项目 dify-agent/〈名〉-业务/公共能力包.md
                   → 有包：校验 + 人确认后按 01～04 造
                   → 无包：templates 回退（须注明）或停问
              → ①或② 成功 → 临时落盘到 dify-agent/ → 人确认 → 01～04 造
  → 收尾：
        业务 → 按包勾选绑定「已上线」公共能力；Agent 打业务标签
        公共 → 必须新版 Agents + 标签「公共」「〈名〉」；回填 catalog/公共能力目录（工具/Skill/Agent URL=已上线）
```

### 公共能力（监控等）额外要求

1. **独立 Agent=是**（默认）：名称如「监控Agent」，URL 必须 `/agents/…`。  
2. **认定靠标签（给后续 AI 用）**——不是靠人口头说「这是公共」：  
   - 强制标签：`公共` + 能力 ID（如 `monitor`）；推荐再加 `monitor-入口` / `monitor-工具`。  
   - Skill 名：`monitor-ops`；工具名带能力前缀。细则见 `catalog/公共能力目录.md` §3。  
3. 搭完写入**仓库** `catalog/公共能力目录.md`（已上线 + URL）并推送。  
4. 业务同事 / **后来的 AI 搭业务 Agent** 两种用法：直达公共 Agent；或绑定公共 Tool/Skill。  
5. 禁止：无 `公共` 标签就当公共；禁止不建 Agents；禁止只改私人 zip。

### 业务 AI 自动「要用监控」时怎么认

不要问人「监控算不算公共」。按机器规则：

1. 在共用 Dify 查标签同时含 **`公共`** 与 **`monitor`**（或用户说的能力 ID）的 Tool/Skill/Agent；  
2. 对照仓库 `catalog/公共能力目录.md` 是否「已上线」；  
3. 命中 → 绑定，不重建；未命中 → 停问「公共监控未上线或未打标签」。

### 无能力包：发现顺序（强制）

| 优先级 | 来源 | 做法 |
|--------|------|------|
| **0** | 工作区已有包 | 读 `dify-agent/〈名〉-业务/公共能力包.md` → 校验待确认项 → 人确认后造。 |
| **1** | Dify 已有对应模块（如原 **Chatflow**） | 打开/导出该模块：节点、HTTP/工具调用、入参出参、分支 → 写成能力矩阵 + 接口表 → **临时落盘** `dify-agent/`。起草方式注明「Dify 模块反推」。**禁止**再浏览器探业务页抓接口。 |
| **2** | 无对应模块 | 确认菜单/环境 → 上环境汇总按钮/Tab/列表为候选（有列表则默认带列表）→ 临时落盘 `dify-agent/`。 |
| **3** | **Dify 与页面都扒不到**对应业务流程 | **【强制】**再读当前项目 `dify-agent/` 下已有技能包；有则按包确认后走 `01`～`04`；仍无 → 优先级 4。 |
| **4** | 仍无可用包 | 才用 `templates/` 回退，须注明原因；或停问用户补包/授权模板回退。 |

共通：

1. **禁止**无证据就按模板编「创建+启停+统计」。  
2. 人确认矩阵前禁止建新版 Agent 侧 Workflow / Tool / Skill / Agent（读已有 Chatflow / 读 `dify-agent/` 以落盘除外）。  
3. 从 Chatflow 落盘时：接口路径/字段以模块内配置为准；看不清 → 第 9 节待确认，禁止猜测。  
4. 能力包仅中间产物；最终以 Dify 上 Tool+Skill+Agents 为准。  
5. 允许「环境探查后人确认」；用户只说「要搭建某某 agent」时用本文件默认环境/Dify。

### 确认后逐项造物

有包且已确认 → 按 `rules/01`→`02`→`03`→`04`；禁止空壳；列表≠单状态。  
来源为环境探查时：找入口 → 抓同款接口再造；来源为 Chatflow / `dify-agent/` 回退时：按包内已有接口造，勿再浏览器补抓（除非包内明确待确认且人授权）。

### 收尾

自测；执行报告含 Agent URL（`/agents/`）；公共类必须已更新目录。失败列人工步骤。

## 业务搭建时「要用监控」——复用，不重建（强制）

公共目录**不能只指望同事本地 zip**。真相来源见 `catalog/公共能力目录.md` §0：

1. **先查共用 Dify**（标签 `公共` + 能力名；Agents / 已发布工具 / Skills）；  
2. **再读团队 Git 仓库里的** `catalog/公共能力目录.md`（不是只读本机 Skill 拷贝）；  
3. 本地拷贝与 Dify 冲突 → **以 Dify 为准**，并提示提交更新仓库目录。

```text
业务同事：搭「某某」业务 Agent，并且要用监控
  → Dify 实况 + 仓库公共能力目录：监控是否已上线？
        是 → 业务包勾选依赖「是」→ 只建业务资产 → Agent 绑定已有监控 Tool/Skill
           → 报告附公共监控 Agent URL（直达）
        否 → 停问（等监控同事上线并提交目录 / 本次不绑）
           → 禁止业务侧重做一套监控
```

| 角色 | 用本 Skill 做什么 | 别人怎么拿到 |
|------|-------------------|--------------|
| 监控同事 | 建公共监控 + 更新**仓库** `catalog/公共能力目录` 并推送 | 业务同事 `git pull` / 拉仓库；Dify Agents 立刻可见 |
| 业务同事 | 建业务；**绑定** Dify/仓库里已上线监控 | 不依赖监控同事发私人 zip |

验收：业务 Agent 已挂监控 Tool/Skill；未再发布同名监控工具。
