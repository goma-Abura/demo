---
name: dsh-plugin-publish
description: 发布 DSH 插件到 GitHub 并收录进 dsh 生态时使用：构建产物入库、建仓与 topics、awesome 列表 PR、常见编码陷阱。
whenToUse: 插件开发完成后准备公开、或向 awesome-deepseek-harness 提收录 PR 时。
---

# DSH 插件发布与生态收录（依据 dsh-video-studio / dsh-voice-input 实战验证）

## 发布前

- `lib/` 构建产物**必须提交进 git**：GitHub tarball 安装直接取仓库内容，没有 `lib/` 装不上
- 补 LICENSE（MIT）与中英双语 README（`README.md` 英文 + `README.zh.md` 中文互链）
- README 不写本机绝对路径（`D:\...`），统一用 `$DSH_HOME/profiles/<profile>` 通用写法

## 建仓与推送

- 本地：`git init -b main` → commit → `git remote add origin git@github.com:<user>/<repo>.git` → `git push -u origin main`
- GitHub 建仓用 API：`POST /user/repos`（body: `name` / `description` / `private`）
- ⚠️ 编码陷阱：PowerShell 5.1 的 `Invoke-RestMethod` 直接传中文 body 会变成问号；必须 `[Text.Encoding]::UTF8.GetBytes($json)` 传字节流，并带 `ContentType 'application/json; charset=utf-8'`
- 仓库 topics（`PUT /repos/<user>/<repo>/topics`）：`dsh`、`deepseek-harness`、`plugin` 必带，再按功能加（如 `voice-input`、`speech-recognition`）

## 生态收录（awesome-deepseek-harness）

- 目标仓库 `Dominic789654/awesome-deepseek-harness`：fork → 改 `README.md`（英）+ `README.zh-CN.md`（中）→ 提 PR
- 条目放匹配类目（UI 类插件 → `## UI / 客户端` / `## UI / Clients`），一行 markdown 链接 + 一句话描述
- 脚本化要点：fetch 加超时与退避重试（GitHub/raw 偶发网络抖动自愈）；提 PR 前先查同 head 的 open PR 直接复用（幂等，避免重复 PR）
- 仓库 description 与 PR body 用 Node fetch（UTF-8 正确）或 UTF-8 字节流，避开 PowerShell 编码坑
