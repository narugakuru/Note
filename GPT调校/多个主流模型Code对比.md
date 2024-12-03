---
title: 多个主流模型Code对比
tags:
date created: 2024-09-21
date modified: 2024-10-22
---

# 简单的CSS图标问题

除了半价4o其他模型都能给出比较合适的方案
半价4o偶尔能给出合适的方案
```
我希望实现一个网页效果，已有一个div标签class=card_title，它是一个包含了文本的矩形  
我要导入一个20*20px的图标，使它展示在card_title矩形的最右侧靠内框区域  
由于要修改的地方很多，最好不要对html有改动，设置一个通用的css样式修改所有card_title，你有哪些合适的实现方案？
```

### 4o半价

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%202%20.jpg)

### 4o稳定版

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%201%20.jpg)

### qwen-max

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%203%20.jpg)

### qwen-plus

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%204%20.jpg)

### qwen-coder-turbo

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%205%20.jpg)

### qwen2.5-72b-instruct

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21%20%206%20.jpg)

### sonnet

![](https://cdn.jsdelivr.net/gh/narugakuru/images@master/img/LobeChat_%E9%9A%8F%E4%BE%BF%E8%81%8A%E8%81%8A_2024-09-21.jpg)

### api客户端

请你设计一个语言大模型的API集成客户端，聊聊设计思路和技术选型
功能需求：
很好的集成openai类的api接口
统计API调用情况和token消耗
可以调用RAG功能来索引知识库
拥有良好的对PDF文件的解析能力
可以使用谷歌账号进行SSO登录
部署需求：
使用nodejs作为后端，可以部署在vercel这类平台

# 设计一个URL短链接系统

功能需求：设计一个URL短链接系统，用户可以输入一个长URL，系统生成一个唯一的短URL。还需要提供将短URL还原为长URL的功能。
编程语言不限，选择你觉得最合适的语言和技术框架

## 4o-2411


设计一个简单的 URL 短链接系统涉及多个步骤，例如 URL 存储、唯一短码的生成以及短链接的解码映射。以下是一个基于 Python 使用 Flask 框架的实现方案，搭配 Redis（作为存储）或内存字典用于存储映射关系。

### 技术选型
1. **编程语言**：Python
   - 理由：开发高效、社区支持强大，适合实现此类快速开发需求。
2. **技术框架**：Flask
   - 理由：轻量级、简单易用的 Web 框架，适合小型工具系统。
3. **数据库**：Redis 或内存字典
   - Redis：简单可靠的 key-value 存储，性能高，支持过期时间等特性。

### 功能设计
- **生成短链接**：
  - 用户提交一个长 URL 后，系统生成一个短码（自动避免重复）并存储映射关系。
  - 短码可以通过哈希算法或计数器生成。
- **还原长链接**：
  - 根据用户提交的短 URL 短码，通过映射找到对应的长链接并返回。
- **高效存储和查询**：
  - 对于查找过程，使用 Redis 或内存字典来高效实现短码与长链接的映射。

---

### 代码实现

```python
import string
import random
from flask import Flask, request, jsonify

# 初始化 Flask 应用
app = Flask(__name__)

# 初始化存储
url_mapping = {}  # 用作短码与长URL的存储映射
base_url = "http://short.ly/"
short_code_length = 6

# 工具函数：随机生成短码
def generate_short_code(length=short_code_length):
    """生成一个指定长度的随机短码"""
    characters = string.ascii_letters + string.digits
    return ''.join(random.choices(characters, k=length))


# 用户接口：生成短链接
@app.route('/shorten', methods=['POST'])
def shorten_url():
    """接收长URL，生成短URL"""
    long_url = request.json.get('long_url')  # 从POST请求读入数据
    if not long_url:
        return jsonify({"error": "Missing 'long_url' parameter"}), 400
    
    # 检查长URL是否已经存在，直接返回短码
    for short_code, url in url_mapping.items():
        if long_url == url:
            return jsonify({"short_url": base_url + short_code})
    
    # 如果不存在，生成新短码
    short_code = generate_short_code()
    
    # 短码避免冲突
    while short_code in url_mapping:
        short_code = generate_short_code()
    
    # 保存映射关系
    url_mapping[short_code] = long_url
    short_url = base_url + short_code
    return jsonify({"short_url": short_url})


# 用户接口：还原长链接
@app.route('/expand/<short_code>', methods=['GET'])
def expand_url(short_code):
    """根据短码还原长URL"""
    long_url = url_mapping.get(short_code)
    if not long_url:
        return jsonify({"error": "Short URL not found"}), 404
    
    return jsonify({"long_url": long_url})


# 启动服务器
if __name__ == '__main__':
    app.run(debug=True)
```

---

### 测试步骤
1. 运行程序。
2. 使用 Postman 或 cURL 测试以下功能：
   - 生成短链接：
     ```bash
     curl -X POST -H "Content-Type: application/json" -d '{"long_url": "http://www.example.com"}' http://127.0.0.1:5000/shorten
     ```
   - 还原长链接：
     假设短链接为 `http://short.ly/abc123`，此时查询短码 `abc123`：
     ```bash
     curl http://127.0.0.1:5000/expand/abc123
     ```

---

### 优化与扩展
1. **自定义短码**：
   - 允许用户手动指定短码。
2. **重复检测**：
   - 对同一个长 URL，生成相同的短码映射。
3. **数据库使用**：
   - 将存储从 `url_mapping` 替换为 Redis 或其他数据库，支持分布式部署。
4. **统计功能**：
   - 添加短链接访问次数统计功能以追踪使用情况。
5. **安全性**：
   - 验证输入的 URL 格式，防止无效请求。


## 4o-2408

设计一个URL短链接系统可以使用Python和Flask框架，因为它简单易用，且有丰富的扩展库支持。下面概述一下系统的设计和实现：

### 技术栈

- **编程语言**: Python
- **框架**: Flask
- **数据库**: SQLite（适合轻量级应用）

### 系统设计

1. **URL Shortening**:
   - 接收用户输入的长URL。
   - 生成唯一的短码。
   - 保存长URL和短码的映射关系到数据库。

2. **URL Redirection**:
   - 接收短码请求。
   - 查询数据库获取对应的长URL。
   - 重定向到长URL。

3. **还原URL**:
   - 提供接口输入短码，返回长URL。

### 代码实现

```python
from flask import Flask, request, redirect, jsonify
from flask_sqlalchemy import SQLAlchemy
import string
import random

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///urls.db'
db = SQLAlchemy(app)

class URLMap(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    long_url = db.Column(db.String(2048), nullable=False)
    short_code = db.Column(db.String(6), unique=True, nullable=False)

def generate_short_code(length=6):
    characters = string.ascii_letters + string.digits
    return ''.join(random.choice(characters) for _ in range(length))

@app.route('/shorten', methods=['POST'])
def shorten_url():
    long_url = request.json.get('long_url')
    short_code = generate_short_code()
    url_map = URLMap(long_url=long_url, short_code=short_code)
    db.session.add(url_map)
    db.session.commit()
    return jsonify({'short_url': request.host_url + short_code})

@app.route('/<short_code>', methods=['GET'])
def redirect_url(short_code):
    url_map = URLMap.query.filter_by(short_code=short_code).first()
    if url_map:
        return redirect(url_map.long_url)
    return jsonify({'error': 'URL not found'}), 404

@app.route('/expand', methods=['POST'])
def expand_url():
    short_code = request.json.get('short_code')
    url_map = URLMap.query.filter_by(short_code=short_code).first()
    if url_map:
        return jsonify({'long_url': url_map.long_url})
    return jsonify({'error': 'URL not found'}), 404

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True)
```

### 详细说明

1. **数据库设置**: 使用SQLite数据库，通过Flask-SQLAlchemy进行管理，存储长URL和短码映射关系。
2. **短码生成**: 使用`string.ascii_letters`和`string.digits`生成随机短码，长度默认为6。
3. **API设计**:
   - `POST /shorten`: 接收长URL，返回短URL。
   - `GET /<short_code>`: 根据短码重定向到长URL。
   - `POST /expand`: 接收短码，返回对应的长URL。

4. **错误处理**: 简单的错误处理，如果找不到对应的短码或长URL，返回404错误。

这个设计使用Flask的简洁性和Python的易用性，适合初学者快速度原型开发和测试。

## Qwen-Coder

设计一个URL短链接系统是一个常见的需求，涉及到数据存储、唯一性保证、安全性和性能优化等多个方面。下面我将详细介绍如何设计这样一个系统，包括技术选型、功能实现和一些最佳实践。

### 技术选型

1. **后端语言**: Python（使用Flask或FastAPI）
2. **数据库**: PostgreSQL（适合处理高并发和大量数据）
3. **缓存**: Redis（用于存储热点数据，提高读取速度）
4. **消息队列**: RabbitMQ（用于异步处理任务，如统计访问次数）
5. **Web服务器**: Nginx（用于反向代理和负载均衡）
6. **编码算法**: Base62（生成短链接）

### 系统架构

1. **前端**:
   - 提供一个简单的网页表单，用户输入长URL并提交。
   - 显示生成的短链接。
   - 提供一个输入框，用户输入短链接并获取对应的长链接。

2. **后端**:
   - **URL生成**:
     - 接受长URL请求。
     - 生成唯一ID并转换为Base62字符串。
     - 存储长URL和短链接的映射关系到数据库。
     - 返回生成的短链接。
   - **URL还原**:
     - 接受短链接请求。
     - 从数据库中查找对应的长URL。
     - 重定向到长URL。

3. **数据库**:
   - 存储长URL和短链接的映射关系。
   - 记录短链接的访问次数（可选）。

4. **缓存**:
   - 使用Redis缓存热点数据，提高读取速度。

5. **消息队列**:
   - 异步处理短链接访问次数的统计。

### 详细实现

#### 1. 后端实现

##### 安装依赖

```bash
pip install flask flask-sqlalchemy psycopg2-binary redis pika
```

##### 初始化项目结构

```
url_shortener/
├── app.py
├── config.py
├── models.py
├── routes.py
└── utils.py
```

##### `config.py`

```python
import os

class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'default_secret_key')
    SQLALCHEMY_DATABASE_URI = os.getenv('DATABASE_URL', 'postgresql://user:password@localhost/url_shortener')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    REDIS_URL = os.getenv('REDIS_URL', 'redis://localhost:6379/0')
    RABBITMQ_URL = os.getenv('RABBITMQ_URL', 'amqp://guest:guest@localhost:5672/')
```

##### `models.py`

```python
from flask_sqlalchemy import SQLAlchemy
from config import Config

db = SQLAlchemy()

class URL(db.Model):
    id = db.Column(db.BigInteger, primary_key=True)
    long_url = db.Column(db.String(500), unique=True, nullable=False)
    short_code = db.Column(db.String(10), unique=True, nullable=False)
    visits = db.Column(db.Integer, default=0)
```

##### `utils.py`

```python
import string
import random
import redis
import pika

def generate_short_code():
    characters = string.ascii_letters + string.digits
    return ''.join(random.choice(characters) for _ in range(6))

def get_redis_client():
    from config import Config
    return redis.StrictRedis.from_url(Config.REDIS_URL)

def send_to_rabbitmq(queue_name, message):
    from config import Config
    connection = pika.BlockingConnection(pika.URLParameters(Config.RABBITMQ_URL))
    channel = connection.channel()
    channel.queue_declare(queue=queue_name)
    channel.basic_publish(exchange='', routing_key=queue_name, body=message)
    connection.close()
```

##### `routes.py`

```python
from flask import Flask, request, redirect, jsonify
from config import Config
from models import db, URL
from utils import generate_short_code, get_redis_client, send_to_rabbitmq

app = Flask(__name__)
app.config.from_object(Config)
db.init_app(app)

@app.route('/shorten', methods=['POST'])
def shorten_url():
    data = request.json
    long_url = data.get('long_url')
    if not long_url:
        return jsonify({'error': 'Long URL is required'}), 400

    existing_url = URL.query.filter_by(long_url=long_url).first()
    if existing_url:
        short_code = existing_url.short_code
    else:
        short_code = generate_short_code()
        while URL.query.filter_by(short_code=short_code).first():
            short_code = generate_short_code()

        new_url = URL(long_url=long_url, short_code=short_code)
        db.session.add(new_url)
        db.session.commit()

    short_url = f"{request.host_url}{short_code}"
    return jsonify({'short_url': short_url}), 201

@app.route('/<short_code>', methods=['GET'])
def redirect_to_long_url(short_code):
    redis_client = get_redis_client()
    cached_long_url = redis_client.get(f"url:{short_code}")
    if cached_long_url:
        send_to_rabbitmq('url_visits', short_code)
        return redirect(cached_long_url.decode('utf-8'))

    url = URL.query.filter_by(short_code=short_code).first()
    if not url:
        return jsonify({'error': 'Short code not found'}), 404

    long_url = url.long_url
    url.visits += 1
    db.session.commit()
    redis_client.set(f"url:{short_code}", long_url)

    send_to_rabbitmq('url_visits', short_code)
    return redirect(long_url)

if __name__ == '__main__':
    app.run(debug=True)
```

##### `app.py`

```python
from flask import Flask
from config import Config
from models import db
from routes import app as routes_app

app = Flask(__name__)
app.config.from_object(Config)
db.init_app(app)

with app.app_context():
    db.create_all()

app.register_blueprint(routes_app)

if __name__ == '__main__':
    app.run(debug=True)
```

### 数据库初始化

在项目根目录下运行以下命令来初始化数据库：

```bash
flask db init
flask db migrate
flask db upgrade
```

### 运行项目

确保PostgreSQL、Redis和RabbitMQ服务已经启动，然后运行 Flask 应用：

```bash
flask run
```

### 前端实现

前端可以使用简单的 HTML 和 JavaScript 来实现表单提交和结果显示。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>URL Shortener</title>
</head>
<body>
    <h1>URL Shortener</h1>
    <form id="urlForm">
        <label for="longUrl">Long URL:</label>
        <input type="text" id="longUrl" name="longUrl" required>
        <button type="submit">Shorten!</button>
    </form>
    <div id="result"></div>

    <script>
        document.getElementById('urlForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const longUrl = document.getElementById('longUrl').value;
            fetch('/shorten', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ long_url: longUrl })
            })
            .then(response => response.json())
            .then(data => {
                if (data.error) {
                    document.getElementById('result').innerText = `Error: ${data.error}`;
                } else {
                    document.getElementById('result').innerText = `Short URL: ${data.short_url}`;
                }
            });
        });
    </script>
</body>
</html>
```

### 总结

以上是一个基本的URL短链接系统的实现。通过使用Python的Flask框架、PostgreSQL数据库、Redis缓存和RabbitMQ消息队列，可以高效地处理长URL的缩短和还原操作。此外，还可以进一步优化性能和扩展功能，例如添加用户认证、访问统计分析等。

# 系统设计

### 要求

编程语言不限，选择你觉得最合适的语言和技术框架，聊聊实现思路和模块设计即可，不需要详细代码

以下是五个经典的、基于功能需求生成代码的问题，这些问题贴合实际工程项目，具有较高的复杂度，能够很好地考验软件开发者的编程水平和项目能力：

### 1. **设计和实现分布式任务调度系统**

   - **功能需求**：开发一个分布式任务调度系统，允许多个节点并行执行任务。系统需要支持任务的动态分配、监控任务的执行状态、任务失败后的自动重试、任务依赖关系的管理等。
   - **复杂点**：
     - 任务的分配算法（如何动态负载均衡）
     - 节点的故障检测与恢复
     - 多任务依赖的处理，任务执行顺序的合理规划
     - 保证系统的扩展性和容错性

### 2. **开发高性能的RESTful API服务**

   - **功能需求**：开发一个处理高并发的RESTful API服务，服务需要支持CRUD操作和复杂的查询逻辑，同时确保在高流量情况下的响应速度和稳定性。需要集成身份验证、权限控制和API限流功能。
   - **复杂点**：
     - 数据库的查询优化（如索引、缓存策略）
     - 负载均衡、API限流（Rate Limiting）和熔断机制的实现
     - 身份验证（OAuth、JWT）和权限管理系统的集成
     - 高并发场景下的性能优化

### 3. **构建一个实时聊天应用程序**

   - **功能需求**：实现一个支持多个用户的实时聊天系统，具备群聊和私聊功能，并确保消息传输的可靠性。用户能够查看在线状态、历史消息，并收到消息提醒。系统还需要实现消息的加密传输和存储。
   - **复杂点**：
     - 实时消息的推送（WebSocket、Socket.IO）
     - 消息的持久化与历史记录查询
     - 用户状态的同步和通知机制
     - 消息加密和隐私保护

### 4. **实现一个智能推荐系统**

   - **功能需求**：为电商平台实现智能推荐系统，根据用户的浏览历史、购物记录等行为数据进行个性化推荐。系统需要支持基于协同过滤、内容推荐以及混合推荐的策略，并且能动态调整推荐算法。
   - **复杂点**：
     - 数据处理与特征提取（大规模用户数据的处理）
     - 推荐算法的选择与实现（协同过滤、矩阵分解、深度学习等）
     - 实时性与个性化的平衡（如何快速响应用户操作）
     - 性能优化与模型评估

### 5. **实现一个带权限管理的文件存储系统**

   - **功能需求**：开发一个企业级文件存储系统，支持用户上传、下载和共享文件。系统需要严格的权限管理机制，不同用户组对文件的访问权限（读/写/删除等）可以动态配置。还要支持大文件的断点续传和加密存储。
   - **复杂点**：
     - 文件的权限控制与用户组管理
     - 大文件的上传优化与断点续传实现
     - 文件的加密存储与解密访问
     - 系统的可扩展性（支持海量文件和用户并发操作）

这些问题不仅要求开发者具备扎实的编码能力，还需要他们在系统设计、性能优化、分布式架构、并发处理等方面展示出全面的工程能力。

# 功能模块

### 1. **实现一个简单的缓存系统（LRU Cache）**

   - **功能需求**：设计并实现一个支持最近最少使用（LRU）策略的缓存系统。系统需要提供 `get(key)` 和 `put(key, value)` 方法，保证缓存容量满时自动移除最久未使用的数据。
   - **复杂点**：
     - 使用数据结构（如哈希表和双向链表）来保持O(1)的时间复杂度
     - 缓存淘汰策略的实现

### 2. **实现一个基本的URL短链接服务**

   - **功能需求**：设计一个URL短链接系统，用户可以输入一个长URL，系统生成一个唯一的短URL。还需要提供将短URL还原为长URL的功能。
   - **复杂点**：
     - 哈希或ID生成策略
     - 短链接的唯一性保证
     - URL映射的存储（可使用内存或简单数据库）

### 3. **开发一个文本文件词频统计工具**

   - **功能需求**：编写一个程序，输入一个文本文件，输出文件中所有单词的词频统计结果。支持忽略大小写、过滤标点符号，并按词频排序输出结果。
   - **复杂点**：
     - 文件的读取与解析
     - 数据结构的选择（如哈希表或字典）
     - 排序与格式化输出

### 4. **设计一个简单的任务队列系统**

   - **功能需求**：实现一个基本的任务队列，支持任务的添加、删除和执行。队列中的任务需要按照优先级顺序执行，且每个任务执行后能够返回结果。
   - **复杂点**：
     - 优先级队列的实现
     - 任务调度与执行机制

### 5. **构建一个简易的日志分析工具**

   - **功能需求**：实现一个日志分析工具，解析系统产生的日志文件，并根据日志级别（如INFO、ERROR、DEBUG等）进行过滤，统计各级别日志的数量，输出统计结果和部分样本日志。
   - **复杂点**：
     - 正则表达式或字符串解析处理日志格式
     - 日志级别的过滤与统计

这些问题设计相对简洁，但仍然能够测试面试者的基本数据结构和算法能力，同时也考验他们解决实际问题的能力和代码质量。每个问题都可以在较短的时间内完成，但也提供了足够的扩展空间供深入探讨。

