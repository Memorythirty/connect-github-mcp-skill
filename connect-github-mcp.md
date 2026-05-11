# GitHub MCP 连接完整指南

> **版本**: 1.0.0
> **作者**: Memorythirty
> **创建日期**: 2026-05-11
> **适用环境**: Windows 10/11 + Docker Desktop + VSCode + Claude Code

---

## 目录

1. [概述](#概述)
2. [前置条件](#前置条件)
3. [安装步骤](#安装步骤)
4. [验证安装](#验证安装)
5. [故障排查](#故障排查)
6. [附录](#附录)

---

## 概述

本指南介绍如何使用 Docker Desktop 的 MCP (Model Context Protocol) 功能连接 GitHub，使 Claude Code 能够直接访问和操作 GitHub 仓库、Issues、Pull Requests 等。

### 什么是 GitHub MCP？

MCP (Model Context Protocol) 是一种协议，允许 AI 助手（如 Claude）与外部服务（如 GitHub）进行交互。通过 Docker Desktop 的 MCP 功能，我们可以：

- 读取和创建 GitHub 仓库
- 管理 Issues 和 Pull Requests
- 查看和提交代码
- 管理分支和标签
- 访问组织和团队信息

---

## 前置条件

### 1. Docker Desktop

**要求版本**: Docker Desktop 4.x+

**安装步骤**:
1. 访问 https://www.docker.com/products/docker-desktop/
2. 下载适合你操作系统的版本
3. 运行安装程序
4. 安装完成后启动 Docker Desktop

**验证安装**:
```powershell
docker info 2>&1 | Select-String -Pattern "Server Version"
```

### 2. GitHub Personal Access Token

**所需权限**:
- `repo` - 完整仓库访问权限
- `read:org` - 读取组织信息的权限
- `workflow` - 管理 GitHub Actions 工作流的权限

**生成步骤**:

1. **访问 Token 设置页面**
   - 打开浏览器，访问: https://github.com/settings/tokens

2. **创建新 Token**
   - 点击 **"Generate new token"** 按钮
   - 选择 **"Classic"** 类型

3. **配置 Token**
   - **Note**: 填写描述，如 "Docker MCP for Claude Code"
   - **Expiration**: 建议选择 90 天

4. **选择权限**:
   - ☑️ **repo**
   - ☑️ **read:org**
   - ☑️ **workflow**

5. **生成并保存 Token**
   - 点击 **"Generate token"**
   - **重要**: 立即复制 Token！

### 3. VSCode with Claude Code Extension

**要求**:
- VSCode 最新版本
- Claude Code 扩展已安装并配置

### 4. Windows PowerShell

**要求版本**: PowerShell 5.1+

---

## 安装步骤

> **重要**: 所有命令必须在 **Windows PowerShell** 中执行！

### 步骤 0: 检查前置条件

**检查 Docker 是否运行**:
```powershell
docker info 2>&1 | Select-String -Pattern "Server Version"
```

**检查 GitHub Secret 是否已设置**:
```powershell
docker mcp secret ls
```

**检查 Profile 是否存在**:
```powershell
docker mcp profile list
```

**检查客户端连接状态**:
```powershell
docker mcp client ls --global
```

### 步骤 1: 设置 GitHub Secret

```powershell
docker mcp secret set github.personal_access_token=<YOUR_TOKEN>
```

将 `<YOUR_TOKEN>` 替换为你的 GitHub Personal Access Token。

### 步骤 2: 安装 dev-workflow 模板

```powershell
docker mcp template use dev-workflow --name dev-workflow
```

### 步骤 3: 全局连接客户端

```powershell
docker mcp client connect claude-code --profile dev_workflow --global
```

**重要**: 必须使用 `--global` 参数！

### 步骤 4: 激活 Profile

在 Claude Code 中执行：
```
mcp-activate-profile: dev_workflow
```

### 步骤 5: 重载 VSCode

1. 按 `Ctrl+Shift+P` 打开命令面板
2. 输入 `Developer: Reload Window`
3. 按 `Enter` 执行

---

## 验证安装

在 Claude Code 中执行：
```
获取我的 GitHub 用户信息
```

Claude 会调用 `mcp__MCP_DOCKER__get_me` 工具，返回你的 GitHub 账户信息。

---

## 故障排查

### 问题 1: "could not find root project root"

**原因**: 连接命令缺少 `--global` 参数

**解决方案**:
```powershell
docker mcp client connect claude-code --profile dev_workflow --global
```

### 问题 2: 工具不可用

**解决方案**:
1. 重新加载 VSCode: `Ctrl+Shift+P` → `Developer: Reload Window`
2. 重新激活 Profile: `mcp-activate-profile: dev_workflow`

### 问题 3: Token 401/403 错误

**解决方案**:
1. 检查 Token 权限
2. 重新生成 Token
3. 更新 Secret: `docker mcp secret set github.personal_access_token=<NEW_TOKEN>`

### 问题 4: Docker Desktop 未启动

**解决方案**: 启动 Docker Desktop 应用

### 问题 5: "docker mcp" 命令不存在

**解决方案**: 更新 Docker Desktop 到最新版本

### 问题 6: WSL 环境中无法执行命令

**解决方案**: 所有 `docker mcp` 命令必须在 Windows PowerShell 中执行

---

## 附录

### A. 常用 MCP 工具列表

| 工具名称 | 功能描述 |
|----------|----------|
| `get_me` | 获取当前用户信息 |
| `search_repositories` | 搜索 GitHub 仓库 |
| `create_repository` | 创建新仓库 |
| `fork_repository` | Fork 仓库 |
| `list_issues` | 列出仓库 Issues |
| `issue_read` | 读取 Issue 详情 |
| `issue_write` | 创建或更新 Issue |
| `list_pull_requests` | 列出 Pull Requests |
| `create_pull_request` | 创建 Pull Request |
| `merge_pull_request` | 合并 Pull Request |
| `list_branches` | 列出分支 |
| `create_branch` | 创建分支 |
| `get_file_contents` | 获取文件内容 |
| `create_or_update_file` | 创建或更新文件 |
| `push_files` | 批量推送文件 |
| `search_code` | 搜索代码 |

### B. 环境要求汇总

| 组件 | 要求 |
|------|------|
| **操作系统** | Windows 10/11 或 Windows Server 2019+ |
| **PowerShell** | 5.1+ |
| **Docker Desktop** | 4.x+ |
| **VSCode** | 最新版本 |
| **Claude Code** | 最新版本 |

### C. 安全注意事项

1. **Token 安全**:
   - 不要将 Token 提交到代码仓库
   - 定期轮换 Token（建议每 90 天）
   - 使用密码管理器存储 Token

2. **权限最小化**:
   - 只授予必要的权限

---

## 参考链接

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub Personal Access Tokens](https://github.com/settings/tokens)
- [GitHub API 文档](https://docs.github.com/en/rest)
- [Claude Code 文档](https://docs.anthropic.com/claude-code)

---

**最后更新**: 2026-05-11
**维护者**: Memorythirty
