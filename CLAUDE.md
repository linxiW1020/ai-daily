# AI Daily - Claude Code Skills 项目

## 项目概述

本项目包含用于 Claude Code 的自定义 Skills，主要功能是 AI 行业资讯聚合和 Git 工作流自动化。

### 核心功能

1. **Berlin AI Daily** - 每日 AI 新闻摘要生成器
   - 自动抓取最近 24 小时内的 AI 行业重要新闻
   - 覆盖权威 AI 科技媒体（TechCrunch, The Verge, Hacker News 等）
   - 智能过滤并生成中文精华版摘要
   - 输出精美的可视化 HTML 页面

2. **Commit-Push-PR** - 自动化 Git 工作流
   - 一键完成 commit、push、创建 PR 的完整流程
   - 集成 GitHub MCP 进行 PR 操作
   - 内置 Git 安全协议，防止误操作
   - 自动生成符合规范的 commit 消息

## 项目结构

```
ai-daily/
├── .gitignore                      # Git 忽略文件配置
├── CLAUDE.md                       # 本文件 - 项目说明文档
├── ai-daily.txt                    # Berlin AI Daily skill 需求文档
├── Berlin-ai-daily.skill           # Berlin AI Daily skill 包
├── Berlin_AI_Daily_YYYYMMDD.html   # 生成的 AI 日报示例
└── commit-push-pr.skill            # Commit-Push-PR skill 包
```

## Skills 使用方法

### Berlin AI Daily

**触发方式：**
```
/Berlin-ai-daily
```

或直接描述需求：
- "生成今日 AI 报告"
- "AI 日报"
- "今日 AI 新闻"

**输出：**
生成精美的 HTML 页面，包含：
- 日期标题：「柏林 AI 日报 - [日期]」
- 卡片式设计的新闻摘要
- 每条资讯 50-100 字的核心要点
- 关键词标签
- 可点击跳转到原文链接

### Commit-Push-PR

**触发方式：**
```
/commit-push-pr
```

或描述需求：
- "提交代码并创建 PR"
- "Push my changes and create a pull request"
- "Commit and push"

**工作流程：**
1. 检查 git 状态
2. 暂存文件
3. 创建符合规范的 commit
4. 推送到远程仓库
5. 使用 GitHub MCP 创建 PR

## 技术栈

- **语言**: Python, HTML/CSS
- **工具**: Claude Code, Git, GitHub
- **集成**: GitHub MCP (`@modelcontextprotocol/server-github`)
- **数据源**: TechCrunch, The Verge, Hacker News 等 AI 科技媒体

## 开发背景

- **开发者**: linxiW1020
- **创建时间**: 2026-02-10
- **目的**: 提升工作效率，自动化 AI 资讯收集和代码提交流程

## 未来规划

- [ ] 支持更多 AI 资讯源
- [ ] 添加多语言支持（英文、日文）
- [ ] 增强 Commit-Push-PR skill 的错误处理
- [ ] 添加 PR 模板自定义功能
- [ ] 支持 Issue 自动创建和关联

## 相关链接

- **GitHub 仓库**: https://github.com/linxiW1020/ai-daily
- **Claude Code 文档**: https://code.claude.com/docs
- **GitHub MCP**: https://github.com/modelcontextprotocol/servers

## 许可证

MIT License

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)
