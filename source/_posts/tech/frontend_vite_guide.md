---
title: Web Frontend
date: 2022-12-31 00:00:00
tags: web
categories: tech
---

---

# 🚀 前端项目构建与部署教程（Vue & React）—— 适用于 Vite 构建工具

> 本教程面向在 WSL 或 Linux 系统中开发前端项目（Vue 或 React）的用户，使用现代构建工具 Vite。适用于已有项目或从零开始创建项目的两种场景。

---

## 📦 一、已有项目：如何安装并运行

### ✅ 步骤 1：确认目录结构

确保你的目录中包含：

- `package.json`（依赖说明）
- `vite.config.ts`（Vite 配置）
- `src/`（源码目录）

### ✅ 步骤 2：安装依赖

在终端中进入项目目录，执行：

```bash
npm install
# 或者
yarn install
```

这将安装 `dependencies` 和 `devDependencies` 到 `node_modules/` 中。

### ✅ 步骤 3：本地开发模式运行

```bash
npm run dev
# 或
yarn dev
```

成功后，控制台会显示：

```
Local:   http://localhost:5173
```

---

## 🧱 二、从零开始新建 Vue 或 React 项目

推荐使用 Vite 官方脚手架：

### ✅ 安装方式：

无需全局安装，只需执行：

```bash
npm create vite@latest
# 或
yarn create vite
```

### ✅ 选择模板时：

- Vue 项目 → 选择 `vue` 或 `vue-ts`
- React 项目 → 选择 `react` 或 `react-ts`

### ✅ 项目创建后：

进入项目目录，运行：

```bash
npm install
npm run dev
```

---

## ⚙️ 三、常用命令说明

| 命令 | 说明 |
|------|------|
| `npm install` / `yarn install` | 安装项目依赖 |
| `npm run dev` / `yarn dev` | 启动开发服务器 |
| `npm run build` / `yarn build` | 构建静态生产文件 |
| `npm run preview` | 本地预览构建结果 |

---

## 🔁 四、Vite 配置建议（vite.config.ts）

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
  plugins: [vue()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: path => path.replace(/^\/api/, '')
      }
    }
  }
});
```

---

## 🧠 五、注意事项

- 默认端口是 `5173`，如被占用可修改 `vite.config.ts`
- Vue 3 推荐使用 `script setup` + `Pinia`
- React 推荐使用 `vite` 替代 `create-react-app`，启动速度快很多
- 如果连接后端，建议设置 Vite 代理（如 `/api` 转发）

---

## 📌 六、构建输出路径

运行 `npm run build` 后，生成的静态文件默认位于：

```bash
dist/
```

可以部署至：

- GitHub Pages
- Nginx 服务器
- Vercel / Netlify / Render 等前端托管平台

---

> 编写人：ChatGPT  
> 日期：2024  
> 本文可用于笔记、备忘、或前端项目团队 Wiki
