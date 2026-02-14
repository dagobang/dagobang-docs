# 开发与构建

本页给开发者一个最短的构建与调试路径。

## 1. 扩展（dagobang-extention）

进入目录：

- `cd dagobang-extention`

安装依赖：

- `npm install`

开发模式（热更新，供本地调试使用）：

- `npm run dev`

构建 MV3 产物（用于加载到浏览器）：

- `npm run build`
- 产物目录：`.output/chrome-mv3/`

打包 zip（可选）：

- `npm run zip`

## 2. 浏览器加载方式（Chrome/Edge）

- 打开扩展管理页（Chrome：`chrome://extensions/`；Edge：`edge://extensions/`）
- 开启“开发者模式”
- 点击“加载已解压的扩展程序”，选择 `.output/chrome-mv3/`

## 3. 常见调试点

- Popup：扩展图标打开
- Content UI（悬浮窗）：在支持的网站页面上查看
- Background：在扩展管理页打开 service worker 的调试面板

