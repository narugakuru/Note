vue+fastapi项目中，vue发起websock请求，前后端的连接条件和终止条件分别是什么？


我需要实现一个demo  
前端有按钮和日志输出界面  
后端有两个接口，ws接口用于实时传输日志，run接口用于执行脚本  
前端需要调用两个接口，当执行run时开启ws连接，获取日志并实时更新到界面上，当run接口执行完毕时前端主动关闭ws连接

现在让我们来改进一下这demo  

后端fastapi需要使用logging日志模块，run_script接口调用script脚本，script中使用了logging处理日志，script是已有的一些脚本，请你从logging入手来想办法传输日志  
下面是每个script脚本都有的run函数，通过script_name来管理每一个logging实例  
setup_stream_logger是一个已有的logging初始化函数  
请你讲解一下实现原理，如果仅通过logging模块来给ws接口实时传输日志


我明白了，既然script中使用了logging模块并且通过QueueHandler来处理日志，我们可以利用这个机制来实现WebSocket实时传输日志。下面是实现方案：

1. 首先，让我们实现一个全局的日志队列管理器：

```python
# queue_manager.py
from queue import Queue
from typing import Dict

class QueueManager:
    def __init__(self):
        self._queues: Dict[str, Queue] = {}

    def get_queue(self, name: str) -> Queue:
        if name not in self._queues:
            self._queues[name] = Queue()
        return self._queues[name]

    def remove_queue(self, name: str):
        if name in self._queues:
            del self._queues[name]

queue_manager = QueueManager()
```

2. 修改FastAPI后端代码：

```python
from fastapi import FastAPI, WebSocket
import asyncio
from queue_manager import queue_manager
import logging
from typing import Dict, Set

app = FastAPI()

# 存储活跃的WebSocket连接
active_connections: Dict[str, Set[WebSocket]] = {}

# WebSocket接口
@app.websocket("/ws/{script_name}")
async def websocket_endpoint(websocket: WebSocket, script_name: str):
    await websocket.accept()
    
    # 将WebSocket连接添加到对应脚本的连接集合中
    if script_name not in active_connections:
        active_connections[script_name] = set()
    active_connections[script_name].add(websocket)
    
    try:
        # 获取该脚本的日志队列
        log_queue = queue_manager.get_queue(script_name)
        
        # 持续监听队列并发送日志
        while True:
            try:
                # 非阻塞方式获取日志
                while not log_queue.empty():
                    log_record = log_queue.get_nowait()
                    if isinstance(log_record, logging.LogRecord):
                        # 格式化日志消息
                        formatted_log = f"{log_record.asctime} - {log_record.levelname} - {log_record.getMessage()}"
                        await websocket.send_text(formatted_log)
                
                # 适当的休眠以避免过度CPU使用
                await asyncio.sleep(0.1)
                
            except asyncio.CancelledError:
                break
    finally:
        active_connections[script_name].remove(websocket)
        if not active_connections[script_name]:
            del active_connections[script_name]

# 执行脚本接口
@app.post("/run/{script_name}")
async def run_script(script_name: str, params: dict):
    try:
        # 确保logger已经设置
        setup_stream_logger(script_name)
        
        # 异步执行脚本
        result = await run(script_name, params)
        
        # 通知所有相关的WebSocket连接脚本执行完成
        if script_name in active_connections:
            for connection in active_connections[script_name]:
                await connection.send_text("Script execution completed")
        
        return {"status": "success", "result": result}
    except Exception as e:
        return {"status": "error", "message": str(e)}
```

3. 修改前端代码：

```vue
<template>
  <div class="container">
    <button @click="runScript" :disabled="isRunning">
      {{ isRunning ? 'Running...' : 'Run Script' }}
    </button>
    <div class="log-container">
      <div v-for="(log, index) in logs" 
           :key="index"
           :class="getLogLevel(log)">
        {{ log }}
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const logs = ref([])
    const isRunning = ref(false)
    let ws = null

    const initWebSocket = (scriptName) => {
      ws = new WebSocket(`ws://localhost:8000/ws/${scriptName}`)
      
      ws.onopen = () => {
        console.log('WebSocket connected')
      }

      ws.onmessage = (event) => {
        if (event.data === "Script execution completed") {
          closeWebSocket()
        } else {
          logs.value.push(event.data)
        }
      }

      ws.onerror = (error) => {
        console.error('WebSocket error:', error)
      }

      ws.onclose = () => {
        console.log('WebSocket disconnected')
        isRunning.value = false
      }
    }

    const closeWebSocket = () => {
      if (ws) {
        ws.close()
        ws = null
      }
    }

    const runScript = async () => {
      try {
        const scriptName = 'your_script_name' // 指定要运行的脚本名称
        logs.value = []
        isRunning.value = true
        
        // 先建立WebSocket连接
        initWebSocket(scriptName)
        
        // 调用run接口
        const response = await fetch(`http://localhost:8000/run/${scriptName}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            // 传入脚本所需参数
          })
        })
        
        if (!response.ok) {
          throw new Error('Failed to run script')
        }
      } catch (error) {
        console.error('Error:', error)
        closeWebSocket()
        isRunning.value = false
      }
    }

    return {
      logs,
      isRunning,
      runScript
    }
  }
}
</script>
```

实现原理说明：

1. 日志传输机制：
- 利用已有的QueueHandler将日志记录放入队列
- WebSocket连接会持续监控对应脚本的日志队列
- 当队列中有新的日志时，立即通过WebSocket发送给前端

2. 关键点：
- 使用脚本名称作为标识符来管理不同脚本的日志队列和WebSocket连接
- 通过Dict[str, Set[WebSocket]]来管理多个脚本的多个WebSocket连接
- 非阻塞方式读取队列，避免影响其他操作
- 使用asyncio.sleep来控制检查频率

3. 优势：
- 完全利用现有的logging机制
- 支持多个脚本同时运行
- 不需要修改现有脚本的日志处理逻辑
- 可以很容易地扩展添加更多功能（如日志级别过滤等）

4. 注意事项：
- 需要确保每个脚本使用唯一的logger名称
- 要适当处理WebSocket连接的清理
- 考虑添加日志队列的大小限制
- 可能需要添加心跳机制确保连接存活

这个实现方案的核心是利用了logging模块的QueueHandler机制，通过队列来中转日志信息，然后由WebSocket连接负责将队列中的日志实时发送到前端。这样可以很好地集成到现有的脚本系统中，而且不需要修改现有的日志处理逻辑。


# Debug

功能需求，使用websocket_endpoint实时传输logging日志给前端

前端vue，后端fastapi需要使用logging日志模块，run_script接口调用script脚本，script中使用了logging处理日志，script是已有的一些脚本，请你从logging入手来想办法传输日志  

每个script脚本都有的run函数，通过script_name来管理每一个logging实例  
setup_stream_logger是一个已有的logging初始化函数  

目前的实现是通过已有的QueueHandler来传输日志，日志会被收集到一个Queue里
前端日志输出界面，可以看到显示 Script execution completed！！！之后日志才开始传输
现在存在的问题是，日志并没有实时传输，而是在run_script接口执行完毕后，日志才开始通过websocket传输，这看起来是python中对于队列的使用发生了阻塞？script脚本和websocket_endpoint接口同时在调用Queue实例


```终端
e:\CodeAchieve\MyFluent\itTools-fastapi/server\api\script_routes.py has been modified, restarting FastAPI server...
Restart complete.
INFO:     Started server process [30660]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
DEBUG:    = connection is CONNECTING
DEBUG:    < GET /api/script/ws/copy_list HTTP/1.1
DEBUG:    < host: localhost:8000
DEBUG:    < connection: Upgrade
DEBUG:    < pragma: no-cache
DEBUG:    < cache-control: no-cache
DEBUG:    < user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36 Edg/130.0.0.0
DEBUG:    < upgrade: websocket
DEBUG:    < origin: http://localhost:5173
DEBUG:    < sec-websocket-version: 13
DEBUG:    < accept-encoding: gzip, deflate, br, zstd
DEBUG:    < accept-language: zh-CN,zh;q=0.9,ja;q=0.8,en;q=0.7,en-GB;q=0.6,en-US;q=0.5      
DEBUG:    < sec-websocket-key: cpYO1l4C6qQ+8o+HngBqPw==
DEBUG:    < sec-websocket-extensions: permessage-deflate; client_max_window_bits
INFO:     ('127.0.0.1', 16405) - "WebSocket /api/script/ws/copy_list" [accepted]
===websocket_endpoint===log_queue: copy_list====
DEBUG:    > HTTP/1.1 101 Switching Protocols
DEBUG:    > Upgrade: websocket
DEBUG:    > Connection: Upgrade
DEBUG:    > Sec-WebSocket-Accept: 4L7h1HiHvujQRxU+8iX8k1sCkw4=
DEBUG:    > Sec-WebSocket-Extensions: permessage-deflate
DEBUG:    > date: Wed, 30 Oct 2024 06:21:40 GMT
DEBUG:    > server: uvicorn
INFO:     connection open
DEBUG:    = connection is OPEN
INFO:     127.0.0.1:16406 - "OPTIONS /api/script/copy_list HTTP/1.1" 200 OK
指定获取脚本logger名称：======== copy_list =========
=========Executing script: copy_list==========
copy folder from E:/WorkSpace/WebKaisyu/ssl-htdocs-local to E:/WorkSpace/WebKaisyu/html_1020
100%|█████████████████████████████████████████████████████| 7/7 [00:00<00:00, 1293.40it/s] 
DEBUG:    > TEXT 'Script execution completed！！！' [35 bytes]
INFO:     127.0.0.1:16406 - "POST /api/script/copy_list HTTP/1.1" 200 OK
DEBUG:    = connection is CLOSING
DEBUG:    > CLOSE 1000 (OK) [2 bytes]
DEBUG:    < CLOSE 1000 (OK) [2 bytes]
DEBUG:    x half-closing TCP connection
DEBUG:    = connection is CLOSED
Timeout occurred, closing WebSocket connection!!!
INFO:     connection closed
```

```前端日志

Script execution completed！！！
2024-10-30 14:37:58,236 - Starting script: copy_list
2024-10-30 14:38:00,243 - ====== copy_files_with_structure =======
2024-10-30 14:38:00,243 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\recruit/msg01.html -> E:/WorkSpace/WebKaisyu/html_1020\recruit/msg01.html
2024-10-30 14:38:00,245 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\recruit/way.html -> E:/WorkSpace/WebKaisyu/html_1020\recruit/way.html
2024-10-30 14:38:00,245 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\recruit/carrerPath.html -> E:/WorkSpace/WebKaisyu/html_1020\recruit/carrerPath.html
2024-10-30 14:38:00,245 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\recruit/training.html -> E:/WorkSpace/WebKaisyu/html_1020\recruit/training.html
2024-10-30 14:38:00,245 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\recruit/other.html -> E:/WorkSpace/WebKaisyu/html_1020\recruit/other.html
2024-10-30 14:38:00,248 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common/template/footer.php -> E:/WorkSpace/WebKaisyu/html_1020\common/template/footer.php
2024-10-30 14:38:00,249 - 
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common2/tmp/footer.php -> E:/WorkSpace/WebKaisyu/html_1020\common2/tmp/footer.php
2024-10-30 14:38:00,249 - Script completed: copy_list
2024-10-30 14:38:00,249 - Result: True

=== 连接已关闭 ===
```



### Debug2

这是一个fastapi项目  
希望的功能：前端请求websocket_endpoint建立连接，然后请求run_script执行脚本，run_script期间产生的日志被websocket_endpoint实时传输给前端

现在存在的问题是，日志并没有实时传输，而是在run_script接口执行完毕后，日志才开始通过websocket传输，这看起来是python发生了串行阻塞？

我在WebSocketHandler中使用了异步操作，但是看起来不起效果
请解释原因可能是哪一步导致了异步阻塞


```
===websocket_endpoint: copy_list====
DEBUG:    > HTTP/1.1 101 Switching Protocols
DEBUG:    > Upgrade: websocket
DEBUG:    > Connection: Upgrade
DEBUG:    > Sec-WebSocket-Accept: aORJvVxlJv4tFlQjYlSm6DjvgkM=
DEBUG:    > Sec-WebSocket-Extensions: permessage-deflate
DEBUG:    > date: Wed, 30 Oct 2024 11:37:46 GMT
DEBUG:    > server: uvicorn
INFO:     connection open
DEBUG:    = connection is OPEN
======== 指定获取脚本logger名称：copy_list =========
Attempting to send message: 2024-10-30 19:37:47,013 - INFO - ======== run_script : copy_list ==========
Message queued for sending
========= execute_script: copy_list==========
========== run! ： copy_list ===========
Attempting to send message: 2024-10-30 19:37:47,025 - INFO - ====== copy_files_with_structure =======
Message queued for sending
copy folder from E:/WorkSpace/WebKaisyu/ssl-htdocs-local to E:/WorkSpace/WebKaisyu/html_10114
  0%|                                                                                                                                                  | 0/5 [00:00<?, ?it/s]Attempting to send message: 2024-10-30 19:37:47,028 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/content.html
Message queued for sending
Attempting to send message: 2024-10-30 19:37:47,029 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect01.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect01.html
Message queued for sending
Attempting to send message: 2024-10-30 19:37:47,031 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect02.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect02.html
Message queued for sending
Attempting to send message: 2024-10-30 19:37:47,032 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/exam/report.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/exam/report.html
Message queued for sending
Attempting to send message: 2024-10-30 19:37:47,033 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common/css/common.css -> E:/WorkSpace/WebKaisyu/html_10114\common/css/common.css
Message queued for sending
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 5/5 [00:00<00:00, 838.16it/s] 
Attempting to send message: 2024-10-30 19:37:47,034 - INFO - Script completed: copy_list
Message queued for sending
Attempting to send message: 2024-10-30 19:37:47,034 - INFO - Result: True
Message queued for sending
INFO:     127.0.0.1:59406 - "POST /api/script/copy_list HTTP/1.1" 200 OK
DEBUG:    > TEXT '2024-10-30 19:37:47,013 - INFO - ======== run_s... : copy_list ==========' [75 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,025 - INFO - ====== copy_files_with_structure =======' [73 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,028 - INFO - \nCopied: E:/W...14\\effort/content.html' [158 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,029 - INFO - \nCopied: E:/W...\\effort/reflect01.html' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,031 - INFO - \nCopied: E:/W...\\effort/reflect02.html' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,032 - INFO - \nCopied: E:/W...effort/exam/report.html' [166 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,033 - INFO - \nCopied: E:/W...\\common/css/common.css' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,034 - INFO - Script completed: copy_list' [60 bytes]
DEBUG:    > TEXT '2024-10-30 19:37:47,034 - INFO - Result: True' [45 bytes]
```


```
INFO:     ('127.0.0.1', 59547) - "WebSocket /api/script/ws/copy_list" [accepted]
WebSocket connection established for copy_list
======== 指定获取脚本logger名称：copy_list =========
2024-10-30 19:38:53,046 - copy_list - INFO - Setting up stream logger: copy_list
====== 初始化日志Handler:  copy_list =======
WebSocket handler added to logger copy_list
===websocket_endpoint: copy_list====
DEBUG:    > HTTP/1.1 101 Switching Protocols
DEBUG:    > Upgrade: websocket
DEBUG:    > Connection: Upgrade
DEBUG:    > Sec-WebSocket-Accept: FkEGXnCB+6vOXnBwnpR7kBV/VoY=
DEBUG:    > Sec-WebSocket-Extensions: permessage-deflate
DEBUG:    > date: Wed, 30 Oct 2024 11:38:52 GMT
DEBUG:    > server: uvicorn
INFO:     connection open
DEBUG:    = connection is OPEN
INFO:     127.0.0.1:59548 - "OPTIONS /api/script/copy_list HTTP/1.1" 200 OK
======== 指定获取脚本logger名称：copy_list =========
2024-10-30 19:38:53,064 - copy_list - INFO - ======== run_script : copy_list ==========
Attempting to send message: 2024-10-30 19:38:53,064 - INFO - ======== run_script : copy_list ==========
Message queued for sending
========= execute_script: copy_list==========
========== run! ： copy_list ===========
2024-10-30 19:38:53,078 - copy_list - INFO - ====== copy_files_with_structure =======
Attempting to send message: 2024-10-30 19:38:53,078 - INFO - ====== copy_files_with_structure =======
Message queued for sending
copy folder from E:/WorkSpace/WebKaisyu/ssl-htdocs-local to E:/WorkSpace/WebKaisyu/html_10114
  0%|                                                                                                                                                  | 0/5 [00:00<?, ?it/s]2024-10-30 19:38:53,082 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/content.html
Attempting to send message: 2024-10-30 19:38:53,082 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/content.html
Message queued for sending
2024-10-30 19:38:53,083 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect01.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect01.html
Attempting to send message: 2024-10-30 19:38:53,083 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect01.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect01.html
Message queued for sending
2024-10-30 19:38:53,084 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect02.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect02.html
Attempting to send message: 2024-10-30 19:38:53,084 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect02.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect02.html
Message queued for sending
2024-10-30 19:38:53,086 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/exam/report.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/exam/report.html
Attempting to send message: 2024-10-30 19:38:53,086 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/exam/report.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/exam/report.html
Message queued for sending
2024-10-30 19:38:53,087 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common/css/common.css -> E:/WorkSpace/WebKaisyu/html_10114\common/css/common.css
Attempting to send message: 2024-10-30 19:38:53,087 - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common/css/common.css -> E:/WorkSpace/WebKaisyu/html_10114\common/css/common.css
Message queued for sending
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 5/5 [00:00<00:00, 668.61it/s] 
2024-10-30 19:38:53,088 - copy_list - INFO - Script completed: copy_list
Attempting to send message: 2024-10-30 19:38:53,088 - INFO - Script completed: copy_list
Message queued for sending
2024-10-30 19:38:53,088 - copy_list - INFO - Result: True
Attempting to send message: 2024-10-30 19:38:53,088 - INFO - Result: True
Message queued for sending
INFO:     127.0.0.1:59548 - "POST /api/script/copy_list HTTP/1.1" 200 OK
DEBUG:    > TEXT '2024-10-30 19:38:53,064 - INFO - ======== run_s... : copy_list ==========' [75 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,078 - INFO - ====== copy_files_with_structure =======' [73 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,082 - INFO - \nCopied: E:/W...14\\effort/content.html' [158 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,083 - INFO - \nCopied: E:/W...\\effort/reflect01.html' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,084 - INFO - \nCopied: E:/W...\\effort/reflect02.html' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,086 - INFO - \nCopied: E:/W...effort/exam/report.html' [166 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,087 - INFO - \nCopied: E:/W...\\common/css/common.css' [162 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,088 - INFO - Script completed: copy_list' [60 bytes]
DEBUG:    > TEXT '2024-10-30 19:38:53,088 - INFO - Result: True' [45 bytes]
```


### 异步队列

```
INFO:     ('127.0.0.1', 60595) - "WebSocket /api/script/ws/copy_list" [accepted]
WebSocket connection established for copy_list
======== 指定获取脚本logger名称：copy_list =========
DEBUG:    > HTTP/1.1 101 Switching Protocols
DEBUG:    > Upgrade: websocket
DEBUG:    > Connection: Upgrade
DEBUG:    > Sec-WebSocket-Accept: daF4ffWit4YRztE/J1ejq02lfig=
DEBUG:    > Sec-WebSocket-Extensions: permessage-deflate
DEBUG:    > date: Wed, 30 Oct 2024 11:49:00 GMT
DEBUG:    > server: uvicorn
INFO:     connection open
DEBUG:    = connection is OPEN
INFO:     127.0.0.1:60596 - "OPTIONS /api/script/copy_list HTTP/1.1" 200 OK
======== 指定获取脚本logger名称：copy_list =========
2024-10-30 19:49:01,071 - copy_list - INFO - ======== run_script : copy_list ==========
========= execute_script: copy_list==========
========== run! ： copy_list ===========
2024-10-30 19:49:01,071 - copy_list - INFO - ====== copy_files_with_structure =======
copy folder from E:/WorkSpace/WebKaisyu/ssl-htdocs-local to E:/WorkSpace/WebKaisyu/html_10114
  0%|                                                                                                                                                  | 0/5 [00:00<?, ?it/s]2024-10-30 19:49:01,074 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/content.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/content.html
2024-10-30 19:49:01,075 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect01.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect01.html
2024-10-30 19:49:01,076 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/reflect02.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/reflect02.html
2024-10-30 19:49:01,077 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\effort/exam/report.html -> E:/WorkSpace/WebKaisyu/html_10114\effort/exam/report.html
2024-10-30 19:49:01,078 - copy_list - INFO -
Copied: E:/WorkSpace/WebKaisyu/ssl-htdocs-local\common/css/common.css -> E:/WorkSpace/WebKaisyu/html_10114\common/css/common.css
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 5/5 [00:00<00:00, 713.15it/s] 
2024-10-30 19:49:01,079 - copy_list - INFO - Script completed: copy_list
2024-10-30 19:49:01,079 - copy_list - INFO - Result: True
INFO:     127.0.0.1:60596 - "POST /api/script/copy_list HTTP/1.1" 200 OK
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,071 - INFO - ======== run_s... : copy_list ==========' [75 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,071 - INFO - ====== copy_files_with_structure =======' [73 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,074 - INFO - \nCopied: E:/W...14\\effort/content.html' [158 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,075 - INFO - \nCopied: E:/W...\\effort/reflect01.html' [162 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,076 - INFO - \nCopied: E:/W...\\effort/reflect02.html' [162 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,077 - INFO - \nCopied: E:/W...effort/exam/report.html' [166 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,078 - INFO - \nCopied: E:/W...\\common/css/common.css' [162 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,079 - INFO - Script completed: copy_list' [60 bytes]
====== =websocket开始发送消息 =====
DEBUG:    > TEXT '2024-10-30 19:49:01,079 - INFO - Result: True' [45 bytes]
Connection timeout
```