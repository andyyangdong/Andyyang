SpringMVC 
===


工作原理如下   [链接](https://juejin.im/post/5b38a619f265da5999115e30)

[流程图](https://user-gold-cdn.xitu.io/2018/7/1/164554b676de2a77?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

>*   用户发送的请求首先会到达`DispatcherServlet`，并由它进行处理；
>*   `DispatcherServlet`先通过`HandlerMapping`找到该请求对应的`HandlerExecutionChain`（包含一个`Handler`处理器、多个`HandlerInterceptor`拦截器），通过这种策略模式，很容易添加新的映射策略；
>*   然后`DispatcherServlet`继续将请求发送给`HandlerAdapter`，`HandlerAdapter`会把处理器包装为适配器，从而支持多种类型的处理器，即适配器设计模式，从而很容易支持很多类型的处理器；
>*   `HandlerAdapter`将会根据适配的结果调用真正的处理器的功能处理方法，完成功能处理，并返回一个`ModelAndView`对象（包含模型数据、逻辑视图名）；
>*   再由`DispatcherServlet`根据`ModelAndView`找到对应的`ViewResolver`，并由它把逻辑视图名解析为具体的`View`，通过这种策略模式，很容易更换其他视图技术；
>*   接下来会对`View`进行渲染，`View`会根据传进来的`Model`模型数据进行渲染，Model实际是一个Map数据结构，因此很容易支持其他视图技术；
>*   返回控制权给`DispatcherServlet`，由`DispatcherServlet`返回响应给用户，到此一个流程结束。


适配器：SimpleControllerhandlerAdapter  控制器：HttpRequestHandlerAdapter

### 拦截器和过滤器的区别

>*   拦截器是基于java的反射机制的，过滤器是基于函数回调；
>*   拦截器不依赖于servlet容器，过滤器依赖于servlet容器，因为过滤器是Servlet规范规定的，只用于Web程序中，而拦截器可以用在Appliaction和Swing等中；
>*   在Action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次；
>*   拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑;
>*   过滤器是在请求进入容器后，但请求进入servlet之前进行预处理的。请求结束返回也是，是在servlet处理完后，返回给前端之前



