### HTTP

##### GET和POST请求的区别（https://blog.csdn.net/kebi007/article/details/103059900）

http协议最常见的两种方法GET和POST，这几点答案其实有几点并不准确

请求缓存：GET 会被缓存，而post不会

收藏书签：GET可以，而POST不能

保留浏览器历史记录：GET可以，而POST不能

用处：get常用于取回数据，post用于提交数据（其实都可以取回数据，在一些ui框架中会用到使用post请求数据）

安全性：post比get安全；（相对性的，因为get请求会把参数数据显示在地址栏，用户很容易看到）

请求参数：querystring 是url的一部分get、post都可以带上。 get的querystring（仅支持urlencode编码），post的参数是放在body（支持多种编码）

请求参数长度限制：get请求长度最多1024kb，post对请求数据没有限制；（实质上http请求中并没有对此标有要求，这个说法只是因为一些浏览器的限制及服务器对它的限制）

##### 误区“GET产生一个TCP数据包；POST产生两个TCP数据包。”

这一点理解起来还是有一定难度的,实际上，不论哪一种浏览器，在发送 POST 的时候都没有带 Expect 头，server 也自然不会发 100 continue。通过抓包发现，尽管会分两次，body 就是紧随在 header 后面发送的，根本不存在『等待服务器响应』这一说。
从另一个角度说，TCP 是传输层协议。别人问你应用层协议里的 GET 和 POST 有啥区别，你回答说这俩在传输层上发送数据的时候不一样，确定别人不抽你？
参考资料：https://zhuanlan.zhihu.com/p/25028045

