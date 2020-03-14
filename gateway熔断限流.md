<div id="cnblogs_post_body" class="blogpost-body ">
    <h1 id="spring-cloud-gateway">Spring Cloud Gateway</h1>
<p>Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。</p>
<p>Spring Cloud Gateway 作为 Spring Cloud 生态系统中的网关，目标是替代 Netflix Zuul，其不仅提供统一的路由方式，并且基于 Filter 链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。</p>
<p>相关概念:</p>
<ul>
<li>Route（路由）：这是网关的基本构建块。它由一个 ID，一个目标 URI，一组断言和一组过滤器定义。如果断言为真，则路由匹配。</li>
<li>Predicate（断言）：这是一个 Java 8 的 Predicate。输入类型是一个 ServerWebExchange。我们可以使用它来匹配来自 HTTP 请求的任何内容，例如 headers 或参数。</li>
<li>Filter（过滤器）：这是<code class="highlighter-rouge">org.springframework.cloud.gateway.filter.GatewayFilter</code>的实例，我们可以使用它修改请求和响应。</li>
</ul>
<p>工作流程：</p>
<p><img src="http://www.ityouknow.com/assets/images/2018/springcloud/spring-cloud-gateway.png" alt=""></p>
<p>客户端向 Spring Cloud Gateway 发出请求。如果 Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到 Gateway Web Handler。Handler 再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。</p>
<p>Spring Cloud Gateway 的特征：</p>
<ul>
<li>基于 Spring Framework 5，Project Reactor 和 Spring Boot 2.0</li>
<li>动态路由</li>
<li>Predicates 和 Filters 作用于特定路由</li>
<li>集成 Hystrix 断路器</li>
<li>集成 Spring Cloud DiscoveryClient</li>
<li>易于编写的 Predicates 和 Filters</li>
<li>限流</li>
<li>路径重写</li>
</ul>
<h1>&nbsp;快速上手</h1>
<p>引入spring-boot&nbsp;&nbsp;2.1.1.RELEASE ，springcloud的版本为&nbsp;Greenwich.M3</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">    &lt;</span><span style="color: #800000;">parent</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.boot<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-boot-starter-parent<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">version</span><span style="color: #0000ff;">&gt;</span>2.1.1.RELEASE<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">version</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">relativePath</span><span style="color: #0000ff;">/&gt;</span> <span style="color: #008000;">&lt;!--</span><span style="color: #008000;"> lookup parent from repository </span><span style="color: #008000;">--&gt;</span>
    <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">parent</span><span style="color: #0000ff;">&gt;</span>
    
    <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">properties</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">java.version</span><span style="color: #0000ff;">&gt;</span>1.8<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">java.version</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">spring-cloud.version</span><span style="color: #0000ff;">&gt;</span>Greenwich.M3<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">spring-cloud.version</span><span style="color: #0000ff;">&gt;</span>
    <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">properties</span><span style="color: #0000ff;">&gt;</span>
    
    <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependencyManagement</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependencies</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
                <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.cloud<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
                <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-cloud-dependencies<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
                <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">version</span><span style="color: #0000ff;">&gt;</span>${spring-cloud.version}<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">version</span><span style="color: #0000ff;">&gt;</span>
                <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">type</span><span style="color: #0000ff;">&gt;</span>pom<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">type</span><span style="color: #0000ff;">&gt;</span>
                <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">scope</span><span style="color: #0000ff;">&gt;</span>import<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">scope</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependencies</span><span style="color: #0000ff;">&gt;</span>
    <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependencyManagement</span><span style="color: #0000ff;">&gt;</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;添加的依赖包如下</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">        &lt;</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.cloud<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-cloud-starter-gateway<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.cloud<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-cloud-starter-netflix-hystrix<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.boot<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-boot-starter-webflux<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>org.springframework.boot<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">groupId</span><span style="color: #0000ff;">&gt;</span>
            <span style="color: #0000ff;">&lt;</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>spring-boot-starter-data-redis-reactive<span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">artifactId</span><span style="color: #0000ff;">&gt;</span>
        <span style="color: #0000ff;">&lt;/</span><span style="color: #800000;">dependency</span><span style="color: #0000ff;">&gt;<br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;注意springcloud gateway使用的web框架为webflux，和springMVC不兼容。引入的限流组件是hystrix。redis底层不再使用jedis，而是lettuce。</p>
<h1>路由断言</h1>
<p>&nbsp;接下来就是配置了，可以使用java代码硬编码配置路由过滤器，也可以使用yml配置文件配置。下面我们首先介绍配置文件配置方式</p>
<p>application.yml</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">server.port: 8082

spring:
  application:
    name: gateway
  cloud:
    gateway:
      routes:
        - id: path_route
          uri: http://localhost:8000
          order: 0
          predicates:
            - Path=/foo/**
          filters:
            - StripPrefix=1<br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;上面给出了一个根据请求路径来匹配目标uri的例子，如果请求的路径为/foo/bar，则目标uri为&nbsp;http://localhost:8000/bar。如果上面例子中没有加一个StripPrefix=1过滤器，则目标uri 为http://localhost:8000/foo/bar，StripPrefix过滤器是去掉一个路径。</p>
<p>&nbsp;其他的路由断言和过滤器使用方法请查看官网</p>
<p>&nbsp;<a href="https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.1.0.RC2/single/spring-cloud-gateway.html#gateway-how-it-works" target="_blank">https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.1.0.RC2/single/spring-cloud-gateway.html#gateway-how-it-works</a></p>
<p>&nbsp;接下来我们来看一下设计一个网关应该需要的一些功能</p>
<h1>修改接口返回报文</h1>
<p>因为网关路由的接口返回报文格式各异，并且网关也有有一些限流、认证、熔断降级的返回报文，为了统一这些报文的返回格式，网关必须要对接口的返回报文进行修改，过滤器代码如下：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.filter.global;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.nio.charset.Charset;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.response.Response;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.reactivestreams.Publisher;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.GatewayFilterChain;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.GlobalFilter;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.Ordered;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.io.buffer.DataBuffer;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.io.buffer.DataBufferFactory;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.io.buffer.DataBufferUtils;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.server.reactive.ServerHttpResponse;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.server.reactive.ServerHttpResponseDecorator;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.stereotype.Component;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.server.ServerWebExchange;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> com.alibaba.fastjson.JSON;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Flux;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Mono;

@Component
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> WrapperResponseFilter <span style="color: #0000ff;">implements</span><span style="color: #000000;"> GlobalFilter, Ordered {
    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">int</span><span style="color: #000000;"> getOrder() {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> -1 is response write filter, must be called before that</span>
        <span style="color: #0000ff;">return</span> -2<span style="color: #000000;">;
    }

    @Override
    </span><span style="color: #0000ff;">public</span> Mono&lt;Void&gt;<span style="color: #000000;"> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpResponse originalResponse </span>=<span style="color: #000000;"> exchange.getResponse();
        DataBufferFactory bufferFactory </span>=<span style="color: #000000;"> originalResponse.bufferFactory();
        ServerHttpResponseDecorator decoratedResponse </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ServerHttpResponseDecorator(originalResponse) {
            @Override
            </span><span style="color: #0000ff;">public</span> Mono&lt;Void&gt; writeWith(Publisher&lt;? <span style="color: #0000ff;">extends</span> DataBuffer&gt;<span style="color: #000000;"> body) {
                </span><span style="color: #0000ff;">if</span> (body <span style="color: #0000ff;">instanceof</span><span style="color: #000000;"> Flux) {
                    Flux</span>&lt;? <span style="color: #0000ff;">extends</span> DataBuffer&gt; fluxBody = (Flux&lt;? <span style="color: #0000ff;">extends</span> DataBuffer&gt;<span style="color: #000000;">) body;
                    </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">super</span>.writeWith(fluxBody.map(dataBuffer -&gt;<span style="color: #000000;"> {
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> probably should reuse buffers</span>
                        <span style="color: #0000ff;">byte</span>[] content = <span style="color: #0000ff;">new</span> <span style="color: #0000ff;">byte</span><span style="color: #000000;">[dataBuffer.readableByteCount()];
                        dataBuffer.read(content);
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 释放掉内存</span>
<span style="color: #000000;">                        DataBufferUtils.release(dataBuffer);
                        String rs </span>= <span style="color: #0000ff;">new</span> String(content, Charset.forName("UTF-8"<span style="color: #000000;">));
                        Response response </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Response();
                        response.setCode(</span>"1"<span style="color: #000000;">);
                        response.setMessage(</span>"请求成功"<span style="color: #000000;">);
                        response.setData(rs);
                        
                        </span><span style="color: #0000ff;">byte</span>[] newRs = JSON.toJSONString(response).getBytes(Charset.forName("UTF-8"<span style="color: #000000;">));
                        originalResponse.getHeaders().setContentLength(newRs.length);//如果不重新设置长度则收不到消息。
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> bufferFactory.wrap(newRs);
                    }));
                }
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> if body is not a flux. never got there.</span>
                <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">super</span><span style="color: #000000;">.writeWith(body);
            }
        };
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> replace response with decorator</span>
        <span style="color: #0000ff;">return</span><span style="color: #000000;"> chain.filter(exchange.mutate().response(decoratedResponse).build());
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>需要注意的是order需要小于-1，需要先于NettyWriteResponseFilter过滤器执行。</p>
<p>有了一个这样的过滤器，我们就可以统一返回报文格式了。</p>
<h1>认证</h1>
<p>以下提供一个简单的认证过滤器</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.filter.global;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.nio.charset.StandardCharsets;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.response.Response;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.GatewayFilterChain;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.GlobalFilter;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.io.buffer.DataBuffer;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.HttpStatus;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.server.reactive.ServerHttpResponse;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.stereotype.Component;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.server.ServerWebExchange;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> com.alibaba.fastjson.JSON;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Mono;

@Component
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> AuthFilter <span style="color: #0000ff;">implements</span><span style="color: #000000;"> GlobalFilter{
    @Override
    </span><span style="color: #0000ff;">public</span> Mono&lt;Void&gt;<span style="color: #000000;"> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String token </span>= exchange.getRequest().getHeaders().getFirst("token"<span style="color: #000000;">);
        </span><span style="color: #0000ff;">if</span> ("token"<span style="color: #000000;">.equals(token)) {
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> chain.filter(exchange);
        }
        ServerHttpResponse response </span>=<span style="color: #000000;"> exchange.getResponse();
        Response data </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Response();
        data.setCode(</span>"401"<span style="color: #000000;">);
        data.setMessage(</span>"非法请求"<span style="color: #000000;">);
        </span><span style="color: #0000ff;">byte</span>[] datas =<span style="color: #000000;"> JSON.toJSONString(data).getBytes(StandardCharsets.UTF_8);
        DataBuffer buffer </span>=<span style="color: #000000;"> response.bufferFactory().wrap(datas);
        response.setStatusCode(HttpStatus.UNAUTHORIZED);
        response.getHeaders().add(</span>"Content-Type", "application/json;charset=UTF-8"<span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> response.writeWith(Mono.just(buffer));
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h1>限流</h1>
<p>springcloud gateway 为我们提供了限流过滤器RequestRateLimiterGatewayFilterFactory，和限流的实现类RedisRateLimiter使用令牌桶限流。但是官方的不一定满足我们的需求，所以我们重新写一个过滤器（基本和官方一致），只是将官方的返回报文改了。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.limiter;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.nio.charset.StandardCharsets;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.Map;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.response.Response;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.GatewayFilter;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.factory.AbstractGatewayFilterFactory;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.ratelimit.KeyResolver;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.ratelimit.RateLimiter;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.route.Route;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.support.ServerWebExchangeUtils;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.core.io.buffer.DataBuffer;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.HttpStatus;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.http.server.reactive.ServerHttpResponse;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> com.alibaba.fastjson.JSON;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Mono;

</span><span style="color: #008000;">/**</span><span style="color: #008000;">
 * User Request Rate Limiter filter. See </span><span style="color: #008000; text-decoration: underline;">https://stripe.com/blog/rate-limiters</span><span style="color: #008000;"> and
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> RateLimiterGatewayFilterFactory <span style="color: #0000ff;">extends</span> AbstractGatewayFilterFactory&lt;RateLimiterGatewayFilterFactory.Config&gt;<span style="color: #000000;"> {

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">final</span> String KEY_RESOLVER_KEY = "keyResolver"<span style="color: #000000;">;

    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">final</span><span style="color: #000000;"> RateLimiter defaultRateLimiter;
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">final</span><span style="color: #000000;"> KeyResolver defaultKeyResolver;

    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> RateLimiterGatewayFilterFactory(RateLimiter defaultRateLimiter,
                                                  KeyResolver defaultKeyResolver) {
        </span><span style="color: #0000ff;">super</span>(Config.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">this</span>.defaultRateLimiter =<span style="color: #000000;"> defaultRateLimiter;
        </span><span style="color: #0000ff;">this</span>.defaultKeyResolver =<span style="color: #000000;"> defaultKeyResolver;
    }

    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> KeyResolver getDefaultKeyResolver() {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> defaultKeyResolver;
    }

    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> RateLimiter getDefaultRateLimiter() {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> defaultRateLimiter;
    }

    @SuppressWarnings(</span>"unchecked"<span style="color: #000000;">)
    @Override
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> GatewayFilter apply(Config config) {
        KeyResolver resolver </span>= (config.keyResolver == <span style="color: #0000ff;">null</span>) ?<span style="color: #000000;"> defaultKeyResolver : config.keyResolver;
        RateLimiter</span>&lt;Object&gt; limiter = (config.rateLimiter == <span style="color: #0000ff;">null</span>) ?<span style="color: #000000;"> defaultRateLimiter : config.rateLimiter;

        </span><span style="color: #0000ff;">return</span> (exchange, chain) -&gt;<span style="color: #000000;"> {
            Route route </span>=<span style="color: #000000;"> exchange.getAttribute(ServerWebExchangeUtils.GATEWAY_ROUTE_ATTR);

            </span><span style="color: #0000ff;">return</span> resolver.resolve(exchange).flatMap(key -&gt;
                    <span style="color: #008000;">//</span><span style="color: #008000;"> TODO: if key is empty?</span>
                    limiter.isAllowed(route.getId(), key).flatMap(response -&gt;<span style="color: #000000;"> {

                        </span><span style="color: #0000ff;">for</span> (Map.Entry&lt;String, String&gt;<span style="color: #000000;"> header : response.getHeaders().entrySet()) {
                            exchange.getResponse().getHeaders().add(header.getKey(), header.getValue());
                        }

                        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (response.isAllowed()) {
                            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> chain.filter(exchange);
                        }
                        ServerHttpResponse rs </span>=<span style="color: #000000;"> exchange.getResponse();
                        Response data </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Response();
                        data.setCode(</span>"101"<span style="color: #000000;">);
                        data.setMessage(</span>"访问过快"<span style="color: #000000;">);
                        </span><span style="color: #0000ff;">byte</span>[] datas =<span style="color: #000000;"> JSON.toJSONString(data).getBytes(StandardCharsets.UTF_8);
                        DataBuffer buffer </span>=<span style="color: #000000;"> rs.bufferFactory().wrap(datas);
                        rs.setStatusCode(HttpStatus.UNAUTHORIZED);
                        rs.getHeaders().add(</span>"Content-Type", "application/json;charset=UTF-8"<span style="color: #000000;">);
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> rs.writeWith(Mono.just(buffer));
                    }));
        };
    }

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> Config {
        </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> KeyResolver keyResolver;
        </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> RateLimiter rateLimiter;
        </span><span style="color: #0000ff;">private</span> HttpStatus statusCode =<span style="color: #000000;"> HttpStatus.TOO_MANY_REQUESTS;

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> KeyResolver getKeyResolver() {
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> keyResolver;
        }

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> Config setKeyResolver(KeyResolver keyResolver) {
            </span><span style="color: #0000ff;">this</span>.keyResolver =<span style="color: #000000;"> keyResolver;
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> RateLimiter getRateLimiter() {
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> rateLimiter;
        }

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> Config setRateLimiter(RateLimiter rateLimiter) {
            </span><span style="color: #0000ff;">this</span>.rateLimiter =<span style="color: #000000;"> rateLimiter;
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> HttpStatus getStatusCode() {
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> statusCode;
        }

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> Config setStatusCode(HttpStatus statusCode) {
            </span><span style="color: #0000ff;">this</span>.statusCode =<span style="color: #000000;"> statusCode;
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">;
        }
    }

}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;然后限流必须要有一个key，根据什么来进行限流，ip，接口，或者用户来进行限流，所以我们自定义一个KeyResolver</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.limiter;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.ratelimit.KeyResolver;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.server.ServerWebExchange;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> com.alibaba.fastjson.JSON;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Mono;

</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> CustomKeyResolver <span style="color: #0000ff;">implements</span><span style="color: #000000;"> KeyResolver {

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">final</span> String BEAN_NAME = "customKeyResolver"<span style="color: #000000;">;

    @Override
    </span><span style="color: #0000ff;">public</span> Mono&lt;String&gt;<span style="color: #000000;"> resolve(ServerWebExchange exchange) {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> Mono.just(getKey(exchange));
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> exchange
     * </span><span style="color: #808080;">@return</span>
     <span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> String getKey(ServerWebExchange exchange) {
        
        LimitKey limitKey </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> LimitKey();
        
        limitKey.setApi(exchange.getRequest().getPath().toString());
        limitKey.setBiz(exchange.getRequest().getQueryParams().getFirst(</span>"biz"<span style="color: #000000;">));

        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> JSON.toJSONString(limitKey);
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;最后RedisRateLimiter我们也需要重写，因为不支持多级限流，原生的只会判断一个key。代码如下：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * This uses a basic token bucket algorithm and relies on the fact that Redis scripts
     * execute atomically. No other operations can run between fetching the count and
     * writing the new count.
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span> Mono&lt;Response&gt;<span style="color: #000000;"> isAllowed(String routeId, String id) {
        </span><span style="color: #0000ff;">if</span> (!<span style="color: #0000ff;">this</span><span style="color: #000000;">.initialized.get()) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalStateException("RedisRateLimiter is not initialized"<span style="color: #000000;">);
        }

        LimitConfig limitConfig </span>=<span style="color: #000000;"> getLimitConfig(routeId);
        
        </span><span style="color: #0000ff;">if</span> (limitConfig == <span style="color: #0000ff;">null</span> || limitConfig.getTokenConfig().size()==0<span style="color: #000000;">) {
            </span><span style="color: #0000ff;">return</span> Mono.just(<span style="color: #0000ff;">new</span> Response(<span style="color: #0000ff;">true</span>,<span style="color: #0000ff;">null</span><span style="color: #000000;">));
        }

        Map</span>&lt;String, Config&gt; conf =<span style="color: #000000;"> limitConfig.getTokenConfig();
        
        LimitKey limitKey </span>= JSON.parseObject(id, LimitKey.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">api限流</span>
        String api =<span style="color: #000000;"> limitKey.getApi();
        Config apiConf </span>=<span style="color: #000000;"> conf.get(api);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">业务方限流</span>
        String biz =<span style="color: #000000;"> limitKey.getBiz();
        Config bizConf </span>=<span style="color: #000000;"> conf.get(biz);
        
        </span><span style="color: #0000ff;">if</span> (apiConf!=<span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">return</span> isSingleAllow(api,routeId,apiConf).flatMap(res -&gt;<span style="color: #000000;"> {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (res.isAllowed()) {
                    </span><span style="color: #0000ff;">if</span>(bizConf!=<span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> isSingleAllow(biz, routeId, bizConf);
                    }</span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                        </span><span style="color: #0000ff;">return</span> Mono.just(<span style="color: #0000ff;">new</span> Response(<span style="color: #0000ff;">true</span>,<span style="color: #0000ff;">new</span> HashMap&lt;&gt;<span style="color: #000000;">()));
                    }
                }</span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> Mono.just(res);
                }
            } );
        }</span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span> (bizConf!=<span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> isSingleAllow(biz, routeId, bizConf);
            }</span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #0000ff;">return</span> Mono.just(<span style="color: #0000ff;">new</span> Response(<span style="color: #0000ff;">true</span>,<span style="color: #0000ff;">new</span> HashMap&lt;&gt;<span style="color: #000000;">()));
            }
        }
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 单级限流
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> api
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> routeId
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> apiConf
     * </span><span style="color: #808080;">@return</span> 
     <span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> Mono&lt;Response&gt;<span style="color: #000000;"> isSingleAllow(String key, String routeId, Config config) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> How many requests per second do you want a user to be allowed to do?</span>
        <span style="color: #0000ff;">int</span> replenishRate =<span style="color: #000000;"> config.getReplenishRate();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> How much bursting do you want to allow?</span>
        <span style="color: #0000ff;">int</span> burstCapacity =<span style="color: #000000;"> config.getBurstCapacity();

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            List</span>&lt;String&gt; keys = getKeys(routeId+"$"+<span style="color: #000000;">key);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> The arguments to the LUA script. time() returns unixtime in seconds.</span>
            List&lt;String&gt; scriptArgs = Arrays.asList(replenishRate + "", burstCapacity + ""<span style="color: #000000;">,
                    Instant.now().getEpochSecond() </span>+ "", "1"<span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> allowed, tokens_left = redis.eval(SCRIPT, keys, args)</span>
            Flux&lt;List&lt;Long&gt;&gt; flux = <span style="color: #0000ff;">this</span>.redisTemplate.execute(<span style="color: #0000ff;">this</span><span style="color: #000000;">.script, keys, scriptArgs);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> .log("redisratelimiter", Level.FINER);</span>
            <span style="color: #0000ff;">return</span> flux.onErrorResume(throwable -&gt; Flux.just(Arrays.asList(1L, -1L<span style="color: #000000;">)))
                    .reduce(</span><span style="color: #0000ff;">new</span> ArrayList&lt;Long&gt;(), (longs, l) -&gt;<span style="color: #000000;"> {
                        longs.addAll(l);
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> longs;
                    }) .map(results </span>-&gt;<span style="color: #000000;"> {
                        </span><span style="color: #0000ff;">boolean</span> allowed = results.get(0) == 1L<span style="color: #000000;">;
                        Long tokensLeft </span>= results.get(1<span style="color: #000000;">);

                        Response response </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Response(allowed, getHeaders(config, tokensLeft));

                        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (log.isDebugEnabled()) {
                            log.debug(</span>"response: " +<span style="color: #000000;"> response);
                        }
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> response;
                    });
        }
        </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            </span><span style="color: #008000;">/*</span><span style="color: #008000;">
             * We don't want a hard dependency on Redis to allow traffic. Make sure to set
             * an alert so you know if this is happening too much. Stripe's observed
             * failure rate is 0.01%.
             </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            log.error(</span>"Error determining if user allowed from redis"<span style="color: #000000;">, e);
        }
        </span><span style="color: #0000ff;">return</span> Mono.just(<span style="color: #0000ff;">new</span> Response(<span style="color: #0000ff;">true</span>, getHeaders(config, -1L<span style="color: #000000;">)));
    }

    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> LimitConfig getLimitConfig(String routeId) {
        Map</span>&lt;String, LimitConfig&gt; map = <span style="color: #0000ff;">new</span> HashMap&lt;&gt;<span style="color: #000000;">();
        LimitConfig limitConfig </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> LimitConfig();
        limitConfig.setRouteId(</span>"rateLimit_route"<span style="color: #000000;">);
        Map</span>&lt;String, Config&gt; tokenMap = <span style="color: #0000ff;">new</span> HashMap&lt;&gt;<span style="color: #000000;">();
        Config apiConfig </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Config();
        apiConfig.setBurstCapacity(</span>5<span style="color: #000000;">);
        apiConfig.setReplenishRate(</span>5<span style="color: #000000;">);
        
        Config bizConfig </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Config();
        bizConfig.setBurstCapacity(</span>1<span style="color: #000000;">);
        bizConfig.setReplenishRate(</span>1<span style="color: #000000;">);
        
        tokenMap.put(</span>"/hello/rateLimit"<span style="color: #000000;">, apiConfig);
        tokenMap.put(</span>"jieyin"<span style="color: #000000;">, bizConfig);
        limitConfig.setTokenConfig(tokenMap);
        map.put(</span>"rateLimit_route"<span style="color: #000000;">, limitConfig);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> limitConfig;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;如上的代码是写死的，但是我们可以根据我们的业务需求设计一个自定义key，自定义令牌桶容量和速率的限流规则。</p>
<p>bean配置和yml配置如下</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">    @Bean
    @Primary
    </span><span style="color: #0000ff;">public</span> CustomRedisRateLimiter customRedisRateLimiter(ReactiveRedisTemplate&lt;String, String&gt;<span style="color: #000000;"> redisTemplate,
                                             @Qualifier(RedisRateLimiter.REDIS_SCRIPT_NAME) RedisScript</span>&lt;List&lt;Long&gt;&gt;<span style="color: #000000;"> redisScript,
                                             Validator validator) {
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> CustomRedisRateLimiter(redisTemplate, redisScript, validator);
    }
    
    @Bean
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> RateLimiterGatewayFilterFactory rateLimiterGatewayFilterFactory(CustomRedisRateLimiter customRedisRateLimiter, CustomKeyResolver customKeyResolver) {
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RateLimiterGatewayFilterFactory(customRedisRateLimiter, customKeyResolver);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>server.port: 8082<span style="color: #000000;">

spring:
  application:
    name: </span><span style="color: #000000;">gateway
  redis:
      host: localhost
      port: </span>6379<span style="color: #000000;">
      password: 123456
  cloud:
    gateway:
      routes:
        </span>-<span style="color: #000000;"> id: rateLimit_route
          uri: http:</span><span style="color: #008000;">//</span><span style="color: #008000;">localhost:8000</span>
          order: 0<span style="color: #000000;">
          predicates:
            </span>- Path=/foo<span style="color: #008000;">/**</span><span style="color: #008000;">
          filters:
            - StripPrefix=1
            - name: RateLimiter</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h1>熔断</h1>
<p>&nbsp; &nbsp; &nbsp; 当下游接口负载很大，或者接口不通等其他原因导致超时，如果接口不熔断的话将会影响到下游接口得不到喘息，网关也会因为超时连接一直挂起，很可能因为一个子系统的问题导致整个系统的雪崩。所以我们的网关需要设计熔断，当因为熔断器打开时，网关将返回一个降级的应答。</p>
<p>熔断配置如下：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>server.port: 8082<span style="color: #000000;">

spring:
  application:
    name: </span><span style="color: #000000;">gateway
  redis:
      host: localhost
      port: </span>6379<span style="color: #000000;">
      password: 123456
  cloud:
    gateway:
      routes:
        </span>-<span style="color: #000000;"> id: rateLimit_route
          uri: http:</span><span style="color: #008000;">//</span><span style="color: #008000;">localhost:8000</span>
          order: 0<span style="color: #000000;">
          predicates:
            </span>- Path=/foo<span style="color: #008000;">/**</span><span style="color: #008000;">
          filters:
            - StripPrefix=1
            - name: RateLimiter
            - name: Hystrix
              args:
                name: fallbackcmd
                fallbackUri: forward:/fallback<br></span></pre>
<p>&nbsp; hystrix.command.fallbackcmd.execution.isolation.thread.timeoutInMilliseconds: 5000</p>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.controller;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.response.Response;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.GetMapping;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.RestController;

@RestController
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> FallbackController {

    @GetMapping(</span>"/fallback"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> Response fallback() {
        Response response </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Response();
        response.setCode(</span>"100"<span style="color: #000000;">);
        response.setMessage(</span>"服务暂时不可用"<span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> response;
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>注意需要设置commandKey的超时时间。其他的hystrix配置请访问<a class="link" href="https://github.com/Netflix/Hystrix/wiki/Configuration" target="_top">Hystrix wiki</a>.</p>
<h1>动态配置路由和过滤器</h1>
<p>最后我们来看一下如何动态配置路由和过滤器。</p>
<p>定义路由实体</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/**</span><span style="color: #008000;">
 * Gateway的路由定义模型
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> GatewayRouteDefinition {

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 路由的Id
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> String id;

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 路由断言集合配置
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> List&lt;GatewayPredicateDefinition&gt; predicates = <span style="color: #0000ff;">new</span> ArrayList&lt;&gt;<span style="color: #000000;">();

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 路由过滤器集合配置
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> List&lt;GatewayFilterDefinition&gt; filters = <span style="color: #0000ff;">new</span> ArrayList&lt;&gt;<span style="color: #000000;">();

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 路由规则转发的目标uri
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> String uri;

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 路由执行的顺序
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">int</span> order = 0<span style="color: #000000;">;</span><span style="color: #000000;">
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>路由断言实体</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/**</span><span style="color: #008000;">
 * 路由断言定义模型
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> GatewayPredicateDefinition {

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 断言对应的Name
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> String name;

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 配置的断言规则
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> Map&lt;String, String&gt; args = <span style="color: #0000ff;">new</span> LinkedHashMap&lt;&gt;();<br>}</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>过滤器实体</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/**</span><span style="color: #008000;">
 * 过滤器定义模型
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> GatewayFilterDefinition {

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Filter Name
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> String name;

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 对应的路由规则
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> Map&lt;String, String&gt; args = <span style="color: #0000ff;">new</span> LinkedHashMap&lt;&gt;<span style="color: #000000;">();
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>路由增删改controller</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.controller;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.net.URI;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.ArrayList;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.Arrays;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.HashMap;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.List;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.Map;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.model.GatewayFilterDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.model.GatewayPredicateDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.model.GatewayRouteDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.route.DynamicRouteServiceImpl;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.beans.factory.annotation.Autowired;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.filter.FilterDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.handler.predicate.PredicateDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.route.RouteDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.util.CollectionUtils;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.GetMapping;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.PathVariable;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.PostMapping;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.RequestBody;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.RequestMapping;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.bind.annotation.RestController;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.util.UriComponentsBuilder;

@RestController
@RequestMapping(</span>"/route"<span style="color: #000000;">)
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> RouteController {

    @Autowired
    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> DynamicRouteServiceImpl dynamicRouteService;

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 增加路由
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> gwdefinition
     * </span><span style="color: #808080;">@return</span>
     <span style="color: #008000;">*/</span><span style="color: #000000;">
    @PostMapping(</span>"/add"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> String add(@RequestBody GatewayRouteDefinition gwdefinition) {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            RouteDefinition definition </span>=<span style="color: #000000;"> assembleRouteDefinition(gwdefinition);
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">.dynamicRouteService.add(definition);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            e.printStackTrace();
        }
        </span><span style="color: #0000ff;">return</span> "succss"<span style="color: #000000;">;
    }

    @GetMapping(</span>"/delete/{id}"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> String delete(@PathVariable String id) {
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">.dynamicRouteService.delete(id);
    }

    @PostMapping(</span>"/update"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> String update(@RequestBody GatewayRouteDefinition gwdefinition) {
        RouteDefinition definition </span>=<span style="color: #000000;"> assembleRouteDefinition(gwdefinition);
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">this</span><span style="color: #000000;">.dynamicRouteService.update(definition);
    }

    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> RouteDefinition assembleRouteDefinition(GatewayRouteDefinition gwdefinition) {
        RouteDefinition definition </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> RouteDefinition();
        List</span>&lt;PredicateDefinition&gt; pdList=<span style="color: #0000ff;">new</span> ArrayList&lt;&gt;<span style="color: #000000;">();
        definition.setId(gwdefinition.getId());
        List</span>&lt;GatewayPredicateDefinition&gt; gatewayPredicateDefinitionList=<span style="color: #000000;">gwdefinition.getPredicates();
        </span><span style="color: #0000ff;">for</span><span style="color: #000000;"> (GatewayPredicateDefinition gpDefinition: gatewayPredicateDefinitionList) {
            PredicateDefinition predicate </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> PredicateDefinition();
            predicate.setArgs(gpDefinition.getArgs());
            predicate.setName(gpDefinition.getName());
            pdList.add(predicate);
        }
        
        List</span>&lt;GatewayFilterDefinition&gt; gatewayFilterDefinitions =<span style="color: #000000;"> gwdefinition.getFilters();
        List</span>&lt;FilterDefinition&gt; filterList = <span style="color: #0000ff;">new</span> ArrayList&lt;&gt;<span style="color: #000000;">();
        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">CollectionUtils.isEmpty(gatewayFilterDefinitions)) {
            </span><span style="color: #0000ff;">for</span><span style="color: #000000;"> (GatewayFilterDefinition gatewayFilterDefinition : gatewayFilterDefinitions) {
                FilterDefinition filterDefinition </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> FilterDefinition();
                filterDefinition.setName(gatewayFilterDefinition.getName());
                filterDefinition.setArgs(gatewayFilterDefinition.getArgs());
                filterList.add(filterDefinition);
            }
        }
        definition.setPredicates(pdList);
        definition.setFilters(filterList);
        URI uri </span>=<span style="color: #000000;"> UriComponentsBuilder.fromHttpUrl(gwdefinition.getUri()).build().toUri();
        definition.setUri(uri);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> definition;
    }</span><span style="color: #000000;">
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;动态路由service&nbsp;</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">package</span><span style="color: #000000;"> org.gateway.route;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.net.URI;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.Arrays;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.HashMap;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> java.util.Map;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.model.GatewayPredicateDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.gateway.model.GatewayRouteDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.beans.factory.annotation.Autowired;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.event.RefreshRoutesEvent;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.handler.predicate.PredicateDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.route.RouteDefinition;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.cloud.gateway.route.RouteDefinitionWriter;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.context.ApplicationEventPublisher;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.context.ApplicationEventPublisherAware;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.stereotype.Service;
</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> org.springframework.web.util.UriComponentsBuilder;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> com.alibaba.fastjson.JSON;

</span><span style="color: #0000ff;">import</span><span style="color: #000000;"> reactor.core.publisher.Mono;

@Service
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> DynamicRouteServiceImpl <span style="color: #0000ff;">implements</span><span style="color: #000000;"> ApplicationEventPublisherAware {

    @Autowired
    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> RouteDefinitionWriter routeDefinitionWriter;

    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> ApplicationEventPublisher publisher;


    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 增加路由
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> definition
     * </span><span style="color: #808080;">@return</span>
     <span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span><span style="color: #000000;"> String add(RouteDefinition definition) {
        routeDefinitionWriter.save(Mono.just(definition)).subscribe();
        </span><span style="color: #0000ff;">this</span>.publisher.publishEvent(<span style="color: #0000ff;">new</span> RefreshRoutesEvent(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
        </span><span style="color: #0000ff;">return</span> "success"<span style="color: #000000;">;
    }


    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 更新路由
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> definition
     * </span><span style="color: #808080;">@return</span>
     <span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span><span style="color: #000000;"> String update(RouteDefinition definition) {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">this</span><span style="color: #000000;">.routeDefinitionWriter.delete(Mono.just(definition.getId()));
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            </span><span style="color: #0000ff;">return</span> "update fail,not find route  routeId: "+<span style="color: #000000;">definition.getId();
        }
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            routeDefinitionWriter.save(Mono.just(definition)).subscribe();
            </span><span style="color: #0000ff;">this</span>.publisher.publishEvent(<span style="color: #0000ff;">new</span> RefreshRoutesEvent(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
            </span><span style="color: #0000ff;">return</span> "success"<span style="color: #000000;">;
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            </span><span style="color: #0000ff;">return</span> "update route  fail"<span style="color: #000000;">;
        }


    }
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * 删除路由
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> id
     * </span><span style="color: #808080;">@return</span>
     <span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span><span style="color: #000000;"> String delete(String id) {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">this</span><span style="color: #000000;">.routeDefinitionWriter.delete(Mono.just(id));
            </span><span style="color: #0000ff;">return</span> "delete success"<span style="color: #000000;">;
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            e.printStackTrace();
            </span><span style="color: #0000ff;">return</span> "delete fail"<span style="color: #000000;">;
        }

    }

    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        </span><span style="color: #0000ff;">this</span>.publisher =<span style="color: #000000;"> applicationEventPublisher;
    }</span><span style="color: #000000;">
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<pre><span>&nbsp;上面 </span>routeDefinitionWriter的实现默认是InMemoryRouteDefinitionRepository，将路由存在内存中，我们可以自己实现一个将路由存在redis中的repository。</pre>
<pre>this.publisher.publishEvent(new RefreshRoutesEvent(this));则会将CachingRouteLocator中的路由缓存清空。</pre>
<pre><br>以上只是springcloud gateway支持的一小部分功能。</pre>
</div>