### 记录一次java后台的websocket无法获取小程序连接携带的header参数

```java
package com.joofeel.miniprogram.config.websocket;

import com.joofeel.miniprogram.utils.TokenUtil;
import org.apache.commons.lang3.StringUtils;
import org.springframework.http.server.ServerHttpRequest;
import org.springframework.http.server.ServerHttpResponse;
import org.springframework.http.server.ServletServerHttpRequest;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.server.support.HttpSessionHandshakeInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.util.Map;

public class MyWebSocketSessionHandshakeInterceptor extends HttpSessionHandshakeInterceptor {

    @Override
    public boolean beforeHandshake(ServerHttpRequest request, ServerHttpResponse response, WebSocketHandler wsHandler, Map<String, Object> attributes) throws Exception {
        if (getSession(request) != null) {
            ServletServerHttpRequest servletRequest = (ServletServerHttpRequest) request;
            HttpServletRequest httpServletRequest = servletRequest.getServletRequest();
            //获取到的token有时候为空
            String token = httpServletRequest.getHeader("token");
            System.out.println("当前连接websocket的token=" + token);
            if (StringUtils.isEmpty(token)) {
                attributes.put("userId", "4");
            }else {
                attributes.put("userId", TokenUtil.getCurrentTokenUid(token)+"");
            }
        }
        super.beforeHandshake(request, response, wsHandler, attributes);
        return true;
    }

    private HttpSession getSession(ServerHttpRequest request) {
        if (request instanceof ServletServerHttpRequest) {
            ServletServerHttpRequest serverRequest = (ServletServerHttpRequest) request;
            return serverRequest.getServletRequest().getSession();
        }
        return null;
    }
}

```

原因：微信开发者平台有人说是因为小程序开发者工具的问题。（https://developers.weixin.qq.com/community/develop/doc/00008a1465c558e1c479a2fa75ec00?jumpto=comment&commentid=000c4640238240f4ca79e133c5b0）

解决：暂时不知道解决方案。微信开发者平台也有人提问，但是一直没有得到回答。