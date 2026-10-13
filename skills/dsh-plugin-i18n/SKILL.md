---
name: dsh-plugin-i18n
description: 给 DSH 插件的客户端 UI 做中英文国际化时使用：locale 命名空间、slot 声明、t() 取词与切语言即时生效。
whenToUse: 插件 UI 需要跟随 DSH 语言偏好（zh/en）切换、或补翻译时。
---

# DSH 插件客户端国际化（依据 @deepseek-ai/dsh-client-locale 实现与实战验证）

## 机制

- 每个插件注册自己的命名空间字典：`ctx.locale.register('my.ns', { zh: {...}, en: {...} })`
- 在 slot 注册里声明 `locale: 'my.ns'` → 组件 props 自动获得 `t(key, params)`
- `t` 每次调用实时读当前语言（切语言即时生效，无需重启）
- 未命中回退顺序：当前语言 → zh → 返回 key 本身

## 步骤

1. client 的 `inject` 数组加 `'locale'`
2. 定义命名空间与双字典（key 用点分命名，如 `'mic.err.network'`）：

```ts
const NS = 'my.ns'
const zh = { 'btn.title': '开始', 'err.generic': '出错：{code}' }
const en = { 'btn.title': 'Start', 'err.generic': 'Error: {code}' }
```

3. apply 里注册（放 `ctx.effect`，随插件生命周期释放）：

```ts
ctx.effect(() => ctx.locale.register(NS, { zh, en }), 'my-plugin: dictionaries')
```

4. slot 声明 `locale: NS`；组件内 `t('btn.title')`、`t('err.generic', { code })`（`{param}` 插值）
5. 设置页标签用惰性 label：`label: () => t('settings.tabLabel')` —— 每次渲染求值，切语言即更新

## 验证

- 切换入口：设置 → 语言（Language）选 zh/en，UI 文案即时切换
- 自测里 mock `ctx.locale.register/bind`，断言字典注册与 slot 的 `locale` 声明
- 双语 README 惯例：`README.md`（英文）+ `README.zh.md`（中文），开头互链 `[中文](README.zh.md)` / `[English](README.md)`
- 注意：经 HTTP API 写中文（如 GitHub description）避免 PowerShell 直接传中文 body，见 dsh-plugin-publish
