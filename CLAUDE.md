# CLAUDE.md

本文档为 Claude Code (claude.ai/code) 提供本仓库的工作指导。

---

## 项目概览

这是一个基于 [Jekyll](https://jekyllrb.com/) 的学术个人主页，使用 [al-folio](https://github.com/alshedivat/al-folio) 主题。部署在 GitHub Pages，地址：https://aceee1222.github.io

---

## 本地开发

### 启动本地服务器

**前置要求**：Docker Desktop 必须已安装并运行

```bash
# 拉取镜像并启动
docker compose pull && docker compose up

# 访问 http://localhost:8080
# 文件修改会自动热重载
```

停止服务器：`Ctrl+C` 或 `docker compose down`

### 轻量版镜像

```bash
docker compose -f docker-compose-slim.yml up
```

### 容器调试

```bash
# 查看日志
docker logs aceee-1222githubio-jekyll-1

# 进入容器内部
docker compose exec -it jekyll /bin/bash
```

---

## 架构与核心概念

### Jekyll Scholar（论文管理）

论文通过 BibTeX 文件 `_bibliography/papers.bib` 管理，使用 [Jekyll Scholar](https://github.com/inukshuk/jekyll-scholar) 渲染。

**作者名字高亮**：在 `_config.yml` 中配置：
```yaml
scholar:
  last_name: [Hong]
  first_name: [Zhiyi, Z.]
```

**精选论文**：在 BibTeX 条目中添加 `selected={true}`，首页会显示。模板通过 `_includes/selected_papers.liquid` 筛选，使用查询 `{ bibliography --group_by none --query @*[selected=true]* }`。

**扩展字段**：支持 `abstract`, `arxiv`, `code`, `pdf`, `poster`, `slides`, `video`, `website`, `preview`（预览图放 `assets/img/publication_preview/`）

### 内容集合

| 目录 | 用途 |
|------|------|
| `_pages/` | 静态页面，首页是 `_pages/about.md`（`permalink: /`） |
| `_posts/` | 博客文章 |
| `_news/` | 新闻动态，首页显示（使用 `inline: true` 紧凑展示） |
| `_projects/` | 项目展示 |
| `_bibliography/` | 论文 BibTeX 文件 |

### 数据文件

| 文件 | 用途 |
|------|------|
| `_config.yml` | 主配置，关键字段：`url`, `baseurl`, `title`, `first_name`, `last_name`, `socials` |
| `_data/cv.yml` | CV 数据（YAML 格式，替代 `assets/json/resume.json`） |
| `_data/socials.yml` | 社交媒体链接 |

### 布局系统

- `_layouts/about.liquid`：首页布局，控制头像、新闻、精选论文、教育经历、社交链接的展示
- `_layouts/bib.liquid`：单篇论文布局
- `_includes/`：可复用的模板片段

**添加新模块（如教育经历）**：
1. 数据放 `_data/education.yml`
2. 模板放 `_includes/education.liquid`
3. 在 `_layouts/about.liquid` 合适位置插入
4. 在 `_pages/about.md` front matter 启用：`education: true`

### 部署流程

GitHub Actions（`.github/workflows/deploy.yml`）自动部署：
1. 推送到 `main` 分支触发工作流
2. Jekyll 构建站点
3. 输出推送到 `gh-pages` 分支
4. GitHub Pages 从 `gh-pages` 分支提供服务

**重要设置**：仓库 Settings → Pages → Source 必须设为 `gh-pages` 分支（不是 `main`）

---

## YAML 语法注意事项

`_config.yml` 对语法错误敏感，常见问题：
- 空值保留 `key:`（不要删除）
- 多行字符串使用正确 YAML 语法
- 缩进用空格，不能用 Tab

如果 Docker 容器启动失败并报 Psych 解析错误，检查 `_config.yml` 语法（如值中间是否有换行）。

---

## 工作流程规范

### 经验沉淀

**每次重大改动或遇到问题时，必须将经验教训总结到 `PROGRESS.md`**：

- 问题描述与根因分析
- 解决方案与关键步骤
- 可复用的模式与最佳实践
- 避免重复踩坑

**目的**：同样的错误下次不要再犯，持续积累领域知识。

---

## 常用文件速查

| 内容 | 位置 |
|------|------|
| 首页内容 | `_pages/about.md` |
| 论文 BibTeX | `_bibliography/papers.bib` |
| 论文预览图 | `assets/img/publication_preview/` |
| CV 数据 | `_data/cv.yml` |
| 社交链接 | `_data/socials.yml` |
| 站点配置 | `_config.yml` |
| 自定义样式 | `_sass/`、`assets/css/` |
