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
写配置：`build-draft/checkout` → `PUT build-draft`（`save_strategy=save_to_current_version`）→ `publish`。

## 2. 提示词（短路由）

只保留：角色一句、能力索引（指向 Skill）、≤6 条短路由、输出约定。  
细则在已挂载业务 Skill（及公共 Skill）。

## 3. 挂载

- 能力矩阵内全部业务 Tool。  
- 业务 Skill；能力包勾选的公共 Tool/Skill。  
- 验收：URL 含 `/agents/`，配置页可见 Skill 列表。

## 4. 误建成旧版

在 Agents 下重建并迁移；旧版不得计为合格交付。
