# 连接 GitHub MCP

请帮助我连接 GitHub MCP。按照以下步骤执行：

## 执行步骤

### 步骤 0: 检查前置条件
在 Windows PowerShell 中依次执行以下命令：

1. **检查 Docker 是否运行**:
   ```powershell
   docker info 2>&1 | Select-String -Pattern "Server Version"
   ```
   - 如果失败 → 提醒用户启动 Docker Desktop

2. **检查 GitHub Secret 是否已设置**:
   ```powershell
   docker mcp secret ls
   ```
   - 成功标志: 输出包含 `github.personal_access_token`

3. **检查 Profile 是否存在**:
   ```powershell
   docker mcp profile list
   ```
   - 成功标志: 输出包含 `dev_workflow`

4. **检查客户端连接状态**:
   ```powershell
   docker mcp client ls --global
   ```
   - 成功标志: `claude-code: connected`

### 步骤 1: 设置 GitHub Secret（如未设置）
如果步骤 0 中 Secret 未设置，需要用户提供 GitHub Token：

```powershell
docker mcp secret set github.personal_access_token=<TOKEN>
```

**Token 要求**:
- 类型: GitHub Personal Access Token (Classic)
- 权限: `repo`, `read:org`, `workflow`
- 生成地址: https://github.com/settings/tokens

### 步骤 2: 安装 dev-workflow 模板（如未安装）
```powershell
docker mcp template use dev-workflow --name dev-workflow
```

### 步骤 3: 全局连接客户端（如未连接）
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
提醒用户：
- 按 `Ctrl+Shift+P` 打开命令面板
- 输入 `Developer: Reload Window`
- 按 Enter 执行

## 验证安装
执行 `mcp__MCP_DOCKER__get_me` 验证连接是否成功。

## 故障排查

| 问题 | 解决方案 |
|------|----------|
| could not find root project root | 连接命令添加 `--global` 参数 |
| 工具不可用 | 重载 VSCode 或重新激活 profile |
| Token 401/403 | 重新生成 Token 并更新 Secret |
| Docker 未启动 | 启动 Docker Desktop |
| docker mcp 命令不存在 | 更新 Docker Desktop 到最新版 |

## 参考文档
详细文档位于: `connect-github-mcp.md`