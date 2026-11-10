# 业务 / 公共项目里怎么放「自己的规则」

## 结论（先看这句）

**是的：对象采集自己的规则、监控自己的规则，落在各自业务/公共项目仓库里，不写进本 Skill 共享仓的 `rules/`。**

| 放哪 | 内容 | 谁维护 |
|------|------|--------|
| **本 Skill 共享仓**（子模块挂进各项目） | L1 统一规范：`SKILL.md`、`rules/`、`templates/`、`catalog/` | 全员约定一起改 |
| **各业务项目 / 公共项目** 自己的 `dify-agent/` | L3 能力包（能力矩阵 + **规则覆盖**） | 该业务/平台同事 |

共享仓里的 `examples/`、本目录 `docs/落盘示例/` **只是样子**，真用时拷到对应项目去改。

---

## 推荐目录（拷到真实项目根）

### 业务项目（例：对象采集所在仓）

```text
〈对象采集业务仓根〉/
├── .cursor/
│   └── skills/
│       └── dify-business-agent-builder/   ← git submodule（共享 L1）
└── dify-agent/                            ← 本业务自己的规则（L3）；**仅此二文件**
    ├── README.md
    └── 对象采集-业务能力包.md
```

> **白名单（硬）**：`dify-agent/` 禁止再放 Skill 正文、Agent 短提示词、执行报告、`build/` 等；那些只进 Dify / 对话交付。见 `rules/04-Agent规范.md` §5。

对照本 Skill 仓里的样子：

```text
docs/落盘示例/业务项目-对象采集/
└── dify-agent/
    ├── README.md
    └── 对象采集-业务能力包.md      ← 从 templates/业务能力包模板.md 起草
```

### 公共项目（例：监控平台仓）

```text
〈监控公共仓根〉/
├── .cursor/skills/dify-business-agent-builder/   ← 同一 submodule
└── dify-agent/                            ← **仅此二文件**
    ├── README.md
    └── 监控-公共能力包.md          ← 从 templates/公共能力包模板.md 起草
```

对照：

```text
docs/落盘示例/公共项目-监控/
└── dify-agent/
    ├── README.md
    └── 监控-公共能力包.md
```

---

## 模板从哪来

| 你要写 | 复制共享 Skill 里的 |
|--------|---------------------|
| 业务（对象采集、比对…） | `templates/业务能力包模板.md` → 改名为 `〈业务〉-业务能力包.md` |
| 公共（监控、告警…） | `templates/公共能力包模板.md` → 改名为 `〈名〉-公共能力包.md` |

填完后：**能力矩阵 + 第「规则覆盖」节** 就是该项目的自定义规则；不要整份复制 `rules/01`～`04`。

---

## AI 怎么找到

打开**该业务/公共项目**对话时：

1. 自动加载子模块里的共享 Skill（L1）。  
2. 再读本仓 `dify-agent/*-业务能力包.md` 或 `*-公共能力包.md`（L3）。  
3. 冲突时：`L3 > L2 > L1`；碰 L0 停问。

话术示例：`按 dify-business-agent-builder 搭对象采集，能力包在 dify-agent/对象采集-业务能力包.md`。
