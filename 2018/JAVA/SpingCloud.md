SpingCloud 服务治理
===
Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署
前端app或者网页通过zuul来调用后端的服务，如果包含静态资源也可以使用nginx做一下代理转发

[spring Cloud 核心组件](https://github.com/ityouknow/spring-cloud-examples)
*   [注册中心 Eureka](http://www.ityouknow.com/springcloud/2017/05/10/springcloud-eureka.html)
    *   生产中我们可能需要三台或者大于三台的注册中心来保证服务的稳定性，配置的原理其实都一样，将注册中心分别指向其它的注册中心。
    *   不同的服务器之间通过异步模式互相复制各自的状态。
    *   Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持  
*   [服务注册与发现](http://blog.didispace.com/springcloud6/)
    *   client初始时为java提供的，如果是Node.js、Net等语言，也有其对应的client，按规范将接口的自定义协议注册到注册中心即可。
    *   客户端向注册中心注册自身提供的服务，并周期性地发送心跳来更新它的服务租约。当服务关闭时，向Eureka Server取消租约。
*   [熔断器 Hystrix](http://www.ityouknow.com/springcloud/2017/05/16/springcloud-hystrix.html)
    *   容错管理组件。帮助服务依赖中出现延迟或故障时，提供强大的容错能力。
*   [服务网关 Zuul](https://mp.weixin.qq.com/s/5PQ9iyPfYCEcJ5W7q0T2oQ)
    *   通过一个API网关根据请求的url，路由到相应的服务
    *   [服务网关zuul初级篇](http://www.ityouknow.com/springcloud/2017/06/01/gateway-service-zuul.html)
    *   [Netflix正式开源其API网关Zuul 2](https://mp.weixin.qq.com/s/wh_7duo4God8_9awPJBJbQ)
*   [客户端负载均衡 Ribbon](http://blog.didispace.com/spring-cloud-starter-dalston-2-2/)
    *   通过在客户端中配置ribbonServerList来设置服务端列表去轮询访问以达到均衡负载的作用。
*   [客户端负载均衡 Feign](http://blog.didispace.com/spring-cloud-starter-dalston-2-3/)
    *   Feign是基于Ribbon实现的，所以它自带了客户端负载均衡功能，也可以通过Ribbon的IRule进行策略扩展。
    *   创建接口并用注解来配置它即可完成对Web服务接口的绑定。它具备可插拔的注解支持，包括Feign注解、JAX-RS注解。它也支持可插拔的编码器和解码器。Spring Cloud Feign还扩展了对Spring MVC注解的支持，同时还整合了Ribbon和Eureka来提供均衡负载的HTTP客户端实现。
*   config
    *   分布式配置管理

