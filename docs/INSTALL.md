# 安装：拉下来就能让 Cursor 读到本 Skill

> 目录分层、必备/可选、出了问题改哪 → 见同目录 **[使用说明.md](./使用说明.md)**。  
> 本文只写：**怎么挂到业务仓 / 其它 AI**。

Cursor 加载：

```text
<业务仓库>/.cursor/skills/dify-business-agent-builder/SKILL.md
```

不要用手拷规则正文；用 **Git 子模块**挂本仓。

仓库分层摘要：`SKILL.md`+`rules/`+`templates/`+`catalog/` 给 AI；`examples/` 参考；`docs/`+`README` 给人（详见使用说明）。

---

## 推荐：子模块

共享仓：

```text
http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git
```

业务仓一次：

```bash
git submodule add http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git .cursor/skills/dify-business-agent-builder
git commit -m "chore: add dify-business-agent-builder skill submodule"
git push
```

可选：复制 `cursor/project-rule-dify-agent-builder.mdc` → 业务仓 `.cursor/rules/dify-agent-builder.mdc`。

日常：

```bash
git pull
git submodule update --init --recursive
```

新开对话 →「按 dify-business-agent-builder 搭…」。

共享仓更新后：

```bash
git submodule update --remote .cursor/skills/dify-business-agent-builder
```

## 备选：用户级

```text
git clone http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git %USERPROFILE%\.cursor\skills\dify-business-agent-builder
```

## 其它 AI

整包拷贝，换宿主目录即可：

| 产品 | 路径 |
|------|------|
| Claude Code | `.claude/skills/dify-business-agent-builder/` |
| Codex | `.agents/skills/dify-business-agent-builder/` |

## 不要

- 只解压 zip 不进 Git  
- 每个业务仓手拷全文导致分叉
