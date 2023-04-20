
### Ubantu
kasm_user
password

### portainer
默认密码
首先下载Portainer的Docker镜像；
```bash
docker pull portainer/portainer
```
然后再使用如下命令运行Portainer容器；
```bash
docker run -p 9000:9000 -p 8000:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /mydata/portainer/data:/data \
-d portainer/portainer
```

### homepage
```shell
docker run -p 3000:3000
-v /mnt/mmc1-6/Configs/homepage/config:/app/config
-v /var/run/docker.sock:/var/run/docker.sock
--restart=always
ghcr.io/benphelps/homepage:latest

docker run -p 3000:3000 -v /mnt/mmc1-6/Configs/homepage/config:/app/config -v /var/run/docker.sock:/var/run/docker.sock --restart=always ghcr.io/benphelps/homepage:latest
```

### Syncthing
```bash
syncthing -gui-address="0.0.0.0:8384" -home="/mnt/mmc1-6/Configs/syncthing" -logfile="/var/log/syncthing.log"  >/dev/null &
```
-   `gui-address` 用于指定 Syncthing web 控制台的地址与端口，默认是 `127.0.0.1` 也就是只有路由器本机可以访问，我们电脑是访问不了的，改成 `0.0.0.0` 也就是任何人都可以访问。
-   `home` 用于指定配置文件存放位置。
-   `logfile` 用于指定日志文件存放位置。
-   `no-browser` 启动时不要自动打开 web 控制台。只对 GUI 系统有效，顺手加上就行了。
-   `>/dev/null` 将输出重定向到 `dev/null`，这是个特殊位置，相当于直接丢弃。也就是不要显示输出。
-   `&` 后台运行。

### Docker可视化面板

docker run --restart always --name fast -p 8081:8081 -d -v /var/run/docker.sock:/var/run/docker.sock wangbinxingkong/fast


docker run --restart always --name casaos -p 8082:8082 casaos/docker101tutorial


### DDNS

#### 外网不能访问内网

内网ipv6可以互相ping通，可以访问外网，外网不能访问内网。
原因：软路由接口没有提供dhcp服务

<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302040057564.png" alt="image.png" style="zoom:67%;" />
#### 内网不能连接bug
软路由作为主路由，无法连接
lan口：192.168.31.2
wan口：192.168.31.3
wifi：192.168.31.1
拓扑：
<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302042142426.png" alt="image.png" style="zoom:33%;" />
<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302010849326.png" alt="image.png" style="zoom:33%;" />

更改IP6端口
vi /etc/config/uhttpd
修改第四行的监听端口443为444
/etc/init.d/uhttpd restart


#### 获取光猫超级用户和密码。

用户名：CMCCAdmin
超级密码：aDm8H%MdA
网络远程管理认证密码：k1808962ek
宽带：18707976607 360724

#### 网络状态
-   连接类型：PPPoE
-   账户：18707976607
-   IP地址：10.159.70.49
-   子网掩码：255.255.255.255
-   默认网关：10.159.64.1
-   DNS：211.141.90.68 211.141.85.68
-   外网状态：拨号成功 [断开](http://192.168.31.1/cgi-bin/luci/;stok=e516a69a60f39304c3767e05beea4ca3/web/setting/wan#)

-   IPv6连接类型：Native
-   WAN IPv6地址：2409:8a38:5401:3097:8ede:f957:5c31:920c/64  
    
-   WAN IPv6网关：fe80::200:5eff:fe00:109
-   LAN IPv6地址：2409:8a38:5413:2960::1/64
-   LAN IPv6前缀：2409:8a38:5413:2960::  
    
-   DNS：2409:8038:2000:1000:211:141:90:68  
    2409:8038:2000:2000:211:141:85:68



#### 关闭防火墙
1.  阻止防火墙服务开机自动启动
/etc/init.d/firewall disable

2. 关闭防火墙
/etc/init.d/firewall stop

### Docker网络配置
#### Docker访问外网
![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302052036616.png)

-   （1）busybox发送ping包：172.17.0.2 > www.bing.com。
-   （2）dockero收到包，发现是发送到外网的，交给NAT处理。
-   （3）NAT将源地址换成ens192的IP：10.0.2.15 >www.bing.com.
-   （4）ping从 ens192出去，达www.bing.como

#### 外网访问Docker
**Docker默认关闭ip6，需手动打开！！！**
先划分ip
2409:8a38:5401:42d3::/66
2409:8a38:5401:42d3:4000::/66
2409:8a38:5401:42d3:8000::/66
2409:8a38:5401:42d3:c000::/66

创建/etc/docker/daemon.json，把一个子网分给docker
```json
{  
  "experimental": true,  
  "ipv6": true,  
  "ip6tables": true,  
  "fixed-cidr-v6": "2409:8a38:5401:42d3::/66"  
}
```
重启docker，完事。

#### DDNS启动问题
sleep 60 && /usr/lib/ddns/dynamic_dns_updater.sh -v 0 -S 你的ddns任务名称 -- start&

### DrawIO
docker pull fjudith/draw.io
docker run -itd --name="draw-io" --restart=always -p 8001:8080 fjudith/draw.io

### RTTY
嵌入式Linux平台

你需要自行交叉编译。

如何在 OpenWRT 中使用

安装：

1.  `opkg update`
    
2.  `opkg list | grep rtty`
    
3.  `opkg install rtty-nossl`
    

如果安装失败，你可以自己编译[3]。

配置服务器参数：


```
uci add rtty rtty
uci set rtty.@rtty[0].host='192.168.100.1'
uci set rtty.@rtty[0].port='5912'
 
```
   

你可以给你的设备自定义一个 ID。如果未指定，rtty 将使用指定的网络接口的 MAC 地址作为其 ID， 以 MAC 地址作为 ID 的格式为：`1A2A3A4A5A6A`。


```
uci set rtty.@rtty[0].id='r66s'

使用 SSL：

uci set rtty.@rtty[0].ssl='0'


保存配置并应用：

uci commit

/etc/init.d/rtty restart`=
  
```
  

如何使用

使用你的 Web 浏览器访问你的服务器: `https://your-server-host:5912`，然后点击连接按钮。

![640?wx_fmt=gif](https://img-blog.csdnimg.cn/img_convert/f96c4f9213b8f53036a850b0668b8ce7.gif)

你可以非常方便的将 rtty 嵌入到你现有的平台： `https://your-server-host:5912?id=your-id`。

自动登录: `https://your-server:5912/?id=device-id&username=device-username&password=device-password`。

上传文件和下载文件

使用快捷键打开菜单: `Ctrl+Shift+f`。

![640?wx_fmt=gif](https://img-blog.csdnimg.cn/img_convert/12ab8f896f09aaaa3a186008e5720219.gif)

![640?wx_fmt=gif](https://img-blog.csdnimg.cn/img_convert/1c6eb2af3ded8d10b1916a24ebeaf36d.gif)

远程执行命令

1.  `curl -k https://your-server:5912/cmd -d '{"devid":"test","username":"test","password":"123456","cmd":"ls","params":["/"],"env":[]}'`
    

3.  `{"Err":0,"msg":"","code":0,"stdout":"bin\ndev\netc\nlib\nmnt\noverlay\nproc\nrom\nroot\nsbin\nsys\ntmp\nusr\nvar\nwww\n","stderr":""}`

### Plex
```shel

docker run -d \
  --name=plex \
  -e PUID=1000 \
  -e PGID=1000 \
  -p 32400:32400 \
  -p 32400:32400/udp \
  -p 32469:32469 \
  -p 32469:32469/udp \
  -p 1122:22/tcp \
  -p 5353:5353/udp \
  -p 2400:2400/udp \
  -e VERSION=docker \
  
  -e PLEX_CLAIM=claim-cPEw-drzCYeh_SpYnbnx \
  -v /opt/plex/library:/config \
  -v /opt/plex/tv:/transcode \
  -v /mnt/sdb2:/movies \
  -v /mnt/sda2:/music \
  --restart unless-stopped \
  lscr.io/linuxserver/plex:latest
```

```
docker run \
-d \
--name plex \
-p 32400:32400/tcp \
-p 8324:8324/tcp \
-p 32469:32469/tcp \
-p 1900:1900/udp \
-p 32410:32410/udp \
-p 32412:32412/udp \
-p 32413:32413/udp \
-p 32414:32414/udp \
-e TZ="ShangHai" \
-e PLEX_CLAIM="<claimToken>" \
-e ADVERTISE_IP="http://127.0.0.1:32400/" \
-h <HOSTNAME> \
-v <path/to/plex/database>:/config \
-v <path/to/transcode/temp>:/transcode \
-v <path/to/media>:/data \
plexinc/pms-docker
```

To use with ARM devices, you must first build one of the ARM images locally.`docker-compose`

docker build -t plexinc/pms-docker:latest -f Dockerfile.armv8

Then you can .`docker-compose up`

### TMM
[群晖Docker：小白安装tmm刮削保姆级教程，修改host解决刮削不全 ，建立完美电影墙！_NAS存储_什么值得买 (smzdm.com)](https://post.smzdm.com/p/ar0nq5dg/)


### Zetotier
8056c2e21c000001

### IStore固件
-   默认IP http://192.168.100.1
-   默认密码：password
-   如果只有一个网口，默认的网口是 LAN；如果大于一个网口，默认 eth0 是 WAN 口，其它都是 LAN。
-   如果在 LAN 口修改 IP，或者任何修改之后导致无法连接路由器，都会导致刚才的修改被回滚。所以要修改 LAN/WAN 口 IP，可以选择强制应用，保证修改肯定生效。

### 易有云
r66s序列号 ST-raspi-42DE154F

### 相册屏蔽
安卓的话，在图片文件夹里放一个名为.nomedia的文件，系统就会屏蔽掉

### 微信推送
SCT191276TQZG8L43kdafxN4osEAMahtsa

### Jellyfish
7660f74bc3a247d39346e78c3b76ec0b
```python
//更改命名为文件名
import sqlite;
import console;
import fsys;
import fsys.config;
import process;

console.open();
//读取配置文件
var cfgPath = io.fullpath("./configs");
var cfg = fsys.config(cfgPath)
//结束jellyfin进程
process.kill("jellyfin.exe");

//打开数据库连接
var db = sqlite(cfg.config.dataBaseFilePath);

//查 - 返回数据表
var dataTable = db.getTable("SELECT `guid`,`Path`,`Name` FROM [TypedBaseItems] WHERE `Path` is not null and `type` != 'MediaBrowser.Controller.Entities.CollectionFolder' ")
for(k,v in dataTable){
	if(k=="fields") continue;
	
	var fileName = fsys.getFileName(v["Path"]);
	if(fileName == v["Name"]) continue;
	console.log(k);
	db.exec("UPDATE TypedBaseItems SET Name = @name, CleanName=@name WHERE guid = @guid;",{
		name = fileName;
		guid = v["guid"];
	});
}
 
db.close()
process.execute(cfg.config.jellyfinPath,cfg.config.params);
//execute("pause")


```

mojie结点
https://mojie.art/api/v1/client/subscribe?token=e9681dda9cc42650a1dffcefb8e8c3cb

### 青龙面板

```shell
以下代码针对的是2.12.2以及2.12.2以上版本的青龙面板
docker run -dit \
--net host \
--name ql \
--hostname ql \
--restart always \
-v /mnt/mmc1-6/ql/config:/ql/data/config \
-v /mnt/mmc1-6/ql/log:/ql/data/log \
-v /mnt/mmc1-6/ql/db:/ql/data/db \
-v /mnt/mmc1-6/ql/scripts:/ql/data/scripts \
-v /mnt/mmc1-6/ql/jbot:/ql/data/jbot \
-v /mnt/mmc1-6/ql/repo:/ql/data/repo \
-e ENABLE_HANGUP=true \
-e ENABLE_WEB_PANEL=true \
-p 5700:5700
whyour/qinglong:latest
```
--registry-mirror=http://hub-mirror.c.163.com

仓库
https://ghproxy.com/

ql repo https://ghproxy.com/https://github.com/KingRan/KR.git "jd_|jx_|jdCookie" "activity|backUp" "^jd[^_]|USER|utils|function|sign|sendNotify|ql|JDJR"

ql repo https://github.com/6dylan6/jdpro.git "jd_|jx_|jddj_" "backUp" "^jd[^_]|USER|JD|function|sendNotify"

依赖库
```shell  

docker exec -it ql bash -c "npm install -g typescript"
docker exec -it ql bash -c "npm install axios date-fns"
docker exec -it ql bash -c "npm install crypto -g"
docker exec -it ql bash -c "npm install jsdom"
docker exec -it ql bash -c "npm install png-js"
docker exec -it ql bash -c "npm install -g npm"
docker exec -it ql bash -c "pnpm i png-js"
docker exec -it ql bash -c "pip3 install requests"
docker exec -it ql bash -c "apk add --no-cache build-base g++ cairo-dev pango-dev giflib-dev && cd scripts && npm install canvas --build-from-source"
docker exec -it ql bash -c "apk add python3 zlib-dev gcc jpeg-dev python3-dev musl-dev freetype-dev"
docker exec -it ql bash -c "cd /ql/scripts/ && apk add --no-cache build-base g++ cairo-dev pango-dev giflib-dev && npm i && npm i -S ts-node typescript @types/node date-fns axios png-js canvas --build-from-source"
```

fatal: Out of memory, malloc failed (tried to allocate 524288000 bytes)
不是内存不够，是swap不够


### 下载镜像报错
error pulling image configuration: Get "https://production.cloudflare.docker.com/registry-v2/docker/

**系统时间不同步！！！**


