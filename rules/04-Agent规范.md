# 04 Agent 规范（包内）

**改这里**：新版 Agents 如何创建、挂载、提示词形态。  
**样板形态**：`http://192.168.127.28/agents/01a1b0ef-aa6b-728c-9676-25d0bbc54df9`（对象采集；换 Dify 时只对照形态，不要求实例上已有它）。

## 1. 必须新版 Agents

| 正确 | 禁止（不合格） |
|------|----------------|
| 顶栏 **Agents**；URL `/agents/<id>/configure` | 工作室 Agent 应用；`/app/<id>/configuration` |
| 有 **Skill** 挂载区 | 标「旧版 AGENT」、无 Skill 区 |
| 短提示词 + 挂工具 + 挂 Skill | 把整本手册塞进提示词且无 Skill |

**API**：`POST /console/api/agent`（roster）。  
禁止：`POST /console/api/apps` 且 `mode=agent`（旧版）。  
挂 Skill：`PUT /console/api/workspaces/current/agents/<id>/skills`，`{"skill_ids":[...]}`。  
写配置：`build-draft/checkout` → `PUT build-draft`（`save_strategy=save_to_current_version`；body 须带 `variant`；`prompt` 为 `{system_prompt:…}` 对象）→ `publish`。

## 2. 提示词（短路由）

只保留：角色一句、能力索引（指向 Skill）、短路由、输出约定。  
细则在已挂载业务 Skill（及公共 Skill）。短路由宜 ≤8 条。  
**禁止**在短提示词里要求/示例传入 `token`/`tokenID`；鉴权由工具内部处理（见 `02` §2.1）。

### 2.0 业务意图优先（硬约束）

> **受众**：本节约束**已上线业务 Agent**（产物）的运行时提示词 / Skill 路由。  
> **Builder（Cursor）**：造物时把下列条款写进产物即可；**不要**把自己当成业务 Agent 去业务环境执行「搭建任务」。身份见 `SKILL.md`「执行身份」与 `生成契约`「执行身份」。

用户话里已是**业务动作**（搭建/创建迁移或采集任务、列表、查状态、转换、迁移、删除等）时：

- **立即**按 Skill 路由调业务 Tool（缺参用能力包默认；只给了采集任务名 → 按名解析 `collect_flow_id` 后调 `create_*`）。  
- **禁止**把「搭建 xxx 流程/任务」理解成「检查/配置/验收本 Agent 或 Skill」。  
- **禁止**在短提示词或对话中写入/执行：verify configuration、investigative setup、确认工具是否挂载、先自检再动手等元指令。  
- **禁止**长篇分析不调工具；首轮应以工具调用为主，中文短结论收尾。

### 2.1 公共解析与业务工具串联（硬约束）

能力包勾选了资产解析（或同类公共解析，如 `resolve_asset` / `asset-resolve`）时，生成的短提示词**必须**含等价表述：

- 用户最终意图为**列表 / 创建（搭建）/ 启停 / 删除**等业务动作，且话里出现主机名/数据源名（或需解析的资产名）：先调公共解析工具，**同一轮内**将得到的 id（如 `mac_id`/`db_id`）传入业务工具；**禁止**只回复解析结果后结束。  
- 用户**未**提主机名/数据源名：不必先调 `resolve_asset`，直接用能力包默认 `mac_id`/`db_id` 调业务工具。  
- 仅当用户明确**只要解析**、无业务动作时，才可停在解析结果。  
- 解析成功但业务必参仍缺：追问缺参；必参已齐：**同一轮**调用业务工具（如 `create_*`），不得等待用户重复下达同一意图。  
- 「搭建任务」场景：解析与创建所需 id/名已齐（或可用默认）时，必须调到 `create_*`（或矩阵中的创建工具），不得停在「主机解析成功」或配置自检。

### 2.2 验收（生成后自测）

- 绑了公共解析的业务 Agent：一句同时含「资产名 + 搭建/列表」→ 工具轨迹须为「解析 → 业务 Tool」，不得止于解析。  
- 一句仅「解析主机/数据源」→ 允许只返回解析结果。  
- 一句「搭建…采集任务：〈名〉」且未提主机 → 轨迹须含 `create_*`（可先按名解析采集 id），不得只有分析无工具调用。

## 3. 挂载

- 能力矩阵内全部业务 Tool。  
- 业务 Skill；能力包勾选的公共 Tool/Skill。  
- 验收：URL 含 `/agents/`，配置页可见 Skill 列表。

## 4. 模型与运行参数（在 Dify 换，不在 Cursor 热切）

- 模型挂在 **每个 Agent 配置页**（`agent_soul.model`）；换模型 = 打开该 Agent → 改模型 → **发布**。  
- 搭建/启停主路径建议：可关思考的非 R1 模型；`think=false`；`temperature=0`；**`max_tokens≥2048`**（过低易只输出思考不调工具）。  
- 十几个业务 Agent 换同一默认模型：可在 Dify Console **逐个改**，或用 API **批量**改 `model` 再 publish；不必为换模型重建工具/Skill。  
- **禁止**指望改 Git/`profiles.yaml` 自动切换线上模型。细则见 `docs/运行画像.md`。

短提示词须含硬契约摘要（保名、状态短路、禁思考当回复），细则在 Skill（见 `03` §3）。

## 5. 同名覆盖与误建成旧版

- **同名覆盖**：工作室/顶栏已有同名新版 Agent → **直接改该 Agent**（build-draft / skills / 工具挂载后 publish），禁止另建副本。细则见 `SKILL.md`「有包直接造」第 5 点。  
- **误建成旧版**：在 Agents 下重建并迁移；旧版不得计为合格交付。

## 6. 工作区 `dify-agent/` 落盘白名单（硬约束）

**当前工作区项目根**（本 Skill 仓或业务/公共仓）的 `dify-agent/` **只允许**下列文件：

```text
〈工作区根〉/
└── dify-agent/
    ├── README.md
    └── 〈业务名〉-业务能力包.md    # 或 〈名〉-公共能力包.md
```

| 允许 | 禁止落在 `dify-agent/` |
|------|------------------------|
| `README.md` | Skill 正文 md、Agent 短提示词 md |
| `〈名〉-业务能力包.md` / `〈名〉-公共能力包.md` | 执行报告、build/、specs、临时脚本、截图 |

能力包是**临时中间产物**（确认矩阵 / Dify·页面扒不到时的回退源），不是最终交付。  
Skill 正文、短提示词、执行报告：写进 **Dify**（及对话交付），**不要**作为 `dify-agent/` 常驻文件。  

发现顺序回退：见 `SKILL.md`——Dify 与页面都扒不到业务流程时，**必须先读**本目录对应能力包，再考虑模板回退。
