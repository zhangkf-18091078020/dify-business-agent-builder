# dify-business-agent-builder

云澜 Dify **业务 / 公共 Agent** 搭建 Skill（跨项目共享）。

**完整说明（目录分层、出了问题改哪、人看 vs AI 看）→ [docs/使用说明.md](./docs/使用说明.md)**  
**业务/公共项目里自己的规则怎么放 → [docs/落盘示例/](./docs/落盘示例/README.md)**  
**安装挂载 → [docs/INSTALL.md](./docs/INSTALL.md)**  
**AI 入口 → [SKILL.md](./SKILL.md)**

## 目录一览

```text
.
├── SKILL.md       # 【AI】总入口（必须在根）
├── rules/         # 【AI】生成契约 + 01～04
├── templates/     # 【AI】能力包空模板
├── catalog/       # 【AI+人】公共能力已上线目录
├── examples/      # 【参考】样板能力包（可选）
├── docs/          # 【人】安装与使用说明
├── cursor/        # 【人】可选 Cursor rule
└── README.md
```

细则、必备/可选、运维改文件表：一律以 **[docs/使用说明.md](./docs/使用说明.md)** 为准。

## 远端

```text
http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git
```
