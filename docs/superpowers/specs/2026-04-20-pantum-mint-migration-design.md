# 迁移 pantum-sdk-docs 的 Mint 内容至 docs 工程

日期：2026-04-20
源工程：`/Users/zolrath/Desktop/code/pantum-sdk-docs`
目标工程：`/Users/zolrath/Desktop/code/docs`（当前工程）

## 背景

源工程是一个 pnpm workspace，除了 Mintlify 文档站，还包含 NestJS 后端（`apps/api/`）和前端门户（`apps/portal-web/`）。目标工程是空白的 Mintlify Starter Kit。现在要把源工程中 **Mintlify 相关** 的全部内容搬到目标工程，同时丢弃与 Mint 无关的 `apps/`、pnpm workspace 配置等。

源工程的 Mint 文档内容（`en/`、`zh-Hans/`、`images/`、`openapi/`）不含任何对 `apps/`、portal-web URL、数据库或 pnpm 脚本的引用（已用 grep 全量校验），可以独立迁出。

## 需求清单

1. 目标工程的 Mintlify Starter Kit 示例内容要 **完全替换** 为源工程的 Pantum SDK 文档。
2. 源工程 `docs.json` 的 `navbar.links`（两条 `http://localhost:35127` 链接）要去掉，改为空数组。
3. 目标 `README.md` 重写为描述 Pantum SDK 文档站的新版；其他顶层元数据文件（`AGENTS.md`、`CONTRIBUTING.md`、`LICENSE`）保留不动。
4. 源工程的非 Mint 部分（`apps/`、pnpm 配置、`.npmrc`、`.codex`、`node_modules`、源的 `README.md` 等）一律不迁移。
5. 整个迁移以 **单个 git commit** 落地，信息：`Migrate Mint content from pantum-sdk-docs`。

## 文件变更清单

### 目标工程最终根目录

```
docs/
├── .git/              ← 保留
├── .mintignore        ← 覆盖（用源版本）
├── AGENTS.md          ← 保留
├── CONTRIBUTING.md    ← 保留
├── LICENSE            ← 保留
├── README.md          ← 重写（见下方草稿）
├── docs.json          ← 覆盖（用源版本 + navbar.links 置空）
├── favicon.ico        ← 新增（从源拷贝）
├── logo-dark.png      ← 新增（从源拷贝）
├── logo-light.png     ← 新增（从源拷贝）
├── style.css          ← 新增（从源拷贝）
├── en/                ← 新增整个目录（47 个 .mdx）
├── zh-Hans/           ← 新增整个目录（47 个 .mdx）
├── images/            ← 覆盖（目标示例图 → 源 icons/）
└── openapi/           ← 新增（含 onesdk-core.yaml）
```

### 要删除的 Starter Kit 残留

- `docs/index.mdx`
- `docs/quickstart.mdx`
- `docs/development.mdx`
- `docs/essentials/`（整个目录）
- `docs/ai-tools/`（整个目录）
- `docs/api-reference/`（整个目录，含 `endpoint/`、`introduction.mdx`）
- `docs/snippets/`（整个目录）
- `docs/logo/`（整个目录：`light.svg`、`dark.svg`）
- `docs/favicon.svg`
- `docs/images/` 原有示例图（会被源 `images/` 覆盖）

### 显式不迁移的源工程内容

`apps/`、`node_modules/`、`package.json`、`pnpm-lock.yaml`、`pnpm-workspace.yaml`、`.npmrc`、`.codex`、`.claude`、`.gitignore`、源工程的 `README.md`。

## `docs.json` 最终形态

源 `docs.json` 原样迁过来，只做一处改动：`navbar.links` 两条本地链接替换为空数组。

```json
"navbar": {
  "links": []
}
```

其余字段全部沿用源版本：

| 字段 | 值 |
|------|------|
| `name` | `"Pantum SDK Docs"` |
| `theme` | `"mint"` |
| `appearance.default` | `"system"` |
| `colors` | primary `#1857FF`, light `#DFF8F1`, dark `#0F172A` |
| `favicon` | `/favicon.ico` |
| `logo.light` / `logo.dark` | `/logo-light.png` / `/logo-dark.png` |
| `api.playground.display` | `"simple"` |
| `navigation.languages` | `zh-Hans`（默认） + `en`；每种语言 4 个 tab：使用指南 / API 文档 / 更新日志 / 常见问题 |
| `footer.socials.github` | `"https://github.com/"`（保留源工程占位值，后续可替换） |

目标 Starter Kit `docs.json` 里独有但源没有的以下字段会丢弃：

- `contextual.options`（copy / view / chatgpt / claude / perplexity / mcp / cursor / vscode）
- `navigation.global.anchors`（Documentation / Blog）
- `navbar.primary`（Dashboard 按钮）

## 新 `README.md` 草稿

```markdown
# Pantum SDK Docs

Pantum SDK 文档站的 Mintlify 源。

## 内容结构

- `docs.json` — Mintlify 站点配置与双语导航
- `zh-Hans/` — 中文文档（默认语言）
- `en/` — 英文镜像文档，目录层级与中文保持同构
- `openapi/onesdk-core.yaml` — OpenAPI 规范
- `images/` — 文档图标资源
- `style.css` — 站点自定义样式
- `logo-light.png` / `logo-dark.png` / `favicon.ico` — 品牌资源

## 本地预览

​```bash
npm i -g mint
mint dev
​```

默认监听 `http://localhost:3000`。

## 校验

​```bash
mint broken-links
​```

## 多语言组织约定

- 中文为默认语言，英文镜像文档保存在 `en/` 下，与中文目录层级一致。
- 跨语言共享的导航入口通过 `docs.json` 的 `navigation.languages` 分别配置。
```

> 写入时请把上方代码块围栏前导的零宽字符 `​` 去掉（这里仅是为了在 Markdown 引用中避免嵌套围栏冲突）。

## 执行步骤

按严格顺序，最后收束成一个 git commit：

1. **删除** Starter Kit 残留（上文列出的 8 项文件/目录）。
2. **拷贝** 源 Mint 文件到目标根：`docs.json`、`favicon.ico`、`logo-light.png`、`logo-dark.png`、`style.css`、`.mintignore`、`en/`、`zh-Hans/`、`images/`、`openapi/`。
3. **修改** 拷过来的 `docs.json`：删除 `navbar.links` 两条，替换为 `"links": []`。
4. **写入** 新 `README.md`（见上方草稿）。
5. **校验**（见下节）。
6. `git add -A && git commit -m "Migrate Mint content from pantum-sdk-docs"`。

## 验证方案

- **目录对照**：目标根目录 tree 与本文件"目标工程最终根目录"一致，无 Starter Kit 残留。
- **文件数**：`en/` 和 `zh-Hans/` 各含 47 个 `.mdx`，合计 94 个。
- **导航链接有效性**：遍历 `docs.json` 的 `navigation.languages[*].tabs[*].groups[*].pages[*]`，逐条 stat 确认磁盘文件存在。
- **品牌资源**：`favicon.ico`、`logo-light.png`、`logo-dark.png`、`style.css` 均在磁盘上；`docs.json` 中的引用路径与实际文件名一致。
- **（可选）`mint broken-links`**：若本地已安装 `mint` CLI，跑一次；未安装则跳过并在执行报告中注明。
- **git 状态**：`git status` 干净；`git log -1` 显示预期 commit 信息。

## 回滚方案

迁移全部落在单个 commit 中，失败或需要复盘时：

```bash
git reset --hard HEAD~1
```

## 范围之外

以下事项 **不在本次迁移范围**，如需处理应另起 spec：

- 修复 `docs.json` 中的占位值 `footer.socials.github = "https://github.com/"`。
- `apps/api`、`apps/portal-web` 的迁移或对接。
- 多语言 MDX 内容的勘误 / 翻译同步。
- Mint 站点部署（Vercel / Mintlify cloud / 自部署）配置。
- `AGENTS.md`、`CONTRIBUTING.md`、`LICENSE` 的 Pantum 化定制。
