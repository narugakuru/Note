---
title: PyScript
tags:
  - 
date created: 2024-10-17
date modified: 2024-11-11
---
[[开发/ws接口实时传输日志]]

[Claude-3.5-sonnet new+Cline+Continue打造最强编程智能体！玩转全自动编程,发各种复杂应用，轻松修改代码、优化代码、添加注,小白也能开发各种app！#claude](https://blog.stoeng.site/20241024.html)


# PDF处理

# Pyinstaller打包

https://cloud.tencent.com/developer/article/1630758
https://blog.csdn.net/weixin_41916986/article/details/122342035

# 项目结构

```
/project_root
├── client/                    # 前端部分
│   ├── __pycache__/           # 编译缓存
│   ├── build/                 # 构建输出
│   ├── dist/                  # 项目分发文件
│   ├── node_modules/          # NPM 依赖
│   ├── public/                # 静态文件
│   ├── src/                   # 源代码
│   │   ├── assets/            # 资源文件
│   │   ├── components/        # Vue 组件
│   │   ├── composables/       # 组合式 API
│   │   ├── router/            # 路由配置
│   │   ├── styles/            # 样式文件
│   │   └── view/              # 视图组件
│   ├── .gitignore             # Git 忽略文件
│   ├── .npmrc                 # NPM 配置
│   ├── index.html             # 入口 HTML 文件
│   ├── package.json           # NPM 项目配置
│   ├── README.md              # 项目说明
│   └── vite.config.ts         # Vite 配置
│
├── resource/                  # 资源文件夹
│
└── server/                    # 后端 FastAPI 部分
│    ├── api/                   # API 路由和业务逻辑
│    ├── component/             # 组件
│    ├── schemas/               # 数据模式定义
│    ├── scripts/               # 脚本
│    ├── utils/                 # 工具类
│    ├── config.py              # 配置文件
│    └── main.py                # FastAPI 入口
│
├── app-dev.py                 # 开发环境配置
├── app.py                     # 主应用文件
├── app.spec                   # 应用规格
├── demo.py                    # 示例代码
├── monitor.py                 # 监控工具
├── requirements.txt           # Python 依赖
├── server.py                  # 服务器启动文件
└── start_app.bat             # 启动脚本

```

# 项目要求

我想做一个本地工具库，使用vue作为前端界面，pywebview将前端界面打包，后端是fastapi。前端界面比较简单，后端主要核心功能是开发过程中会用到的大量的python脚本，我希望可以很方便的传参数调用这些脚本所以写这个工具
前端网页表单属性以json格式存放在文件里，以script_name作为索引，获取表单参数
现在的问题：设计组件，通过传入参数script_name索引的方式来构建form，你有哪些实现方案
```
{
    "script_list": {
        "copy_list": {
            "form_fields": [
                {
                    "label": "源文件夹路径",
                    "model": "origin_path",
                    "type": "input",
                    "placeholder": "请输入源文件夹路径"
                },
                {
                    "label": "目标文件夹路径",
                    "model": "copy_path",
                    "type": "input",
                    "placeholder": "请输入目标文件夹路径"
                },
                {
                    "label": "文件列表",
                    "model": "file_list",
                    "type": "textarea",
                    "placeholder": "请输入文件列表，每行一个文件路径",
                    "rows": 10
                }
            ]
        }
    }
}
```

![](开发/attachments/Pasted%20image%2020241104221314.png)
```
======== 指定获取脚本logger名称：copy_list =========  
2024-11-04 22:15:35,395 - copy_list - INFO - ======== run_script : copy_list ==========  
========= execute_script: copy_list==========  
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
| 参数名 | 值  
|  
+=======+===============================================================================================================================================================================================+  
| 参数 0 | copy_list  
|  
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
| 参数 1 | {'origin_path': 'E:/WorkSpace/WebKaisyu/ssl-htdocs-local', 'copy_path': 'E:/WorkSpace/WebKaisyu/html_11', 'file_list': ['effort/content.html', 'effort/content.html', 'effort/content.html']} |  
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
========== run! ： copy_list ===========  
2024-11-04 22:15:35,396 - copy_list - INFO - ====== copy_files_with_structure =======  
copy folder from E:/WorkSpace/WebKaisyu/ssl-htdocs-local to E:/WorkSpace/WebKaisyu/html_11  
=======websocket开始发送消息 =====  
DEBUG: > TEXT '2024-11-04 22:15:35,395 - INFO - ======== run_s... : copy_list ==========' [75 bytes]  
=======websocket开始发送消息 =====  
DEBUG: > TEXT '2024-11-04 22:15:35,396 - INFO - ====== copy_files_with_structure =======' [73 bytes]  
2024-11-04 22:15:35,401 - copy_list - INFO -  
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_11\effort/content.html  
=======websocket开始发送消息 =====  
DEBUG: > TEXT '2024-11-04 22:15:35,401 - INFO - \nCopied: E:/W...11\\effort/content.html' [155 bytes]  
2024-11-04 22:15:35,404 - copy_list - INFO -  
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_11\effort/content.html  
=======websocket开始发送消息 =====  
DEBUG: > TEXT '2024-11-04 22:15:35,404 - INFO - \nCopied: E:/W...11\\effort/content.html' [155 bytes]  
2024-11-04 22:15:35,406 - copy_list - INFO -  
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_11\effort/content.html  
2024-11-04 22:15:35,407 - copy_list - INFO - Script completed: copy_list  
2024-11-04 22:15:35,407 - copy_list - INFO - Result: True  
2024-11-04 22:15:35,407 - copy_list - INFO -  
  
================================================================================
```
![](开发/attachments/Pasted%20image%2020241104221318.png)
```
======== 指定获取脚本logger名称：copy_list =========  
2024-11-04 22:15:18,119 - copy_list - INFO - Setting up stream logger: copy_list  
====== 初始化日志Handler: copy_list =======  
2024-11-04 22:15:18,119 - copy_list - INFO - ======== run_script : copy_list ==========  
========= execute_script: copy_list==========  
+-------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
| 参数名 | 值 |  
+=======+====================================================================================================================================================================+  
| 参数 0 | copy_list |  
+-------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
| 参数 1 | {'origin_path': 'E:/WorkSpace/WebKaisyu/ssl-htdocs-local', 'copy_path': 'E:/WorkSpace/WebKaisyu/html_11', 'file_list': 'effort/content.html\neffort/content.html'} |  
+-------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+  
========== run! ： copy_list ===========  
2024-11-04 22:15:18,130 - copy_list - INFO - ====== copy_files_with_structure =======  
2024-11-04 22:15:18,130 - copy_list - INFO - ====== file_list is normalizing! ======  
2024-11-04 22:15:18,130 - copy_list - ERROR - Error in run: object list can't be used in 'await' expression  
2024-11-04 22:15:18,130 - copy_list - INFO - Script completed: copy_list  
2024-11-04 22:15:18,130 - copy_list - INFO - Result: object list can't be used in 'await' expression  
2024-11-04 22:15:18,130 - copy_list - INFO -  
  
================================================================================  
  
  
INFO: 127.0.0.1:16333 - "POST /api/script/copy_list HTTP/1.1" 200 OK

```

# 路由bug

一个pywebview+vue+fastapi的项目天坑bug
vue前端自带路由，把vue生成的静态html文件放到pywebview里还是只能通过fastapi获取静态文件
这要解决vue路由,fastapi后端api,静态资源api三者的冲突关系

fastapi把静态文件挂载为/,那全部请求都会被错误当作对静态文件的请求，无法访问后端api，这可能是fastapi的bug？
INFO: 127.0.0.1:48963 - "GET /static/vite.svg HTTP/1.1" 404 Not Found
INFO: 127.0.0.1:48963 - "POST /api/file/copy_list HTTP/1.1" 405 Method Not Allowed

fastapi把静态文件挂载为static，vue设置base=/static，那pywebview无法加载前端界面，这可能是vue前端的路由问题？
INFO: 127.0.0.1:46346 - "GET /index HTTP/1.1" 200 OK
INFO: 127.0.0.1:46346 - "GET /static/assets/index-B96MkbnA.js HTTP/1.1" 404 Not Found
INFO: 127.0.0.1:46349 - "GET /static/assets/index-BMILcQzm.css HTTP/1.1" 404 Not Found
INFO: 127.0.0.1:46349 - "GET /static/favicon.svg HTTP/1.1" 404 Not Found
INFO: 127.0.0.1:46349 - "GET /static/assets/index-BMILcQzm.css HTTP/1.1" 404 Not Found

fastapi本身的问题导致无法挂载html目录为/
所以只能从vue入手，vue设置base=/static，fastapi挂载html为/static
重点操作来了，把vue生成的dist静态文件，放到/dist/static，index在dist下不要动
```
# Vue 前端路由处理，调整路径, web_path是client/dist目录
@app.get("/{full_path:path}")
async def serve_frontend(full_path: str):
    return FileResponse(os.path.join(web_path, 'index.html'))
# 静态重定向路由，调整路径
@app.get("/index")
async def index():
    return FileResponse(os.path.join(web_path,"index.html"))
```

# 命名规范

函数和变量：snake_case
类：PascalCase
常量：ALL_CAPS
私有变量和函数： _single_leading_underscore
模块和包：snake_case但包名尽量避免下划线

# 项目结构

```
/my_app
│
├── /app/                   # FastAPI 代码
│   └── ...
├── /dist/                  # 打包后的输出目录
│   └── /my_app/            # 可执行文件及资源
│       ├── my_app.exe      # 可执行文件
│       ├── /static/        # 静态文件
│       └── /db/            # 数据库文件
├── /static/                # 原始静态文件
│   └── ...
├── /db/                    # 数据库文件
│   └── database.db
├── run.py                  # 主启动文件
├── run.spec                # PyInstaller spec 文件
└── requirements.txt        # Python依赖
```

要将 **FastAPI + PyWebView** 项目打包为独立的可执行文件，通常使用 **PyInstaller**。这个工具可以将 Python 脚本和依赖一起打包成一个可分发的桌面应用。下面是具体步骤：

### 1. 安装 PyInstaller

首先，在你的项目虚拟环境中安装 PyInstaller：

```bash
pip install pyinstaller
```

### 2. 项目准备

确保你的项目结构合理，特别是静态文件、数据库、配置文件等这些需要在打包后的应用中找到的资源。在这种结构中，你的静态文件位于 `static` 目录中，FastAPI 的 Python 代码在 `app` 目录中。

### 3. 创建 PyInstaller 配置文件

PyInstaller 默认会打包所有的 Python 文件，但有时你需要手动指定资源文件，比如静态文件夹 `static` 和 SQLite 数据库等。

1. **创建 `spec` 文件**：运行以下命令来生成 `run.spec` 文件：

    ```bash
    pyinstaller --name my_app --windowed --noconfirm --add-data "static:static" run.py
    ```

   解释：
   - `--name my_app`：设置生成的可执行文件名称为 `my_app`。
   - `--windowed`：不显示控制台窗口。
   - `--noconfirm`：自动覆盖以前的构建结果。
   - `--add-data "static:static"`：将 `static` 目录添加到可执行文件中。格式为 `source:destination`。
   - `run.py`：主脚本文件。

2. **编辑 `run.spec` 文件**：你可以对生成的 `.spec` 文件进行一些修改，确保包括所有需要的文件：

    ```python
    # -*mode: python ; coding: utf-8 -*block_cipher = None

    a = Analysis(
        ['run.py'],
        pathex=['./'],
        binaries=[],
        datas=[
            ('static', 'static'),  # 将静态文件包含在打包文件中
            ('db/database.db', 'db'),  # 如果有SQLite数据库
        ],
        hiddenimports=[],
        hookspath=[],
        runtime_hooks=[],
        excludes=[],
        win_no_prefer_redirects=False,
        win_private_assemblies=False,
        cipher=block_cipher,
        noarchive=False,
    )
    pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

    exe = EXE(
        pyz,
        a.scripts,
        [],
        exclude_binaries=True,
        name='my_app',
        debug=False,
        bootloader_ignore_signals=False,
        strip=False,
        upx=True,
        console=False,  # 不显示控制台窗口
        icon=None,
    )

    coll = COLLECT(
        exe,
        a.binaries,
        a.zipfiles,
        a.datas,
        strip=False,
        upx=True,
        upx_exclude=[],
        name='my_app',
    )
    ```

   这个 `spec` 文件确保将 `static` 文件夹和 SQLite 数据库一起打包。

### 4. 打包项目

完成配置后，运行以下命令打包你的项目：

```bash
pyinstaller run.spec
```

这将生成一个 `dist` 目录，其中包含 `my_app` 可执行文件及所有打包的资源。

### 5. 调整打包后的资源路径

打包时，资源文件（如静态文件和数据库）可能会移动到不同的路径。在运行时，你需要确保代码能够正确地找到这些文件。可以通过 PyInstaller 提供的 `sys._MEIPASS` 变量来处理这种情况：

```python
import os
import sys

def resource_path(relative_path):
    """ 获取打包后的资源文件路径 """
    if hasattr(sys, '_MEIPASS'):
        return os.path.join(sys._MEIPASS, relative_path)
    return os.path.join(os.path.abspath("."), relative_path)

# 用法
static_dir = resource_path("static")
db_file = resource_path("db/database.db")
```

在你的 `FastAPI` 应用中，可以使用 `resource_path()` 函数定位静态文件夹或数据库文件。

### 6. 运行打包好的程序

打包完成后，你可以在 `dist/my_app` 文件夹中找到可执行文件，双击即可运行该桌面应用。

### 项目结构调整

打包完成后的项目结构如下：

```
/my_app
│
├── /app/                   # FastAPI 代码
│   └── ...
├── /dist/                  # 打包后的输出目录
│   └── /my_app/            # 可执行文件及资源
│       ├── my_app.exe      # 可执行文件
│       ├── /static/        # 静态文件
│       └── /db/            # 数据库文件
├── /static/                # 原始静态文件
│   └── ...
├── /db/                    # 数据库文件
│   └── database.db
├── run.py                  # 主启动文件
├── run.spec                # PyInstaller spec 文件
└── requirements.txt        # Python依赖
```

### 总结

通过使用 **PyInstaller**，你可以将 **FastAPI + PyWebView** 的项目打包为一个桌面应用。关键点在于：
- 确保静态文件、数据库等资源被正确包含。
- 使用 `sys._MEIPASS` 确保打包后的文件路径能被正确解析。

### FastAPI + PyWebView 项目的结构

为了便于组织代码，你可以采用如下目录结构：

```
/my_app
│
├── /static/               # 前端静态文件
│   ├── index.html          # 主HTML文件
│   ├── /css/               # 样式文件
│   ├── /js/                # JavaScript文件
│   └── /assets/            # 图片等资源
│
├── /app/                   # 后端FastAPI逻辑
│   ├── __init__.py         # 初始化文件
│   ├── main.py             # FastAPI应用主文件
│   └── crud.py             # 数据库操作（CRUD）
│
├── /db/                    # SQLite数据库相关
│   └── database.db         # SQLite数据库文件
│
├── run.py                  # 入口，启动FastAPI和PyWebView
├── requirements.txt        # Python依赖包
└── README.md               # 项目说明
```

# 启动

### 运行步骤

1. **依赖安装**：
   在项目根目录下创建 `requirements.txt` 文件，包含如下依赖：
   ```txt
   fastapi
   uvicorn
   pywebview
   sqlalchemy
   pydantic
   ```

   然后通过以下命令安装依赖：
   ```bash
   pip install -r requirements.txt
   ```

2. **启动应用**：
   运行 `python run.py`。
   这将同时启动 FastAPI 后端服务器和 PyWebView 的桌面应用窗口。PyWebView 的窗口将加载 FastAPI 提供的 `index.html` 页面，并且可以通过前端调用 FastAPI 的 API。

### 说明

- **静态文件目录**：所有的HTML、CSS、JS等静态资源都放在 `static` 文件夹中。
- **FastAPI** 负责处理API请求，并通过根路径返回 `index.html` 页面。
- **PyWebView** 作为桌面应用的容器，展示FastAPI生成的前端页面。

这种架构允许你将前端和后端分离，保持灵活性。你可以逐步将前端代码替换为Vue或者其他框架，同时保持与FastAPI的高效交互。

为你的项目，我建议采用以下架构和工具组合来满足自动生成CRUD API、异步处理、日志记录等需求：

### 1. **后端设计：**

- **使用FastAPI**：
  FastAPI是一个非常适合自动生成CRUD API的框架。它支持自动生成文档、异步处理（基于`async`）、和SQLite数据库集成，且性能优异。

  - **SQLite集成**：使用`SQLAlchemy`和`pydantic`来处理数据库模型和验证。
  - **自动生成CRUD API**：可以使用`SQLModel`（基于SQLAlchemy），它可以自动为每个数据库表生成标准的CRUD操作。
  - **异步处理**：FastAPI天然支持异步处理，确保后端在执行长时间任务时不阻塞主线程。

  **示例代码**：
  ```python
  from fastapi import FastAPI, Depends, HTTPException
  from sqlalchemy.orm import Session
  from sqlmodel import SQLModel, Field, create_engine, Session

  app = FastAPI()

  class Item(SQLModel, table=True):
      id: int = Field(default=None, primary_key=True)
      name: str
      description: str

  sqlite_url = "sqlite:///./test.db"
  engine = create_engine(sqlite_url, echo=True)

  @app.post("/items/", response_model=Item)
  async def create_item(item: Item, session: Session = Depends()):
      session.add(item)
      session.commit()
      return item

  @app.get("/items/{item_id}", response_model=Item)
  async def read_item(item_id: int, session: Session = Depends()):
      item = session.get(Item, item_id)
      if not item:
          raise HTTPException(status_code=404, detail="Item not found")
      return item
  ```

- **日志记录**：
  - 使用Python内置的`logging`模块来记录API请求、数据库操作及错误信息。
  - 可以创建一个中间件来捕捉所有API调用并记录日志。

  **示例日志代码**：
  ```python
  import logging

  logging.basicConfig(level=logging.INFO, filename='app.log', 
                      format='%(asctime)s - %(message)s')

  @app.middleware("http")
  async def log_requests(request, call_next):
      logger = logging.getLogger("app")
      logger.info(f"Received request: {request.method} {request.url}")
      response = await call_next(request)
      logger.info(f"Completed request: {response.status_code}")
      return response
  ```

### 2. **前端设计：**

- **初期使用原生HTML和Element**：
  你可以简单地用HTML和Element来构建表单和交互界面，调用后端生成的API完成CRUD操作。Element UI的组件库可以提供不错的布局和表单控件。

- **逐渐迁移到Vue**：
  当你逐渐熟悉Vue后，可以通过Vue的组件化和双向绑定特性优化界面交互。你可以用`axios`来简化与FastAPI的通信。

### 3. **异步处理：**

- **FastAPI异步功能**：
  FastAPI原生支持异步任务处理，可以使用`async`函数来处理数据库和外部资源的调用。同时，如果需要更复杂的异步任务管理，可以引入`Celery`，使用`Redis`作为任务队列。

  **异步任务示例**：
  ```python
  @app.post("/items/async/")
  async def create_item_async(item: Item):
      # 异步任务处理
      await asyncio.sleep(2)  # 模拟耗时操作
      return {"message": "Task completed"}
  ```

### 4. **其他特性**

- **自动生成API文档**：FastAPI会自动生成Swagger UI文档，可以访问`/docs`查看和测试API。
- **数据库迁移工具**：你可以使用`Alembic`来处理SQLite数据库的迁移和版本控制。

### 总结

这个方案非常适合你的需求：
- FastAPI可以自动为SQLite表生成CRUD API。
- 支持异步操作，适合本地工具的特性。
- 支持从原生HTML逐步迁移到Vue，确保项目灵活可扩展。
- 完整的日志功能保证操作可追溯。

你可以从简单的原型开始，然后逐步引入更多功能如异步任务和Vue前端优化。

# 项目整合

在构建一个结合 **Vue**、**FastAPI** 和 **PyWebView** 的项目时，项目结构的设计需要同时考虑 **前端** (Vue)、**后端 API** (FastAPI)，以及 **本地桌面应用** (PyWebView) 的交互。下面是一个推荐的项目结构以及如何管理各个框架的调用。

### 1. 项目结构

```bash
/fastapi
├── server/                    # 后端 FastAPI 部分
│   ├── main.py                 # FastAPI 入口
│   ├── api/                    # API 路由和业务逻辑
│   ├── models/                 # 数据库模型
│   ├── db.py                   # 数据库连接配置
│   └── utils/                  # 工具类（处理异步操作等）
├── frontend/                   # 前端 Vue 部分
│   ├── public/                 # 静态文件
│   ├── src/
│   │   ├── components/         # Vue 组件
│   │   ├── views/              # Vue 页面
│   │   ├── router.js           # Vue Router 路由
│   │   └── main.js             # Vue 入口文件
│   └── package.json            # Vue 项目配置
├── webview/                    # PyWebView 部分
│   ├── webview_app.py          # PyWebView 入口
│   └── utils.py                # PyWebView 相关的工具函数
├── app.py                      # 启动整个项目的脚本
├── requirements.txt            # Python 依赖
└── package.json                # Vue 项目配置 (前端构建相关)
```

### 2. 各框架的调用与整合

#### (1) **FastAPI 作为后端 API 提供者**

- **FastAPI** 用于处理后端业务逻辑和 API 请求，比如 CRUD 操作，异步任务处理等。
- **SQLite** 可以作为后端的数据库，用于存储用户的数据，FastAPI 通过数据库 ORM 操作这些数据。
- 将 FastAPI 设置为 PyWebView 的本地服务器，供前端 Vue 使用。

#### 示例：FastAPI 的基本 `main.py`

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from .api import routes  # 引入后端 API 路由

app = FastAPI()

# 配置 CORS，允许前端 Vue 访问 API
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# 包含后端 API 路由
app.include_router(routes.router)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)
```

#### (2) **Vue 作为前端用户界面**

- **Vue.js** 负责构建用户界面，通过 Axios 或 Fetch 与 FastAPI 后端交互。
- 使用 **Vue Router** 进行页面的导航和路由管理。
- Vue 的构建结果（`dist/`）将作为静态文件供 PyWebView 加载。

#### 示例：Vue 中调用 FastAPI

```javascript
// 在 Vue 组件中通过 Axios 调用 FastAPI 后端的 API
<template>
  <div>
    <h1>{{ message }}</h1>
    <button @click="fetchMessage">Fetch Data</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      message: 'Hello, Vue!'
    };
  },
  methods: {
    fetchMessage() {
      axios.get('http://127.0.0.1:8000/api/data')  // 请求 FastAPI 提供的 API
        .then(response => {
          this.message = response.data.message;
        })
        .catch(error => {
          console.error("Error fetching data:", error);
        });
    }
  }
}
</script>
```

#### (3) **PyWebView 作为本地桌面应用**

- **PyWebView** 负责创建一个本地桌面应用，并加载 Vue 的前端页面。
- PyWebView 可以通过本地 HTTP 服务器访问 FastAPI 和前端的 Vue 页面。
- PyWebView 还可以通过其内置的功能直接调用 Python 函数，比如异步任务或本地文件操作。

#### 示例：`webview_app.py`

```python
import webview
import threading
import uvicorn
from backend.main import app  # 引入 FastAPI 应用

# 启动 FastAPI 作为本地服务器
def start_fastapi():
    uvicorn.run(app, host="127.0.0.1", port=8000)

# 启动 PyWebView 的桌面窗口
def start_webview():
    window = webview.create_window("My Vue App", "http://127.0.0.1:8000")
    webview.start()

if __name__ == "__main__":
    # 并行启动 FastAPI 和 PyWebView
    api_thread = threading.Thread(target=start_fastapi)
    api_thread.start()

    start_webview()
```

### 3. 使用步骤

1. **启动 FastAPI 服务器**
   `app.py` 中的 `main` 启动脚本会并行启动 **FastAPI** 后端服务器和 **PyWebView** 的桌面应用窗口。
   - PyWebView 加载的 URL 是 `http://127.0.0.1:8000`，对应的是 FastAPI 提供的服务。

2. **构建 Vue 应用**
   在 `frontend/` 目录下，运行 Vue 项目，生成打包后的静态文件。
   ```bash
   cd frontend
   npm run build
   ```

3. **加载 Vue 应用**
   PyWebView 会从本地加载打包好的 Vue 应用文件。

4. **集成 API 和前端**
   前端 Vue 通过 API 调用 FastAPI 提供的数据，所有交互通过 FastAPI 来处理。

### 4. 打包项目（使用 PyInstaller）

为了将项目打包成一个独立的可执行文件，可以使用 **PyInstaller** 将 Python 代码（包括 FastAPI 和 PyWebView）打包：

```bash
pip install pyinstaller

# 使用 PyInstaller 打包
pyinstaller --noconfirm --onedir --windowed app.py
```

注意，打包时需要保证 Python 依赖已安装，包括 **FastAPI**、**PyWebView** 以及 **Uvicorn** 等。同时，打包的 Vue 前端应该已经构建完成。

### 总结

- **Vue** 负责前端界面，Vue Router 管理路由，Axios 处理与后端的交互。
- **FastAPI** 提供 API 和业务逻辑处理，支持异步操作，并作为本地 HTTP 服务器。
- **PyWebView** 提供桌面环境，运行 Vue 前端应用，并通过 FastAPI 实现数据交互。

这种结构下，Vue、FastAPI 和 PyWebView 的功能能有效配合，为本地工具提供用户友好且高效的界面和后端服务。
