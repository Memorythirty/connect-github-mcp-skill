# GitHub MCP 连接 Skill

Claude Code skill：通过 Docker Desktop 连接 GitHub MCP 的完整指南

---

## 目录

- [文件说明](#文件说明)
- [使用指南](#使用指南)
  - [开发者手动安装](#开发者手动安装)
  - [AI 自动执行](#ai-自动执行)
- [前置条件](#前置条件)
- [安装步骤](#安装步骤)
- [验证安装](#验证安装)
- [故障排查](#故障排查)
- [环境要求](#环境要求)
- [注意事项](#注意事项)

---

## 文件说明

本仓库包含以下文件：

| 文件 | 用途 | 放置位置 |
|------|------|----------|
| `连接github.md` | **斜杠命令触发文件** | `~/.claude/commands/` |
| `connect-github-mcp.json` | 结构化配置文件 | `~/.claude/skills/` |
| `connect-github-mcp.md` | 详细参考文档 | `~/.claude/skills/` |

---

## 使用指南

### 开发者手动安装

如果你想在自己的电脑上使用这个 skill，请按照以下步骤操作：

#### 第一步：克隆仓库

```bash
git clone https://github.com/Memorythirty/connect-github-mcp-skill.git
cd connect-github-mcp-skill
```

#### 第二步：复制文件到正确目录

**Windows (PowerShell)**:
```powershell
# 创建目录（如果不存在）
New-Item -ItemType Directory -Force -Path ~/.claude/commands
New-Item -ItemType Directory -Force -Path ~/.claude/skills

# 复制斜杠命令文件
Copy-Item "连接github.md" ~/.claude/commands/

# 复制配置和文档文件
Copy-Item "connect-github-mcp.json" ~/.claude/skills/
Copy-Item "connect-github-mcp.md" ~/.claude/skills/
```

**Linux/macOS (Terminal)**:
```bash
# 创建目录（如果不存在）
mkdir -p ~/.claude/commands
mkdir -p ~/.claude/skills

# 复制斜杠命令文件
cp "连接github.md" ~/.claude/commands/

# 复制配置和文档文件
cp "connect-github-mcp.json" ~/.claude/skills/
cp "connect-github-mcp.md" ~/.claude/skills/
```

#### 第三步：重启 VSCode

按 `Ctrl+Shift+P`，输入 `Developer: Reload Window`，然后按 Enter。

#### 第四步：使用 skill

在 Claude Code 中输入：
```
/连接github
```

或者直接说：
```
连接 GitHub
```

---

### AI 自动执行

如果你是 AI 助手（如 Claude），需要帮助用户连接 GitHub MCP，请按照以下步骤执行：

#### 步骤 0：检查前置条件

在 **Windows PowerShell** 中依次执行以下命令：

```powershell
# 检查 Docker 是否运行
docker info 2>&1 | Select-String -Pattern "Server Version"

# 检查 GitHub Secret 是否已设置
docker mcp secret ls

# 检查 Profile 是否存在
docker mcp profile list

# 检查客户端连接状态
docker mcp client ls --global
```

#### 步骤 1：设置 GitHub Secret（如未设置）

如果用户未设置 GitHub Token，需要用户提供：

```powershell
docker mcp secret set github.personal_access_token=<TOKEN>
```

**Token 要求**：
- 类型：GitHub Personal Access Token (Classic)
- 权限：`repo`, `read:org`, `workflow`
- 生成地址：https://github.com/settings/tokens

#### 步骤 2：安装 dev-workflow 模板（如未安装）

```powershell
docker mcp template use dev-workflow --name dev-workflow
```

#### 步骤 3：全局连接客户端（如未连接）

```powershell
docker mcp client connect claude-code --profile dev_workflow --global
```

**重要**：必须使用 `--global` 参数！

#### 步骤 4：激活 Profile

在 Claude Code 中执行：
```
mcp-activate-profile: dev_workflow
```

#### 步骤 5：重载 VSCode

提醒用户：
- 按 `Ctrl+Shift+P` 打开命令面板
- 输入 `Developer: Reload Window`
- 按 Enter 执行

#### 验证安装

执行 `mcp__MCP_DOCKER__get_me` 验证连接是否成功。

---

## 前置条件

在开始之前，请确保满足以下条件：

### 1. Docker Desktop

- **版本要求**：Docker Desktop 4.x+
- **下载地址**：https://www.docker.com/products/docker-desktop/
- **验证命令**：
  ```powershell
  docker info 2>&1 | Select-String -Pattern "Server Version"
  ```

### 2. GitHub Personal Access Token

- **生成地址**：https://github.com/settings/tokens
- **所需权限**：
  - `repo` - 完整仓库访问权限
  - `read:org` - 读取组织信息
  - `workflow` - 管理 GitHub Actions

### 3. VSCode + Claude Code

- VSCode 最新版本
- Claude Code 扩展已安装

### 4. Windows PowerShell

- 版本要求：PowerShell 5.1+

---

## 安装步骤

### 方式一：使用斜杠命令（推荐）

1. 将 `连接github.md` 复制到 `~/.claude/commands/` 目录
2. 在 Claude Code 中输入 `/连接github`
3. 按照提示执行

### 方式二：手动安装

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
| `could not find root project root` | 连接命令添加 `--global` 参数 |
| 工具不可用 | 重载 VSCode 或重新激活 profile |
| Token 401/403 | 重新生成 Token 并更新 Secret |
| Docker 未启动 | 启动 Docker Desktop |
| `docker mcp` 命令不存在 | 更新 Docker Desktop 到最新版 |
| WSL 环境无法执行 | 在 Windows PowerShell 中执行 |

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

1. **命令执行环境**
   - 所有 `docker mcp` 命令必须在 **Windows PowerShell** 中执行
   - 不要在 WSL/Linux 终端中执行这些命令

2. **Token 安全**
   - 不要将 Token 提交到代码仓库
   - 定期轮换 Token（建议每 90 天）
   - 使用密码管理器存储 Token

3. **权限最小化**
   - 只授予必要的权限
   - 如果只需要读取权限，不要授予写入权限

---

## 参考链接

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub Personal Access Tokens](https://github.com/settings/tokens)
- [GitHub API 文档](https://docs.github.com/en/rest)
- [Claude Code 文档](https://docs.anthropic.com/claude-code)

---

## 更新日志

### v1.0.0 (2026-05-11)
- 初始版本
- 包含完整的安装指南
- 包含故障排查指南
- 支持斜杠命令触发

---

**版本**: 1.0.0
**作者**: Memorythirty
**创建日期**: 2026-05-11
