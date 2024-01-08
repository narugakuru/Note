为了体验GPT4，更快的响应，以及（可能）优先Plugin体验（然而还没申请下来）等权益，最近开通了ChatGPT Plus的会员。这篇文章分享一下开通的过程，以及一些经验。

> 2023/04/24 更新，已经自动续费，没出现扣款失败。
> 
> ![image-20230425063722305](https://razeen.me/img/2023/image-20230425063722305.png)

## [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E8%BF%87%E7%A8%8B "过程")过程

我整个开通的过程还是比较顺利的，其中的关键步骤如下：

-   信用卡：使用的是 [Depay 的 Master 虚拟信用卡](https://razeen.me/posts/how-to-use-depay/)。
-   梯子：[全局科学上网](https://razeen.me/posts/how-to-access-the-real-Internet/)，全局是用美国的节点。
-   浏览器：Microsoft Edge Dev 浏览器。
-   账单地址：使用[美国地址生成器](https://www.meiguodizhi.com/usa-address/alaska)生成了一个免税州的地址和电话。

![image-20230420055139249](https://razeen.me/img/2023/image-20230420055139249.png)

![image-20230420060218952](https://razeen.me/img/2023/image-20230420060218952.png)

开通Plus门槛确实有点高，需要IP，地址，信用卡都没问题才能成功开通。风控的太严格了。

下面从几个方面，结合一些身边和推上分享的经验，展开聊聊。

## [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E5%85%B3%E4%BA%8E%E7%BD%91%E7%BB%9C "关于网络")关于网络

支付是否能成功，首页原因应该就是网络。

一般使用的代理IP，可能被很多人在使用，IP本身的风险就很高。

### [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#IP-%E6%A3%80%E6%B5%8B "IP 检测")IP 检测

可以利用下面几个工具来查看自己的IP，提供商信息，是否在黑名单之类的：

-   [whoer.net](https://whoer.net/zh)
-   [ipinfo.io](https://ipinfo.io/)

下面的截图是我成功支付的节点情况。

![image-20230420063842513](https://razeen.me/img/2023/image-20230420063842513.png)

还可以利用 [scamalytics](https://scamalytics.com/ip) 来查询一下IP的欺诈风险，风险低的节点，成功几率会大点。

下图是我成功支付的节点风险检测。

![image-20230420064445897](https://razeen.me/img/2023/image-20230420064445897.png)

### [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E6%90%AD%E5%BB%BA%E4%B8%B4%E6%97%B6%E4%BB%A3%E7%90%86 "搭建临时代理")搭建临时代理

购买的机场IP可能被风控，就可以考虑自己搭建梯子。

-   可以考虑按用量开通一个机器，用docker快速搭建，用于支付，支付完成就关闭。
-   可以考虑微软的Azure,Google,AWS 等大一点的云提供商。
-   可以考虑开通一个和自己选的账单地址相同的地区开通。

身边有位朋友，自己的梯子死活不行，然后就在AWS上，开通了一个和账单地址相同地区的VPS，在支付的环节，将代理切换到这个临时搭建的代理上，成功支付了。

### [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E4%BD%8F%E5%AE%85-IP "住宅 IP")住宅 IP

上面方法还行不通，可以考虑在代理的前面套一个美国住宅IP代理。提供住宅IP的商家很多，如

-   [ip2world](https://www.ip2world.com/?ref=77WL2QBNJX)
-   [iproyal](https://iproyal.com/?r=238136)
-   [brightdata](https://brightdata.com/proxy-types/isp-proxies)

## [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E5%85%B3%E4%BA%8E%E6%B5%8F%E8%A7%88%E5%99%A8 "关于浏览器")关于浏览器

我直接使用 Microsoft Edge Dev 浏览器就开通成功了。看很多是用chrome无痕模式。理论上就是尽量保证环境干净，不被以往的一些Cookie,缓存触发一些风控。这没有具体的指标，只能失败后多次尝试。

## [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E5%85%B3%E4%BA%8E%E6%94%AF%E4%BB%98 "关于支付")关于支付

目前我了解到的支付方式有：

-   找拥有国外信用卡/借记卡的朋友开通，当然也有找[美帝朋友帮忙都无法开通的例子](https://twitter.com/haoel/status/1648513213640896516?s=20)。
    
-   淘宝找人开通。多花点钱，把折腾的事给淘宝解决无疑也是个好方法。唯一担心的是商家是怎么支付的，是不是用的什么网传的黑卡，会封号之类的。可以提前沟通好，问问支付方式，看看评论等综合考虑。
    
-   然后就是自己开通虚拟信用卡，目前了解到的开通比较简单的有：
    
    -   [Depay](https://depay.depay.one/web-app/register-h5?invitCode=807647&lang=en-us) 👉 我用的是[这种方式](https://razeen.me/posts/how-to-use-depay/) ，531993 卡段的。相对来说手续费低点。
    -   [OneKey](https://card.onekey.so/?i=CNN5WO) 👉 最近新出的，开卡很方便，但费用会稍微高点。
    -   [NobePay](https://www.nobepay.com/) 👉 有几个卡段可以选，500元或30USDT起充。
-   还有就是注册海外的个体商户的信用卡等，这种需要护照或国际友人帮忙。
    

​

其中自己支付的时候，还需要准备好美国的地址，用[地址生成器](https://www.meiguodizhi.com/usa-address/alaska)就好了。注意选免税州的，不然还要交税。

## [](https://razeen.me/posts/how-to-activate-chatgpt-plus/#%E5%85%B3%E4%BA%8E%E8%B4%A6%E5%8F%B7 "关于账号")关于账号

当然先要拥有一个[账号](https://razeen.me/posts/ai-how-to-register-chatgpt/)，账号没有被封。

如果排除其他因素，同一个账号总是无法支付，可以考虑开通一下新账号试试。账号的开通，登陆，购买尽量在同一个IP，或保证IP在同一个地区，减少被风控的风险。

这篇文件就分享这么多，等后续续费的时候再来更新一下续费情况，祝开通顺利～