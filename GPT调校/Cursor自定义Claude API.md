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

调用请求示例
- https://你的CF域名/api供应商域名
- https://你的CF域名/api供应商域名/v1

```js
const modelMapping = {
    "one-3-5-sn": "claude-3-5-sonnet-20241022",
    "one-3-5-hk": "claude-3-5-haiku-20241022",
};

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

# 参考文章

- [使用Cf Workers搭建反代加速器 | LiuShen's Blog](https://blog.liushen.fun/posts/dd89adc9/)
- [Cursor 使用 One API 配置 Anthropic Claude BaseURL 代理指南_cursor oneapi-CSDN博客](https://blog.csdn.net/zhq426/article/details/141982668)
