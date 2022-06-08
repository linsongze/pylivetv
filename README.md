实现思路来自https://github.com/linsongze/livetv
主要是，用golang运行youtube-dl只能够用shell的方式调用，出现错误的概率比较高，而且很慢，现在用python实现，则可以用库的形式进行调用，在获取m3u8链接上稳定了很多。

### 运行要求 3.7以上
```
1、安装依赖
pip install requests requests-async sanic youtube-dl

2、运行
python ytb_proxy.py -p 端口（不加就默认5000）


***添加修改频道的话，，修改ytb_proxy.py里面的initURL函数
*** http://ip:port/ytb.m3u #获得m3u直播列表

```

### 在cloudflare workers中转发 [进阶版workers脚本]([https://www.runoob.com](https://github.com/linsongze/pylivetv/blob/main/cf_better.js))
由于worker.dev域名已经在国内被阻断，必须自己加托管域名进行转发
```
addEventListener("fetch", event => {
  let url = new URL(event.request.url);
  url.hostname = "你真正部署程序的域名 没有http://"; //改成你想反代的网站
  url.protocol = 'http'; //协议如果没ssl 就http
  let request = new Request(url, event.request);

  event.respondWith(
    fetch(request, {
      headers: {
          "baseUrl":"cf路由转发的域名URL http://域名",
        'Referer': 'https://www.cloudflare.com/', //referer头,不必修改
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' //
      }
    })
  );
});

```
