# AI Daily 项目 - 知识库与记忆

## 项目信息

- **项目名称**: AI Daily - Claude Code Skills
- **开发者**: linxiW1020
- **GitHub 仓库**: https://github.com/linxiW1020/ai-daily
- **创建时间**: 2026-02-10
- **主要功能**: AI 行业资讯聚合、Git 工作流自动化

---

## 2026-02-10 工作记录

### 1. GitHub MCP 安装与配置

#### 目标
安装 GitHub MCP Server，用于集成 GitHub PR 创建功能到 Claude Code。

#### 遇到的问题

**问题 1: 包名错误**
- **错误**: 尝试安装 `@github/github-mcp-server` 失败
- **错误信息**: `404 Not Found`
- **原因**: 正确的包名是 `@modelcontextprotocol/server-github`
- **解决**: 更新配置文件使用正确的包名

**问题 2: Token 权限不足**
- **错误**: 使用第一个 token 创建仓库时返回 404
- **原因**: Token 缺少 `public_repo` 或 `repo` 权限
- **解决**: 更换为有完整权限的 token（具有 repo 和 public_repo 权限）

#### 最终配置
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN_HERE"
      }
    }
  }
}
```

#### 关键经验
- GitHub MCP 正确包名：`@modelcontextprotocol/server-github`
- Token 需要至少 `public_repo` 权限才能创建公开仓库
- 验证命令：`claude mcp list`

---

### 2. /commit-push-pr Skill 创建

#### 目标
创建一个 Claude Code skill，自动化 commit、push、创建 PR 的完整流程。

#### 遇到的问题

**问题 1: init_skill.py 编码错误**
- **错误**: `UnicodeEncodeError: 'gbk' codec can't encode character`
- **原因**: Windows CMD 默认使用 GBK 编码，脚本包含 emoji
- **尝试解决**: 设置 `PYTHONIOENCODING=utf-8` 无效
- **最终解决**: 手动创建目录结构

**问题 2: YAML frontmatter 格式错误**
- **错误**: `Invalid YAML in frontmatter: mapping values are not allowed here`
- **原因**: description 中包含括号 `(1)` 导致 YAML 解析失败
- **解决**: 将整个 description 用引号包裹，并移除编号

**问题 3: package_skill.py 编码错误**
- **错误**: `UnicodeDecodeError: 'gbk' codec can't decode byte 0x93`
- **原因**: SKILL.md 中包含特殊字符（如箭头 `↓`）
- **解决**: 将所有特殊字符替换为 ASCII（`↓` → `|` + `v`）

#### Skill 结构
```
commit-push-pr/
├── SKILL.md                        # 主工作流程
└── references/
    ├── commit-types.md             # 提交消息类型参考
    └── pr-template.md              # PR 模板
```

#### 关键经验
- Windows 环境下避免使用 emoji 和特殊字符
- YAML frontmatter 中字符串包含特殊字符时必须用引号
- Skill 描述需要清晰说明触发场景
- 使用 Conventional Commits 规范

---

### 3. Git 仓库初始化与推送

#### 目标
将 ai-daily 项目初始化为 git 仓库并推送到 GitHub。

#### 工作流程
1. ✓ 初始化本地仓库：`git init`
2. ✓ 创建 .gitignore 文件
3. ✓ 添加所有文件：`git add .`
4. ✓ 创建初始提交
5. ✓ 使用 GitHub API 创建远程仓库
6. ✗ git push 失败（网络问题）
7. ✓ 使用 GitHub API 上传文件

#### 遇到的问题

**问题 1: HTTPS 端口 443 连接失败**
- **错误**: `Failed to connect to github.com port 443`
- **尝试**:
  - 重试多次无效
  - ping github.com 成功（网络可达）
  - 切换到 SSH 方式：`Host key verification failed`（未配置 SSH 密钥）
- **最终解决**: 使用 GitHub API 直接上传文件

**问题 2: git push 与 API 上传的 commit SHA 不一致**
- **本地 SHA**: 7c02c5a
- **远程 SHA**: a7583d6
- **原因**: 通过 API 创建的提交与本地 git commit 不同
- **影响**: 无实质影响，内容完全一致

#### GitHub API 上传文件方法
```bash
# Base64 编码文件
base64_content=$(base64 CLAUDE.md)

# 使用 API 上传
curl -X PUT \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  "https://api.github.com/repos/USER/REPO/contents/FILE_PATH" \
  -d "{\"message\":\"commit message\",\"content\":\"$base64_content\"}"
```

#### 关键经验
- 网络连接问题可能只是特定端口（如 HTTPS 443）
- GitHub API 是可靠的备用方案
- SSH 需要预先配置密钥对
- API 创建的提交与本地 git commit 是独立的

---

### 4. 文档编写

#### CLAUDE.md 内容
- 项目概述和核心功能
- Skills 使用说明
- 项目结构
- 技术栈
- 开发背景
- 未来规划
- 相关链接

#### memory.md 内容（本文件）
- 每次对话的重要记录
- 遇到的问题和解决方案
- 技术决策记录
- 最佳实践总结

---

## 技术决策记录

### 1. 为什么使用 GitHub MCP 而非 gh CLI？
- **原因**:
  - gh CLI 未安装
  - GitHub MCP 与 Claude Code 原生集成
  - 跨平台一致性更好
- **决策**: 使用 GitHub MCP

### 2. 为什么使用 API 上传而非解决网络问题？
- **原因**:
  - 网络问题可能是临时性的
  - API 方案更快更可靠
  - 不需要修改系统配置
- **决策**: 优先使用 API，后续可配置代理

### 3. Skill 文件编码选择
- **决策**: 使用纯 ASCII，避免特殊字符
- **原因**: Windows GBK 编码环境兼容性

---

## 常用命令速查

### GitHub MCP
```bash
# 检查 MCP 状态
claude mcp list

# 验证连接
claude mcp health github
```

### Git
```bash
# 初始化仓库
git init

# 添加远程仓库
git remote add origin <URL>

# 推送（HTTPS）
git push -u origin main

# 推送（SSH）
git push -u origin main
```

### GitHub API
```bash
# 创建仓库
curl -X POST -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/user/repos \
  -d '{"name":"repo-name","private":false}'

# 上传文件
base64_content=$(base64 file.txt)
curl -X PUT -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/repos/USER/REPO/contents/FILE_PATH \
  -d "{\"message\":\"commit msg\",\"content\":\"$base64_content\"}"

# 查看仓库内容
curl -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/repos/USER/REPO/contents/
```

### Skill 创建
```bash
# 初始化 skill
python ~/.claude/skills/skill-creator/scripts/init_skill.py <skill-name> --path <output-dir>

# 打包 skill
python ~/.claude/skills/skill-creator/scripts/package_skill.py <skill-path>
```

---

## 环境配置

### 系统信息
- **操作系统**: Windows 10/11
- **Git**: 已安装
- **Python**: 已安装（Anaconda）
- **Node.js**: 已安装（npx 可用）

### Claude Code 配置
- **配置文件**: `C:\Users\JackLee\.claude.json`
- **Skills 目录**: `C:\Users\JackLee\.claude\skills\`
- **MCP Servers**: GitHub MCP, GLM-Camp

### GitHub Token
- **权限要求**: repo, public_repo
- **配置位置**: `~/.claude.json` → `mcpServers.github.env.GITHUB_TOKEN`
- **注意**: 不要在代码或文档中硬编码 Token

---

## 最佳实践

### 1. Git 提交规范
```bash
# 使用 Conventional Commits
git commit -m "feat: add new feature"
git commit -m "fix: resolve bug"
git commit -m "docs: update documentation"

# 使用 heredoc 格式
git commit -m "$(cat <<'EOF'
feat: add feature description

Detailed explanation of the change.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
EOF
)"
```

### 2. Skill 编写规范
- 使用 ASCII 字符，避免特殊符号
- YAML frontmatter 用引号包裹复杂字符串
- description 清晰说明触发场景
- 保持 SKILL.md 简洁，详细内容放 references

### 3. 问题排查流程
1. 阅读完整错误信息
2. 搜索官方文档
3. 尝试替代方案（如 API 替代 git push）
4. 记录问题和解决方案

---

## 未来待办

### 技术改进
- [ ] 配置 Git 代理或 SSH 密钥
- [ ] 修复 git push 网络连接问题
- [ ] 测试 /commit-push-pr skill 实际使用
- [ ] 优化 skill 文件编码兼容性

### 功能增强
- [ ] Berlin AI Daily skill 实现新闻抓取
- [ ] 添加更多资讯源
- [ ] 支持多语言输出
- [ ] 添加 PR 模板自定义

### 文档完善
- [ ] 添加 README.md
- [ ] 编写 Skill 使用教程
- [ ] 添加 CONTRIBUTING.md
- [ ] 创建 CHANGELOG.md

---

## 错误与解决方案索引

| 错误类型 | 错误信息 | 解决方案 | 日期 |
|---------|---------|---------|------|
| 包名错误 | `404 Not Found` | 使用 `@modelcontextprotocol/server-github` | 2026-02-10 |
| Token 权限 | `404 Not Found` (API) | 更换有 repo 权限的 token | 2026-02-10 |
| 编码错误 | `UnicodeEncodeError: 'gbk'` | 手动创建目录，避免特殊字符 | 2026-02-10 |
| YAML 格式 | `mapping values are not allowed` | 用引号包裹字符串 | 2026-02-10 |
| 网络连接 | `Failed to connect to github.com:443` | 使用 GitHub API 上传 | 2026-02-10 |
| SSH 连接 | `Host key verification failed` | 需配置 SSH 密钥或使用 HTTPS | 2026-02-10 |

---

## 资源链接

### 官方文档
- [Claude Code 文档](https://code.claude.com/docs)
- [GitHub MCP Server](https://github.com/modelcontextprotocol/servers)
- [Git 官方文档](https://git-scm.com/doc)
- [Conventional Commits](https://www.conventionalcommits.org/)

### 工具链接
- [GitHub API 文档](https://docs.github.com/en/rest)
- [npm: @modelcontextprotocol/server-github](https://www.npmjs.com/package/@modelcontextprotocol/server-github)

### 项目链接
- [GitHub 仓库](https://github.com/linxiW1020/ai-daily)
- [Berlin AI Daily Skill](https://github.com/linxiW1020/ai-daily/blob/main/Berlin-ai-daily.skill)
- [Commit-Push-PR Skill](https://github.com/linxiW1020/ai-daily/blob/main/commit-push-pr.skill)

---

## 更新日志

### 2026-02-10
- 创建项目知识库
- 记录 GitHub MCP 安装过程
- 记录 Skill 创建流程
- 记录 Git 仓库初始化
- 添加常用命令速查
- 添加错误解决方案索引

---

**注意**: 每次对话结束后，请更新本文件，记录新的内容、问题和解决方案。

🤖 Generated with [Claude Code](https://claude.com/claude-code)
