---
title: 技术选型Vite
tags:
date created: 2024-10-08
date modified: 2024-10-08
---
根据你的项目需求和背景，以下是我为你推荐的技术选型和架构：

### 技术选型

1. **前端框架：Vue.js**
   - Vue.js 是一个易于学习和使用的前端框架，适合构建单页应用（SPA），特别是你已经有了基本的 HTML、CSS 和 JavaScript 知识。
   - 可以使用 Vite 或 Vue CLI 来初始化项目，Vite 更加现代化，构建速度快。

2. **后端框架：Express.js（Node.js）**
   - 使用 Express.js 作为 Node.js 的后端框架，能够快速构建 RESTful API，便于处理来自前端的请求。
   - Express 与 Vercel 的集成良好，方便部署。

3. **Markdown 处理：VuePress 或 VitePress**
   - 如果你的博客模块主要使用 Markdown 文档，可以使用 [VuePress](https://vuepress.vuejs.org/) 或 [VitePress](https://vitepress.vuejs.org/) 来生成静态页面。这两个工具都基于 Vue，支持 Markdown 格式，并且能够自定义主题和布局。

4. **数据迁移：Google Sheets API**
   - 利用 Google Sheets API 将你的数据从 Google Sheets 导入到你的应用中。可以使用 Node.js 的 Google API 客户端库来处理数据迁移。

5. **数据库：Firebase Firestore 或 MongoDB Atlas**
   - 如果需要简单的数据库操作，考虑使用 Firebase Firestore 或 MongoDB Atlas，这些都是云托管的解决方案，易于与 Node.js 集成，且可以直接在 Vercel 上使用。
   - Firebase Firestore 特别适合快速开发，具有实时数据库功能，适合社区模块。

6. **SEO 优化**
   - 确保你的 Vue 应用是 SEO 友好的，可以使用 [vue-meta](https://vue-meta.nuxtjs.org/) 或 Vue Router 的 meta 配置来管理页面的 meta 标签。
   - Vercel 支持静态网站生成（SSG），可以为你的应用生成静态页面，以提升 SEO。

### 项目结构建议

```plaintext
my-project/
├── api/                     # Express 后端代码
│   ├── index.js             # Express 入口文件
│   ├── routes/              # API 路由
│   ├── services/            # 数据处理逻辑
├── client/                  # Vue 前端代码
│   ├── public/              # 静态资源
│   ├── src/                 # Vue 源代码
│   │   ├── components/      # Vue 组件
│   │   ├── pages/           # 各个页面
│   │   ├── markdown/        # 存放 Markdown 文件
│   │   ├── App.vue          # 主应用组件
│   │   ├── main.js          # 应用入口
├── package.json             # 根目录 package.json
├── vercel.json              # Vercel 配置文件
```

### 开发步骤

1. **设置 Vue 项目**：使用 Vite 初始化 Vue 项目，并配置路由（使用 Vue Router）。
2. **设置 Express 后端**：创建 Express API 来处理数据请求和处理 Google Sheets 数据导入。
3. **Markdown 处理**：将 Markdown 文件放在 `src/markdown/` 中，使用 VuePress 或 VitePress 生成博客页面。
4. **数据迁移**：编写 API 调用 Google Sheets API 的逻辑，将数据迁移到你的应用。
5. **SEO 优化**：在 Vue 组件中设置 meta 标签，确保应用的可爬取性。

### 部署

- 将前后端代码一起托管到 Vercel，确保 Express API 在 `api/` 目录中，前端构建输出到 `public/` 目录。
- 使用 `vercel.json` 配置文件管理构建和路由。

### 总结

这个技术栈和架构能够满足你的需求，同时利用你现有的知识背景。Vue.js 的学习曲线相对平滑，适合构建现代化的 web 应用，而 Express.js 的使用使得后端开发简单高效。使用 Markdown 来生成博客内容使得内容管理变得容易，而与 Google Sheets 的集成则能方便地处理社区模块的数据迁移。最后，Vercel 提供的无服务器架构非常适合这个项目的需求。

# 初始化

以下是使用 Vite 构建 Vue 项目的具体步骤，包括如何初始化项目、设置 Express 后端，以及如何配置 Markdown 支持。假设你已经安装了 Node.js 和 npm。

### 第一步：初始化 Vue 项目

1. **创建项目目录**：

   ```bash
   mkdir my-project
   cd my-project
   ```

2. **使用 Vite 创建 Vue 项目**：

   使用 Vite 创建 Vue 项目，你可以选择 `vue` 模板：

   ```bash
   npm create vite@latest client --template vue
   ```

   然后进入 `client` 目录：

   ```bash
   cd client
   ```

3. **安装依赖**：

   ```bash
   npm install
   ```

4. **启动开发服务器**：

   ```bash
   npm run dev
   ```

   这时，你的 Vue 应用应该在 `http://localhost:5173` 运行（Vite 默认端口）。

### 第二步：设置 Express 后端

1. **在项目根目录创建 `api` 目录**：

   ```bash
   mkdir ../api
   cd ../api
   ```

2. **初始化 Node.js 项目**：

   ```bash
   npm init -y
   ```

3. **安装 Express**：

   ```bash
   npm install express
   ```

4. **创建 `index.js` 文件**：

   创建 `index.js` 作为 Express 入口文件，并添加基本的服务器代码：

   ```javascript
   // api/index.js
   const express = require('express');
   const cors = require('cors'); // 用于处理跨域请求
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.use(cors()); // 允许跨域请求
   app.use(express.json()); // 解析 JSON 请求体

   app.get('/api/hello', (req, res) => {
       res.json({ message: 'Hello from Express!' });
   });

   app.listen(PORT, () => {
       console.log(`Server is running on http://localhost:${PORT}`);
   });
   ```

5. **启动 Express 服务器**：

   在 `api` 目录中运行：

   ```bash
   node index.js
   ```

   你的 Express 服务器应该在 `http://localhost:3000` 上运行。

### 第三步：添加 Markdown 支持

为了使用 Markdown 文件生成博客内容，可以使用 `vite-plugin-md` 或者其他 Markdown 相关插件。这里以 `vite-plugin-md` 为例。

1. **返回到 Vue 项目目录**：

   ```bash
   cd ../client
   ```

2. **安装 `vite-plugin-md`**：

   ```bash
   npm install vite-plugin-md --save-dev
   ```

3. **修改 `vite.config.js`**：

   在 `client` 目录下的 `vite.config.js` 中添加 `vite-plugin-md` 的配置：

   ```javascript
   // client/vite.config.js
   import { defineConfig } from 'vite';
   import vue from '@vitejs/plugin-vue';
   import md from 'vite-plugin-md';

   export default defineConfig({
       plugins: [vue(), md()],
       resolve: {
           alias: {
               '@': '/src',
           },
       },
   });
   ```

4. **创建 Markdown 文件**：

   在 `src/markdown/` 目录中创建 Markdown 文件，例如 `example.md`：

   ```markdown
   # Hello Markdown

   This is an example Markdown file.
   ```

5. **在 Vue 组件中加载 Markdown**：

   在 Vue 组件中引入和渲染 Markdown 文件：

   ```vue
   <template>
       <div>
           <h1>Markdown Content</h1>
           <MarkdownContent />
       </div>
   </template>

   <script setup>
   import MarkdownContent from '@/markdown/example.md';
   </script>
   ```

### 第四步：配置 Vercel 部署

1. **在项目根目录创建 `vercel.json` 文件**：

   ```json
   {
       "builds": [
           {
               "src": "api/**/*.js",
               "use": "@vercel/node"
           },
           {
               "src": "client/package.json",
               "use": "@vercel/static-build",
               "config": {
                   "distDir": "client/dist"
               }
           }
       ],
       "routes": [
           {
               "src": "/api/(.*)",
               "dest": "api/index.js"
           },
           {
               "src": "/(.*)",
               "dest": "/client/dist/$1"
           }
       ]
   }
   ```

2. **构建 Vue 应用**：

   在 `client` 目录中运行：
   ```bash
   npm run build
   ```

   这会将构建后的文件输出到 `client/dist` 目录。

3. **部署到 Vercel**：

   - 确保你已经安装了 Vercel CLI，如果没有，可以通过以下命令安装：

     ```bash
     npm install -g vercel
     ```

   - 在项目根目录中运行：

     ```bash
     vercel
     ```

   - 按照提示完成部署。

### 总结

通过以上步骤，你已经成功初始化了一个使用 Vite 构建的 Vue 项目，并设置了 Express 后端和 Markdown 支持。现在可以在本地开发和测试，然后部署到 Vercel。这个架构能够很好地满足你的项目需求，并为未来的扩展提供了灵活性。

# 博客模块

在 Vite 5 中，有几个工具可以将 Markdown 文档生成静态网页内容。以下是一些推荐的工具和库：

1. **vite-plugin-md**:
   - 这个插件可以让你直接在 Vite 项目中使用 Markdown 文件作为 Vue 组件。你可以在 `.md` 文件中使用 Vue 语法，从而在 Markdown 中嵌入组件。
   - 使用示例：
     ```bash
     npm install vite-plugin-md
     ```

2. **vitepress**:
   - VitePress 是一个基于 Vite 的静态网站生成器，专门用于文档和博客。它支持 Markdown，并提供了主题、布局等功能，非常适合用于生成文档网站。
   - 使用示例：
     ```bash
     npm install vitepress
     ```

3. **markdown-it**:
   - `markdown-it` 是一个强大的 Markdown 解析器，可以用来将 Markdown 文档解析为 HTML。你可以在 Vite 中集成 `markdown-it` 来处理你的 Markdown 文件。
   - 使用示例：
     ```bash
     npm install markdown-it
     ```

4. **vite-plugin-pages**:
   - 这个插件允许你根据文件系统生成路由，可以与 Markdown 文件结合使用，方便地创建静态内容。
   - 使用示例：
     ```bash
     npm install vite-plugin-pages
     ```

5. **vuepress**:
   - 虽然不是直接基于 Vite 5，但 VuePress 是一个流行的静态网站生成器，支持 Markdown 文件，适合文档和博客。
   - 使用示例：
     ```bash
     npm install -g vuepress
     ```

你可以根据项目的具体需求选择合适的工具来生成静态网页内容。这些工具都能够很方便地与 Vite 进行集成，提升开发效率。
