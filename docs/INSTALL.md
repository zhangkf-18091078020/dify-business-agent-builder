# 安装：拉下来就能让 Cursor 读到本 Skill

Cursor 会自动加载项目内：

```text
<业务仓库>/.cursor/skills/dify-business-agent-builder/SKILL.md
```

各业务仓不同，**不要把规则正文复制进每个仓**；用 **Git 子模块**挂共享 Skill 源。

本仓库结构：

- **根目录**：给 AI 的 Skill（`SKILL.md`、`01`～`04`、能力包等）
- **`docs/`**：给人看的安装与说明（本文件）

---

## 推荐：子模块（pull 即更新）

### 0. Skill 共享仓

```text
http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git
```

（Gogs 也可能显示 `http://release:3000/...`，本机一般用 IP。）

### 1. 每个业务仓库加一次

```bash
git submodule add http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git .cursor/skills/dify-business-agent-builder
git commit -m "chore: add dify-business-agent-builder skill submodule"
git push
```

可选：把包内 `project-rule-dify-agent-builder.mdc` 复制为业务仓 `.cursor/rules/dify-agent-builder.mdc`。

### 2. 同事日常

```bash
git pull
git submodule update --init --recursive
```

打开业务项目 → **新开** Agent 对话 →「按 dify-business-agent-builder 搭…」。

共享仓有更新后：

```bash
git submodule update --remote .cursor/skills/dify-business-agent-builder
```

再新开对话。

---

## 备选：用户级 Skill

```text
git clone http://192.168.1.174:3000/zhangkefan/dify-business-agent-builder.git %USERPROFILE%\.cursor\skills\dify-business-agent-builder
```

之后 `cd` 进去 `git pull`，再新开对话。

---

## 其它 AI（Claude Code / Codex）

整包拷贝即可，换目录名：

| 产品 | 路径 |
|------|------|
| Claude Code | `.claude/skills/dify-business-agent-builder/` 或 `~/.claude/skills/...` |
| Codex | `.agents/skills/dify-business-agent-builder/` 或 `~/.agents/skills/...` |

Dify：把包打成 zip（含根目录 `SKILL.md`）在 Skills 页 Import；用途与本地搭建手册不同。

---

## 不要用的方式

- 只解压 zip 到某一个业务仓、从不进 Git  
- 每个业务仓手拷全文导致规则分叉
