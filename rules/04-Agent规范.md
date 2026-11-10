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

### 2.1 公共解析与业务工具串联（硬约束）

能力包勾选了资产解析（或同类公共解析，如 `resolve_asset` / `asset-resolve`）时，生成的短提示词**必须**含等价表述：

- 用户最终意图为**列表 / 创建（搭建）/ 启停 / 删除**等业务动作，且话里出现主机名/数据源名（或需解析的资产名）：先调公共解析工具，**同一轮内**将得到的 id（如 `mac_id`/`db_id`）传入业务工具；**禁止**只回复解析结果后结束。
- 仅当用户明确**只要解析**、无业务动作时，才可停在解析结果。
- 解析成功但业务必参仍缺：追问缺参；必参已齐：**同一轮**调用业务工具（如 `create_*`），不得等待用户重复下达同一意图。
- 「搭建任务」场景：解析与创建所需 id/名已齐时，必须调到 `create_*`（或矩阵中的创建工具），不得停在「主机解析成功」。

### 2.2 验收（生成后自测）

- 绑了公共解析的业务 Agent：一句同时含「资产名 + 搭建/列表」→ 工具轨迹须为「解析 → 业务 Tool」，不得止于解析。
- 一句仅「解析主机/数据源」→ 允许只返回解析结果。

## 3. 挂载

- 能力矩阵内全部业务 Tool。  
- 业务 Skill；能力包勾选的公共 Tool/Skill。  
- 验收：URL 含 `/agents/`，配置页可见 Skill 列表。

## 4. 误建成旧版

在 Agents 下重建并迁移；旧版不得计为合格交付。

## 5. 业务仓 `dify-agent/` 落盘白名单（硬约束）

业务/公共项目仓库的 `dify-agent/` **只允许**下列文件（可有其一或两者；禁止再落其它文件）：

```text
〈业务仓〉/
└── dify-agent/
    ├── README.md
    └── 〈业务名〉-业务能力包.md    # 或 〈名〉-公共能力包.md
```

| 允许 | 禁止落在 `dify-agent/` |
|------|------------------------|
| `README.md` | Skill 正文 md、Agent 短提示词 md |
| `〈名〉-业务能力包.md` / `〈名〉-公共能力包.md` | 执行报告、build/、specs、临时脚本、截图 |

Skill 正文、短提示词、执行报告：写进 **Dify**（及对话交付），**不要**作为业务仓 `dify-agent/` 常驻文件。
