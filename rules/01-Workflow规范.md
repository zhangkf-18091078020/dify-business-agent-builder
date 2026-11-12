# 01 Workflow 规范（包内）

**改这里**：工作流节点、页面同款接口调用、空参/失败路径自测、禁止空壳。  
**不要写**：Agent URL、Skill 正文、某业务专用字段全集（字段进业务能力包）；**不要写**后端 Java/错误码表等代码规范（由人改业务仓）。

## 1. 交付形态

- 每个已确认能力 → 1 个业务 Workflow → 再按 `02-Tool规范` 发布为工具。  
- **创建 Workflow 时应用名必须填中文**（矩阵「工作室中文名」，如「数据比对列表查询」）；禁止把 `list_esi_compare` 等英文蛇形写入工作室/侧栏标题。英文名只在发布为工具时写入 Tool `name`（见 `02`）。误用英文名 → 改中文后再继续编排/发布。  
- 接口路径/参数名必须与**业务环境页面抓包**一致；禁止编造。  
- 能力包接口表路径须写在反引号内且 **以 `/` 开头**（含上下文根，如 `/esi_compare/...`）；禁止 `Controller/foo.do` 这种无前导 `/` 片段（网关不认，接口列表会空）。  
- **写入 Dify HTTP 节点 `url` 时**必须再拼环境基址成 `http(s)://…`（见 §5.1.1）；表中的 `/esi_…` **不能**原样当作节点 url。  
- 无页面入口、无同款接口证据 → **禁止**发布占位空壳 Workflow。

## 2. 推荐编排（按能力类型选）

### 2.1 创建类

```text
开始 → 准备参数 → 保存主数据 →【失败即停】→ 保存配置 →【失败即停】
  → 完成校验（如有）→ 成功判定 → 失败则停+按名回查 / 成功则启动（如需）→ 汇总输出
```

主数据/配置/校验失败时**不得**继续启动。  
HTTP 节点须设合理超时；失败分支尽快输出，避免整工具被平台超时杀掉后无结论。

#### 2.1.1 创建半失败（搭 Agent 时通用）

业务接口失败 / 工具超时 / `signal:killed`，**都不能**在 Workflow 或 Skill 里推断「库中无数据」。

| 要求 | 说明 |
|------|------|
| 失败即停 | 保存失败后禁止继续配置/校验/启动 |
| 强制回查 | 用本次目标流程名（或业务主键）调详情/按名列表 |
| 有记录 | 输出未完成（partial）：失败步骤 + 流程名 + 当前状态 + 已入库；引导查状态或确认后销毁再重建 |
| 无记录 | 才报未落库，可换名重试 |
| 禁止 | 凭接口失败断言无残留；失败后静默删；未确认覆盖同名 |

建议工具面向 Agent 的输出含：是否成功/部分成功、失败步骤、是否已入库、流程名、状态、下一步提示；并**原样保留**接口返回的 `code`/`msg`（有则带上）。

### 2.2 单任务状态 / 启停类

```text
开始 → 查当前状态 → 按状态机分支 → 操作（如需）→ 有限次轮询 → 输出
```

须区分「操作已提交」与「业务状态已变化」。  
**列表 ≠ 单条状态**：列表见 2.4；单条状态用详情/findById 类接口。  
销毁/删除：Workflow 对**空流程名**应失败并提示补名，禁止猜一条执行（缺名话术见 `03`）。

### 2.3 统计类

```text
开始（流程标识必填）→ 查询 → 格式化 → 结束
```

缺必填标识直接报错，禁止空查询。

### 2.4 列表类（页面有列表时默认建议）

```text
开始 → 调页面同款 List 接口 → 格式化为 Markdown 表 → 结束
```

| 约定 | 说明 |
|------|------|
| 工作室应用名 | 中文，如「〈业务〉列表查询」 |
| 工具名 `name` | `list_<biz>`（仅发布为工具时） |
| 默认条数 | `limit=10`（可覆盖，建议上限 50） |
| 分页 | 按页面约定（对象采集：`offset`=页码，首页为 `1`） |
| 过滤 | 用户提到的条件透传（流程名/状态/macId/dbId 等，以抓包字段为准） |

## 3. 自测最低要求（发布工具前）

- 成功路径至少 1 次。  
- 失败路径至少 1 次（如缺参、错误名）。  
- **创建类**：至少验证失败路径会回查并带出「是否已入库 / 失败步骤」（或等价信息）。  
- 若支持「空流程名自动找最近任务」：必须用空名跑通，且前缀/筛选项为本业务（禁止残留其它业务前缀）。  
- 列表工具：无过滤默认 10 条；带过滤时结果应符合条件。

## 4. 环境变量（换环境在 Dify 改，不写死 IP）

业务 API 基址、默认主机/数据源 ID、落款环境名等 **环境相关常量**，须放在该 Workflow 的 **环境变量**（编排页右上角「环境变量」），节点用 `{{#env.变量名#}}` 或 Code 入参 `value_selector: ["env","变量名"]` 引用。

| 约定 | 说明 |
|------|------|
| 生效位置 | **Dify 该工具 Workflow** 的环境变量 → 改完后 **发布为工具** |
| 不生效 | Cursor / Git 备忘文件（如 `docs/profiles.yaml`）改了不会自动切线上 |
| 命名建议 | 业务前缀 + 含义，如 `OBJ_COLLECT_API_BASE`、`OBJ_COLLECT_DEFAULT_MAC_ID` |
| HTTP | URL 形如 `{{#env.OBJ_COLLECT_API_BASE#}}/gather/operate` |
| 默认资产 | 准备参数 Code 读 env 默认值；用户/LLM 显式传入的 id 优先 |
| 新环境搭建 | 先在本 Workflow（及同业务其它工具）填好同名变量，再发布；详见 `docs/运行画像.md` |
| 样板 | 对象采集「创建任务」工具已示范：`OBJ_COLLECT_API_BASE` 等 |

**目标**：同一业务多工具尽量共用同一套变量名；换环境 = 在 Dify 改变量并发布，不必改 Skill 契约、不必为换 IP 复制整套 Agent。

## 5. API 写 draft / graph（硬约束）

Console API / API Key **不会**给节点补前端默认值。只写 `method`/`url`/`retry` → 工作室编排页会报「渲染此组件时发生了意外错误」。  
把能力包 `` `/esi_…` `` 原样写入 HTTP `url` → Test Run 报 `url should start with http:// or https://`（**先删再建也会重复失败**，因生成逻辑错了）。

**强制**：凡 `PUT/POST` 写入 `workflows/draft`（含先删再建）时：

1. 每个节点带齐 Studio 可渲染完整结构（禁止最小字段壳）；  
2. 每个 HTTP `url` 通过门禁（§5.1.1）；  
3. 图满足最小可交付（§5.2）；  
4. 写入后自检清单（§5.5）全绿才可 publish。

### 5.0 落库前门禁（Builder 每次写 draft 前默念）

```text
□ 工作室名 = 矩阵「工作室中文名」（中文）
□ 每个 HTTP.url 匹配 ^https?://   ← 相对路径一律否决
□ 能力包路径已「基址+路径」转换，未原样粘贴
□ 有 End；非 Start→单 HTTP
□ 业务 HTTP body ≠ none（有表单字段）；有登录/token 或 Code 内等价
□ features.file_upload.enabled 存在
□ 编排能打开；Test Run 无 url should start with http
```

任一项否 → **停写/回滚**，禁止发布为工具。

### 5.1 HTTP 节点（`type=http-request`）必填默认

写入前自检：下列键 **不得为 `undefined`/缺失**（空字符串 `""` 可以；对象必须在）。

| 字段 | 最低默认（可按业务改值，不可省略键） |
|------|--------------------------------------|
| `authorization` | `{ "type": "no-auth", "config": null }` |
| `headers` | `""`（或等价空结构） |
| `params` | `""` |
| `body` | `{ "type": "none", "data": [] }`；表单则 `type=x-www-form-urlencoded` + `data[{id,key,type,value}]` |
| `timeout` | 见下表示例对象（含 connect/read/write 与 max_*） |
| `retry_config` | `{ "retry_enabled": false, "max_retries": 0, "retry_interval": 100 }` |
| `ssl_verify` | `false` 或 `true`（布尔，勿缺） |
| `variables` | `[]` |
| `title` / `desc` / `method` / `url` / `type` | 齐全；`type` 固定 `http-request` |

### 5.1.1 HTTP `url`（试跑硬门槛；重建也必须遵守）

| 来源 | 写法 | 能否写入 HTTP 节点 `url` |
|------|------|---------------------------|
| 能力包接口表 | `` `/esi_compare/AutoDeployController/deployBatch.do` `` | **否**（仅文档/网关） |
| 节点 url（env） | `{{#env.ESI_COMPARE_API_BASE#}}/esi_compare/AutoDeployController/deployBatch.do` | **是**（`ESI_COMPARE_API_BASE`=`http://主机:端口`，**无尾斜杠**） |
| 节点 url（写死，仅临时） | `http://10.0.0.222:49997/esi_compare/AutoDeployController/deployBatch.do` | **是**（随后应抽到 env） |

- **门禁**：落库前对每个 HTTP 节点执行：`url` 必须以 `http://` 或 `https://` 开头（若用 `{{#env.…#}}` 前缀，env 值本身必须是 `http(s)://…`）。  
- **禁止**：`url` = `/esi_compare/...`、`esi_compare/...`、`Controller/foo.do`。此类写入 = **生成缺陷**，删了再建若不改生成逻辑会 **同样失败**。  
- 能力包路径表继续写 `` `/上下文根/...` ``；**转换公式**：`节点url = API_BASE + 表路径`（表路径已含前导 `/`）。

业务 POST（如 deployBatch / 列表）通常还要：

- `body.type` = `x-www-form-urlencoded`（或页面同款），`data` 含页面抓包字段（至少 `tokenID` 等）；**禁止**业务调用仍 `body.type=none` 空跑。  
- 鉴权：先登录拿 `tokenID`（HTTP 或 Code 内），再带入业务请求；**禁止**假设无 token 可调业务接口。

`timeout` 示例（与现网可打开 Workflow 对齐）：

```json
{
  "connect": 10000,
  "read": 300000,
  "write": 60000,
  "max_connect_timeout": 60000,
  "max_read_timeout": 600000,
  "max_write_timeout": 120000
}
```

节点级画布字段也须带上（缺了也可能崩或拖不动）：

| 字段 | 最低要求 |
|------|----------|
| `position` / `positionAbsolute` | `{ "x": number, "y": number }` |
| `width` / `height` | 如 `244` / `120` |
| `sourcePosition` / `targetPosition` | 如 `"right"` / `"left"` |

边 `edges`：source/target、sourceHandle/targetHandle 齐全，与节点 id 对得上。

### 5.2 最小可交付图（禁止 Start→单 HTTP 空壳）

API 新建/先删再建后，Workflow **至少**满足：

| 项 | 要求 |
|----|------|
| Start | `variables` 暴露 Agent/工具入参（可空默认写在 description）；禁止长期 `variables: []` 却宣称业务工具 |
| 业务节点 | 按 `01` §2 类型编排（列表可用 Code 内调接口；创建须登录→写操作→失败回查/汇总） |
| End | **必须有** `type=end`，`outputs` 接到业务结果变量（如 `result`）；禁止只有 Start+HTTP、无输出 |
| edges | Start→…→End 连通；`edge.data` 建议带 `sourceType`/`targetType`/`isInIteration`/`isInLoop` |
| features | `file_upload` 须为对象且含 `enabled`（如 `{ "enabled": false }`），**禁止** `file_upload: {}` |
| 环境变量 | 基址等放 `environment_variables` / 节点 `env`，与 §4 一致 |

对照样板：现网「数据比对列表查询工具」= Start→Code→End；「数据比对创建任务工具」= 多步 HTTP/Code + End。**禁止**只建 Start→一个相对路径 HTTP 就发布。

### 5.3 推荐写法（默认路径；避免再炸 / 再试跑失败）

1. **默认**：拉取同业务**已能 Test Run 通过**的 Workflow draft，深拷整份 `graph` + `features`，只改中文名/业务 url/body/入参。  
2. **其次**：Studio UI「创建空白 Workflow」（平台写齐默认键）再 API 改业务。  
3. **禁止当默认**：从零手搓「Start + 一个 HTTP + 能力包相对路径」。  
4. 写入后跑 §5.5；任一不过 = **未交付**，先修 graph 再 publish。

### 5.4 其它节点

Code / If-Else / Start / End 等同样禁止缺 Studio 默认键；不确定时从可打开样板拷贝整节点，勿手搓最小 JSON。

### 5.5 写入后自检（发布为工具前）

| # | 检查 | 失败表现 |
|---|------|----------|
| 1 | 每个 HTTP.`url` 为 `http(s)://…` 或 env 展开后如此 | `url should start with http:// or https://` |
| 2 | 编排页可打开，无「渲染此组件时发生了意外错误」 | 缺 authorization/timeout 等默认键 |
| 3 | 存在 End，且 outputs 有业务结果 | 工具无输出 / 空壳 |
| 4 | 业务请求有 body 字段 + 登录/token（或 Code 内完整调用） | 业务码失败 / 未授权 |
| 5 | 工作室名为中文 | 违反名称约束 |

## 6. 禁止

- API 写 draft 时省略 HTTP 的 `authorization` / `headers` / `params` / `body` / `timeout`（及画布 `width`/`height`/`position`）。  
- HTTP `url` 使用无协议相对路径（`/esi_…`）；业务 HTTP `body.type=none` 空调用；无 End / 无登录 token 链路却发布为业务工具。  
- 先删再建后仍用同一套「相对路径 HTTP 壳」交差（等于没修生成逻辑）。  
- 只靠全局字符串从其它业务 Workflow 替换交差而不做差异核对 + 自测。  
- 业务 Workflow 内复制公共别名/映射表（应绑公共能力）。  
- 单工具串联超长等待的全生命周期。  
- 创建失败后不回查却对用户说「未入库」，或自动删残留。  
- 把业务环境 IP/基址长期只写死在 HTTP/Code 节点、却要求「一键换环境」。
