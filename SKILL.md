---
name: dify-business-agent-builder
description: >-
  Builds Yunlan/Dify Agents for both business apps (e.g. 对象采集) and public
  platform capabilities (e.g. 监控/告警/调度). Same flow: explore env UI, confirm
  capabilities, then Workflow→Tool + Skill + new Agents. Public builds must be
  visible under Agents for other teams and registered in 公共能力目录.
  Never old studio AGENT; never empty-shell tools. Pack rules: 01～04.
---

# Dify 业务 / 公共 Agent 搭建执行器

**适用谁**：业务同事（对象采集、应用采集…）与**平台同事**（监控、告警、调度、资产…）**同一 Skill**。  
**共同交付**：新版 **Agents**（顶栏 Agents 可见）。公共类还要登记 `catalog/公共能力目录.md`，方便业务同事直达或绑定。

**规则版本**：`20261109`（改 L1 文件时请更新日期；执行报告须带上此版本 + 本包覆盖项）。

## 包内目录（先认路）

| 路径 | 给谁 | 内容 |
|------|------|------|
| **本文件** `SKILL.md` | AI | 总流程入口（必须在仓库根） |
| `rules/` | AI | L0/L1：`生成契约`、`01`～`04` |
| `templates/` | AI | 业务/公共能力包**空模板** |
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
| **Skill 共享仓**（权威） | 本仓库全套（`rules/`、`templates/`、`catalog/`…） |
| **各业务仓** | 用 **git submodule** 挂到上面的路径；本仓另放 L3 业务能力包 |
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
| Skill 正文 / 路由 | `rules/03-Skill规范.md` |
| Agents 形态 / 旧版 | `rules/04-Agent规范.md` |
| 总流程、业务 vs 公共分支 | **本文件** |
| 验收 / 禁止项 | `rules/生成契约.md` |
| 某个**业务**做什么 | `〈业务〉-业务能力包.md`（业务仓 L3；样板见 `examples/`） |
| 某个**公共**能力做什么 | `〈名〉-公共能力包.md` / `templates/公共能力包模板.md` |
| 公共已上线登记、Agent URL | `catalog/公共能力目录.md` |
| 安装 / 给人说明 | `docs/` |

## 必读顺序

1. 本文件  
2. `rules/生成契约.md`  
3. `rules/01`～`04`  
4. `catalog/公共能力目录.md`  
5. 本对象的能力包（业务包或公共包）  
6. 可选：`examples/对象采集-业务能力包.md`（业务样板，非运行时依赖）

## 默认

| 项 | 默认 |
|----|------|
| 环境 | 222（用户可改；监控等可改为监控环境） |
| Dify | `http://192.168.127.28`（用户可改） |
| Agent | **新版 Agents**（见 `rules/04`；公共/业务都必须） |

## 先判定包类型

| 信号 | 类型 | 落盘 |
|------|------|------|
| 对象采集 / 应用采集 / 某业务菜单 | **业务** | `〈业务〉-业务能力包.md`（可参考 `templates/业务能力包模板.md`） |
| 监控 / 告警 / 调度 / 资产平台；用户说「公共」「给别的业务用」 | **公共** | `〈名〉-公共能力包.md`（模板：`templates/公共能力包模板.md`） |

不确定 → **停问用户**：这是业务入口还是公共平台能力？

## 主流程（两类共用「环境优先」）

```text
用户：搭「某某」Agent
  → 判定业务 or 公共
  → 已有对应能力包？
        有 → 校验待确认 → 按矩阵 + rules/01～04 造
        无 → 上【该环境】看页面有哪些能力 → 汇总表给人确认
              → 落盘业务包或公共包 → 再按 rules/01～04 造
           → 环境摸不到 → 才用对应 templates 回退（须注明）
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

### 无能力包：环境优先（强制）

1. 确认菜单/环境。  
2. 上环境汇总按钮/Tab/列表等为候选表（有列表则默认带列表候选）。  
3. **禁止**未看环境就按模板编「创建+启停+统计」。  
4. 人确认前禁止建 Workflow / Tool / Skill / Agent。  
5. 仅环境不可用时模板回退。

### 确认后逐项探查与造物

找入口 → 抓同款接口 → 按 `rules/01`→`02`→`03`→`04`；禁止空壳；列表≠单状态。

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
