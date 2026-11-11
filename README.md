# dify-business-agent-builder

云澜 Dify **业务 / 公共 Agent** 搭建 Skill（跨项目共享）。

**完整说明与图解 → [docs/使用说明.md](./docs/使用说明.md)**  
**业务/公共项目里自己的规则怎么放 → [docs/落盘示例/](./docs/落盘示例/README.md)**  
**安装挂载 → [docs/INSTALL.md](./docs/INSTALL.md)**  
**AI 入口 → [SKILL.md](./SKILL.md)**

图：`docs/images/搭Agent闭环.png`、`docs/images/各单位关系.png`

## 目录一览

```text
.
├── SKILL.md       # 【AI】总入口（必须在根）
├── rules/         # 【AI】生成契约 + 01～04
├── templates/     # 【AI】能力包空模板
├── catalog/       # 【AI+人】公共能力已上线目录
├── examples/      # 【参考】样板能力包（可选）
├── docs/          # 【人】安装、使用说明、图解
├── cursor/        # 【人】可选 Cursor rule
└── README.md
```

细则、必备/可选、运维改文件表：一律以 **[docs/使用说明.md](./docs/使用说明.md)** 为准。

## 远端

```text
https://github.com/zhangkf-18091078020/dify-business-agent-builder.git
```
