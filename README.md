# 旅游课程设计可视化平台

一个基于 Vue 2 与高德地图 API 的旅游主题课程设计 Web 项目，用于展示中国行政区划地图浏览与基础热力数据可视化效果。

这是我web课设时所作作品

## 项目亮点
- 基于中国行政区划的地图交互展示
- 支持区域点击下钻与返回上级层级的浏览逻辑
- 支持鼠标悬停提示与区域高亮反馈
- 集成热力图效果，可作为旅游数据可视化展示基础
- 适合作为课程设计、前端作品集、答辩展示项目

## 技术栈
- Vue 2
- Vue CLI 5
- JavaScript
- Sass / SCSS
- 高德地图 JavaScript API
- AMap UI DistrictExplorer

## 项目结构
```text
traveldemo/
├─ public/
│  └─ index.html
├─ src/
│  ├─ components/
│  │  └─ componentsMap.vue
│  ├─ App.vue
│  └─ main.js
├─ package.json
├─ babel.config.js
├─ jsconfig.json
├─ vue.config.js
└─ README.md
```

## 本地运行
先安装依赖：

```bash
npm install
```

启动开发环境：

```bash
npm run serve
```

启动后在终端提示的本地地址中访问项目。

## 打包构建
```bash
npm run build
```

## 代码检查
```bash
npm run lint
```

## 地图服务说明
项目在 `public/index.html` 中通过 CDN 引入高德地图脚本和 AMap UI。

如果地图无法显示，请检查：
- 当前网络是否能访问高德地图资源
- 高德地图 Key 是否有效
- 浏览器控制台是否存在脚本加载报错

## 项目定位
这个项目适合用于：
- 旅游主题课程设计
- WebGIS / 地图可视化作业展示
- 前端开发作品集展示
- 毕业设计或课堂答辩演示基础项目

## 后续优化方向
- 接入真实旅游景点与城市数据
- 增加景点推荐、路线规划、数据看板等模块
- 补充首页视觉设计与完整业务页面
- 增加项目截图、演示视频与部署地址

## 上传 GitHub 建议
首次上传前可执行：

```bash
git init
git add .
git commit -m "feat: 初始化旅游课程设计可视化平台"
```

如果你已经创建远程仓库，再执行：

```bash
git branch -M main
git remote add origin <你的仓库地址>
git push -u origin main
```
