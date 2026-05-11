# GitHub MCP 连接 Skill

Claude Code skill：通过 Docker Desktop 连接 GitHub MCP 的完整指南

Claude Code skill: Complete guide for connecting GitHub MCP via Docker Desktop

---

## 文件说明

| 文件 | 用途 |
|------|------|
| `connect-github-mcp.json` | 结构化配置，包含完整安装步骤和故障排查 |
| `connect-github-mcp.md` | 详细文档，人工阅读参考 |
| `连接github.md` | Claude Code 斜杠命令触发文件 |

---

## 快速开始

### 前置条件

1. **Docker Desktop** 已安装并运行
2. **GitHub Personal Access Token**（权限：`repo`, `read:org`, `workflow`）
   - 生成地址：https://github.com/settings/tokens
3. **VSCode** + **Claude Code 扩展**

### 安装步骤

#### 方式一：使用斜杠命令（推荐）

1. 将 `连接github.md` 复制到 `~/.claude/commands/` 目录
2. 在 Claude Code 中输入 `/连接github`
3. 按照提示执行

#### 方式二：手动安装

**步骤 1: 设置 GitHub Secret**
```powershell
docker mcp secret set github.personal_access_token=<YOUR_TOKEN>
```

**步骤 2: 安装 dev-workflow 模板**
```powershell
docker mcp template use dev-workflow --name dev-workflow
```

**步骤 3: 全局连接客户端**
```powershell
docker mcp client connect claude-code --profile dev_workflow --global
```

**步骤 4: 激活 Profile**
```
mcp-activate-profile: dev_workflow
```

**步骤 5: 重载 VSCode**
- `Ctrl+Shift+P` → `Developer: Reload Window`

---

## 验证安装

在 Claude Code 中执行：
```
获取我的 GitHub 用户信息
```

如果返回你的 GitHub 账户信息，说明连接成功。

---

## 故障排查

| 问题 | 解决方案 |
|------|----------|
| could not find root project root | 连接命令添加 `--global` 参数 |
| 工具不可用 | 重载 VSCode 或重新激活 profile |
| Token 401/403 | 重新生成 Token 并更新 Secret |
| Docker 未启动 | 启动 Docker Desktop |
| docker mcp 命令不存在 | 更新 Docker Desktop 到最新版 |

---

## 环境要求

| 组件 | 要求 |
|------|------|
| 操作系统 | Windows 10/11 或 Windows Server 2019+ |
| PowerShell | 5.1+ |
| Docker Desktop | 4.x+ |
| VSCode | 最新版本 |
| Claude Code | 最新版本 |

---

## 注意事项

- 所有 `docker mcp` 命令必须在 **Windows PowerShell** 中执行
- 不要在 WSL/Linux 终端中执行这些命令
- Token 不要提交到代码仓库
- 定期轮换 Token（建议每 90 天）

---

## 参考链接

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub Personal Access Tokens](https://github.com/settings/tokens)
- [Claude Code 文档](https://docs.anthropic.com/claude-code)

---

**版本**: 1.0.0
**作者**: Memorythirty
**创建日期**: 2026-05-11
