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

```bash
npm i -g mint
mint dev
```

默认监听 `http://localhost:3000`。

## 校验

```bash
mint broken-links
```

## 多语言组织约定

- 中文为默认语言，英文镜像文档保存在 `en/` 下，与中文目录层级一致。
- 跨语言共享的导航入口通过 `docs.json` 的 `navigation.languages` 分别配置。
