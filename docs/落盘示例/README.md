# 业务 / 公共项目里怎么放「自己的规则」

## 结论（先看这句）

**自己的规范（能力包 L3）> 公共规范（`rules/` L1）。**  
能力包临时落工作区 `dify-agent/`，最终产物在 Dify（Tool + Skill + Agents）。

| 放哪 | 内容 | 谁维护 |
|------|------|--------|
| **本 Skill 共享仓** `SKILL.md` + `rules/` | L1 统一规范（公共） | 全员约定一起改 |
| **工作区** `dify-agent/`（本 Skill 仓 **或** 业务/公共仓） | L3 能力包（矩阵 + **规则覆盖**）；临时落盘 / 扒不到时回退读 | 搭该业务的人 / AI |
| **`examples/`、本目录** | 样子 / 样板 | 参考用 |

Dify 取模块与页面探查都扒不到业务流程时：必须先读工作区 `dify-agent/` 对应技能包（见根目录 `SKILL.md` 发现顺序）。

---

## 推荐目录

### A. 本 Skill 仓（远程 / Clawbot 拉仓后常见）

```text
dify-business-agent-builder/
├── SKILL.md / rules/ / …
└── dify-agent/                 ← 临时能力包就放这里
    ├── README.md
    └── 〈业务名〉-业务能力包.md
```

### B. 业务项目（例：对象采集所在仓）

```text
〈对象采集业务仓根〉/
├── .cursor/skills/dify-business-agent-builder/   ← submodule（共享 L1）
└── dify-agent/                            ← L3；**仅此二类文件**
    ├── README.md
    └── 对象采集-业务能力包.md
```

> **白名单（硬）**：`dify-agent/` 禁止再放 Skill 正文、Agent 短提示词、执行报告、`build/` 等；那些只进 Dify / 对话交付。见 `rules/04-Agent规范.md` §5。

对照：`docs/落盘示例/业务项目-对象采集/`

### C. 公共项目（例：监控）

```text
〈监控公共仓根〉/
├── .cursor/skills/dify-business-agent-builder/
└── dify-agent/
    ├── README.md
    └── 监控-公共能力包.md
```

对照：`docs/落盘示例/公共项目-监控/`

---

## 模板从哪来

| 你要写 | 复制共享 Skill 里的 |
|--------|---------------------|
| 业务（对象采集、比对…） | `templates/业务能力包模板.md` → 改名为 `〈业务〉-业务能力包.md` |
| 公共（监控、告警…） | `templates/公共能力包模板.md` → 改名为 `〈名〉-公共能力包.md` |

填完后：**能力矩阵 + 第「规则覆盖」节** 就是该项目的自定义规则；不要整份复制 `rules/01`～`04`。

---

## AI 怎么找到

打开工作区对话时：

1. 自动加载本 Skill（L1：`SKILL.md` + `rules/`）。  
2. 再读工作区 `dify-agent/*-业务能力包.md` 或 `*-公共能力包.md`（L3）。  
3. 冲突时：`L3 > L2 > L1`；碰 L0 停问。

话术示例：

- `要搭建对象采集agent`（环境/Dify 走 SKILL 默认；先发现再确认）
- `按 dify-business-agent-builder 搭/维护对象迁移，能力包在 dify-agent/对象迁移-业务能力包.md`
