# 02 Tool 规范（包内）

**改这里**：Workflow 如何发布为可挂载工具、命名、参数暴露给 LLM。  
**前置**：Workflow 已按 `01-Workflow规范` 自测通过。  
**同名**：工作室已有同名已发布 Workflow 工具 → **删除旧 Workflow/工具后按能力包新建并发布**，禁止另建「xxx-2」并存；见 `SKILL.md`「同名先删再建」。

## 1. 命名与一一对应

| 项 | 要求 |
|----|------|
| **工作室 Workflow 应用名**（卡片/侧栏大标题） | **创建时就填中文**（如「数据比对列表查询」）。取矩阵「工作室中文名」。**禁止**把 `list_*` / `create_*` 填进工作室名；误建成英文 → 改名后再发布 |
| 工具名 `name` | 英文蛇形，**仅**在「发布为工具」的机器名字段；给 LLM/Skill 路由（如 `list_esi_compare`）。与工作室标题分离 |
| 显示名 `label` | **必须中文**，与工作室应用名一致或同义 |
| 描述 | 中文；写清何时用、关键参数、空参行为（如有） |
| 对应关系 | 能力矩阵一行 ↔ 一个 Workflow ↔ 一个 Tool |
| 同名匹配键 | 优先按**中文工作室名 / label** 命中后**先删再建**；勿因英文 `name` 另留一套旧壳 |

## 2. 参数

- 仅暴露 Agent/LLM 需要填的项；内部常量放 Workflow 内。  
- `form` 一般用 `llm`（由模型填）。  
- 可选参数在 description 写明默认（如列表 `limit` 默认 10）。  
- 危险操作参数（如 destroy）须在业务 Skill 中约定确认，不单靠工具描述。

### 2.1 鉴权参数（硬约束）

- `token` / `token_id` / `tokenID`：**禁止** `form=llm`（勿让模型填或编造）。  
- 鉴权放 Workflow 内默认 / 环境变量 / 凭据；工具侧对空值及字面量 `None`/`null`/`undefined`/`nil` **一律回落**内置默认，禁止把假 token 打到业务接口。  
- 工具 description、Skill、Agent 短提示词：**禁止**要求或示例「传入 token」。  
- 仅调试需要时可用 `form=form`（人填），仍不得对 LLM 暴露。

## 3. 发布与绑定（含强制同步）

1. Workflow 发布版本（`workflows/publish`）。  
2. 「发布为工具」/ tool-provider 登记（首次）。  
3. **每次改完 Workflow 并 publish 后，必须再同步工具**（硬约束）：  
   - API：先 `GET /console/api/workspaces/current/tool-provider/workflow/get?workflow_app_id=<appId>`  
   - 再 `POST .../tool-provider/workflow/update`（带回 `workflow_tool_id` / `name` / `label` / `description` / `parameters` / `icon` 等必填字段）  
   - 验收：`get` 返回 **`synced=true`**。  
   - **只 publish Workflow、不同步工具 → Agent 仍跑旧图**（会出现「草稿已拒占位名、线上却创建成功」）。  
4. 挂到业务 Agent（见 `04-Agent规范`）；工具名与 Skill 路由一致。

## 4. 创建 / 危险类工具（面向 Agent 的约定）

- **创建类**：失败或半失败时，工具输出须让 Agent 能区分：失败步骤、是否已入库、流程名/主键、接口 `code`/`msg`（有则原样带）（细则见 `01` §2.1.1）。  
- **创建类 flow_name**：工具侧须硬拒绝占位名，返回中文原因、不落库；拒绝文案须要求「立刻用本会话真实流程名重试 create，禁止再问用户要流程名」。  
  最低禁名集（可扩）：`test` / `test_flow` / `my_flow` / `user_flow` / `default` / `demo` / `tmp` / `temp` / `sample` / `example` / `flow` / `对象采集` / `采集任务` / `新建流程` / `新流程`。  
- **预览类（若有）**：须回显「会话流程名」；向导话术写明「用户回确认默认后必须 `create(flow_name=该名)`」。`preview` 入参应带上会话 `flow_name`。  
- **启停/销毁类**：description 写明流程名（或业务主键）必填；空参 → 失败并提示补名，禁止写成可空销毁。  
- **查状态唯一入口**：`operate_*(action=status)`（或矩阵中的单任务状态工具）。统计/预览工具 description 须写明：**禁止用于「查状态/这个流程怎么样」**。  
- **资产解析别名**：环境口语别名（如 `10.0.0.222`、`222-Oracle`）须写入公共 `resolve_asset` 映射；禁止只认正式名导致向导第 1 轮解析失败拖垮确认轮。  
- 工具超时后：靠 Skill 要求 Agent **再查状态/回查**（见 `03`），不要让模型把超时当成「肯定没写入」；超时须短失败白话，不拖垮会话。

## 5. 禁止

- 工具名与 Skill/矩阵不一致。  
- 未自测就发布。  
- **Workflow 已 publish 但工具 `synced=false` 仍交付/验收。**  
- 把整本操作手册塞进工具 description（细则进业务 Skill）。  
- 创建失败只回笼统「失败」、无回查结论、无业务码/文案可供转述。  
- 统计/预览工具描述成「查任务状态」导致与 operate 抢路由。
