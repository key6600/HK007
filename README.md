 1.注册Heroku账号：https://signup.heroku.com/login?redirect

 2.需要反代理的可配合下面的服务商进行套接

 申请Freenom域名：https://www.freenom.com/

 注册CloudFlare Workers ：https://www.cloudflare.com/
 
 
## v2ray-heroku

[![](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/key6600/HK007.git)https://heroku.com/deploy?template=

## 注意事项
### heroku上部署v2ray
- [√] Heroku五合一协议共存项目：Xray内核，支持vless-ws，vmess-ws，trojan-ws，shadowsocks-ws，socks5-ws
- [√] 使用v2ray最新版构建



### 环境变量说明

|  名称 | 值  | 说明  |
| ------------ | ------------ | ------------ |
|  PROTOCOL |  vless<br>vmess（可选） |  协议：vless+ws+tls或vmess+ws+tls |默认vless
|  UUID |  [uuid在线生成器](https://www.uuidgenerator.net "uuid在线生成器") | 用户主ID  |


### CloudFlare Workers反代代码
<details>
<summary>CloudFlare Workers单账户反代代码</summary>

```js
addEventListener(
    "fetch",event => {
        let url=new URL(event.request.url);
        url.hostname="appname.herokuapp.com";
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
</details>

<details>
<summary>CloudFlare Workers单双日轮换反代代码</summary>

```js
const SingleDay = 'app0.herokuapp.com'
const DoubleDay = 'app1.herokuapp.com'
addEventListener(
    "fetch",event => {
    
        let nd = new Date();
        if (nd.getDate()%2) {
            host = SingleDay
        } else {
            host = DoubleDay
        }
        
        let url=new URL(event.request.url);
        url.hostname=host;
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
</details>

<details>
<summary>CloudFlare Workers每五天轮换一遍式反代代码</summary>

```js
const Day0 = 'app0.herokuapp.com'
const Day1 = 'app1.herokuapp.com'
const Day2 = 'app2.herokuapp.com'
const Day3 = 'app3.herokuapp.com'
const Day4 = 'app4.herokuapp.com'
addEventListener(
    "fetch",event => {
    
        let nd = new Date();
        let day = nd.getDate() % 5;
        if (day === 0) {
            host = Day0
        } else if (day === 1) {
            host = Day1
        } else if (day === 2) {
            host = Day2
        } else if (day === 3){
            host = Day3
        } else if (day === 4){
            host = Day4
        } else {
            host = Day1
        }
        
        let url=new URL(event.request.url);
        url.hostname=host;
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
</details>

<details>
<summary>CloudFlare Workers一周轮换反代代码</summary>

```js
const Day0 = 'app0.herokuapp.com'
const Day1 = 'app1.herokuapp.com'
const Day2 = 'app2.herokuapp.com'
const Day3 = 'app3.herokuapp.com'
const Day4 = 'app4.herokuapp.com'
const Day5 = 'app5.herokuapp.com'
const Day6 = 'app6.herokuapp.com'
addEventListener(
    "fetch",event => {
    
        let nd = new Date();
        let day = nd.getDay();
        if (day === 0) {
            host = Day0
        } else if (day === 1) {
            host = Day1
        } else if (day === 2) {
            host = Day2
        } else if (day === 3){
            host = Day3
        } else if (day === 4) {
            host = Day4
        } else if (day === 5) {
            host = Day5
        } else if (day === 6) {
            host = Day6
        } else {
            host = Day1
        }
        
        let url=new URL(event.request.url);
        url.hostname=host;
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
</details>

<details>
<summary>CloudFlare Pages单账户反代代码</summary>

```js
export default {
  async fetch(request, env) {
    let url = new URL(request.url);
    if (url.pathname.startsWith('/')) {
      url.hostname = 'app0.herokuapp.com'
      let new_request = new Request(url, request);
      return fetch(new_request);
    }
    return env.ASSETS.fetch(request);
  },
};
```
</details>

<details>
<summary>CloudFlare Pages单双日轮换反代代码</summary>

```js
export default {
  async fetch(request, env) {
    const day1 = 'app0.herokuapp.com'
    const day2 = 'app1.herokuapp.com'
    let url = new URL(request.url);
    if (url.pathname.startsWith('/')) {
      let day = new Date()
      if (day.getDay() % 2) {
        url.hostname = day1
      } else {
        url.hostname = day2
      }
      let new_request = new Request(url, request);
      return fetch(new_request);
    }
    return env.ASSETS.fetch(request);
  },
};
```
</details>

### 客户端配置

```

IOS端小火箭就可以通吃，安卓端推荐V2rayNG或搭配Kitsunebi

协议：(vless/vmess/trojan)-ws

地址：app.heroku.com（自选IP/域名）

端口：443

用户ID/密码：自定义UUID

传输协议：ws

伪装host：app.heroku.com（workers或pages反代/自定义域）

路径path：/自定义UUID-协议开头两小写字母

传输安全：tls

SNI：app.heroku.com（workers或pages反代/自定义域）

其他设置保持默认即可！



shadowsocks-ws与socks5-ws推荐用Kitsunebi，配置简单，不需要plugin插件

协议：(shadowsocks/socks5)-ws

地址：app.heroku.com（自选IP/域名）

端口：443

shadowsocks密码：自定义UUID

shadowsocks加密方式：chacha20-ietf-poly1305(默认)

socks5用户名：空

socks5密码：空

传输协议：ws

伪装host：app.heroku.com（workers或pages反代/自定义域）

路径path：/自定义UUID-协议开头两小写字母

传输安全：tls

SNI(证书域名)：app.heroku.com（workers或pages反代/自定义域）

其他设置保持默认即可！


```



特别鸣谢：Misaka、rptec、DaoChen6、甬哥，等大佬提供脚本、教程和思路！
