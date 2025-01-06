---
title: Cursor自定义Claude API
tags:
  - 服务器
date created: 2024-12-17
date modified: 2024-12-17
sticky: 10
---

给你的Cursor换上第三方claude api

# 定制Cursor api的work代码

可以使用modelMapping来映射模型，从而达到绕过cursor模型匹配机制的效果
直接使用claude-3-5-sonnet-20241022会走官方claude的api，但是调用one-3-5-sn，再用work偷梁换柱就可以走自己买的第三方api

**功能**
- api代理，如果是调用官方渠道cf应该可以减轻封号的风险？
- 可根据url路径自定义api供应商，apikey不变，仅需替换baseurl；基本就是为替换cursor claude api定制的

**调用请求示例**
- https://你的CF域名/api供应商域名
- https://你的CF域名/api供应商域名/v1

```js
const modelMapping = {
    "one-3-5-sn": "claude-3-5-sonnet-20241022",
    "one-3-5-hk": "claude-3-5-haiku-20241022",
};

const apiMapping = {
	"gemini-2.0-flash-exp": "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent?key="
}

export default {
    async fetch(request) {
        const requestUrl = new URL(request.url);

        // 动态目标路径提取
        const [_, targetHost, ...pathParts] = requestUrl.pathname.split('/');
        if (!targetHost) {
            return new Response('Invalid request: missing target host.', { status: 400 });
        }

        // 构造新的目标 URL
        const targetPath = pathParts.join('/');
        const targetUrl = new URL(`https://${targetHost}/${targetPath}`);

        const init = {
            method: request.method,
            headers: new Headers(request.headers),
        };

        // 检查是否需要处理请求体
        if (request.method === 'POST' || request.method === 'PUT') {
            const contentType = request.headers.get('content-type');
            if (contentType && contentType.includes('application/json')) {
                const bodyText = await request.text();
                try {
                    const body = JSON.parse(bodyText);

                    // 模型替换逻辑
                    console.log('Original model:', body.model);
                    if (body.model && modelMapping[body.model]) {
                        body.model = modelMapping[body.model];
                        console.log('Replaced model:', body.model);
                    }

                    // 更新请求体
                    init.body = JSON.stringify(body);
                    init.headers.set('content-type', 'application/json'); // 确保保持 JSON 类型
                } catch (error) {
                    console.error('Failed to parse JSON body:', error.message);
                    // 保持原始请求体
                    init.body = bodyText;
                }
            } else {
                // 非 JSON 请求体直接传递
                init.body = await request.text();
            }
        }

        // 发起请求到目标地址
        return fetch(targetUrl, init);
    },
};

```


# Gemini

```js
async function gemini(requestJson, env) {
  const messages = requestJson.messages;
  const apiURL = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key='+env.GEMINI_API_KEY;
  const response = await fetch(apiURL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({"contents":transform(messages)})
  });
  if (!response.ok) {
    return new Response(await response.text(), { status: 400});
  }
  const resp = await response.json();

  const chatSession = {
    "model":requestJson.model,
    "request":requestJson.messages,
    "response":resp
  };
  if (env.ChatRecordR2){
    await env.ChatRecordR2.put(requestJson.chatUniqueId, JSON.stringify(chatSession));
  }

  return new Response(resp.candidates[0].content.parts[0].text);
}



// src/providers/gemini.ts
function reformatInput(messages) {
  return messages.map((message) => ({
    role: message.role === "user" ? "user" : "model",
    parts: [{ text: message.content }]
  }));
}
var GeminiInference = class {
  endpoint = "https://generativelanguage.googleapis.com/v1beta/models";
  supplier = "gemini" /* GEMINI */;
  primaryEndpoint = "https://generativelanguage.googleapis.com/v1beta/models";
  fallbackEndpoint = "https://vercel.ddot.cc/gemini/v1";
  async prepareData(js) {
    let x = reformatInput(js.messages);
    if (x.length > 0 && x[0].role !== "user") {
      x.shift();
    }
    const { max_tokens, temperature, top_p, top_logprobs } = js;
    const config2 = {
      temperature: temperature || 0.9,
      maxOutputTokens: max_tokens || 4096,
      topP: top_p || 0.95,
      topK: top_logprobs || 32
    };
    return {
      contents: x,
      generationConfig: config2
    };
  }
```

## 使用 curl 命令验证您的 API 密钥
```
API_KEY="YOUR_API_KEY" curl -H 'Content-Type: application/json' \ -d '{"contents":[ {"role": "user", "parts":[{"text": "Give me five subcategories of jazz?"}]}]}' \ "https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent?key=${API_KEY}"
```



# 参考文章

- [使用Cf Workers搭建反代加速器 | LiuShen's Blog](https://blog.liushen.fun/posts/dd89adc9/)
- [Cursor 使用 One API 配置 Anthropic Claude BaseURL 代理指南_cursor oneapi-CSDN博客](https://blog.csdn.net/zhq426/article/details/141982668)
- [通过CF Worker代理第三方api在Cursor中使用Claude | Shawn's Blog](https://rongsh.me/posts/2024-11-11-oneapi_cursor/)

