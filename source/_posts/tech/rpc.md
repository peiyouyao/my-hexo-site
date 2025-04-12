---
title: PyRPC Framework
date: 2025-02-01 00:00:00
tags: rpc
categories: tech
---

---

> In this blog, I will give an elaborate instruction to the process of how to construct a whole RPC framework with Java. 
>
> It is also a record of project [demo-pyrpc](https://github.com/PerryYao-GitHub/demo-pyrpc) and [demo-rpc](https://github.com/PerryYao-GitHub/demo-rpc), the former one is an update on the last one.

# pyRPC Framework

## What is RPC?

RPC (Remote Procedure Call) 远程过程调用, 是一种计算机通信协议, 允许程序在不同计算机之间通信和交互, 就像本地调用一样.

## Consumer / Provider Model

RPC 框架本身不是一个独立运行的服务, 而是一个被 Consumer (调用方) 和 Provider (服务提供方) 引入的库 (SDK). RPC 框架的主要作用是为开发者提供远程服务调用的能力; 同时抽象网络通信, 序列化/反序列化等底层细节. 

RPC 完成通信的主要过程为:

### 共享模块 (common-example)

- 共享模块 (SDK), 不在服务器上运行, 给各个开发组件提供规范化的数据类型和接口
- 有两个实体类: `User` and `Music`, 含有基本数据类型和集合数据类型
- 规定两个接口: `UserService` and `MusicService`, 包含 `getUserById` and `getMusicById` 方法

### Consumer

- 需引入 common-example & pyrpc
- 模拟服务消费者, 运行在服务器上

#### :zero:初始化 Consumer 端配置

```
RpcApplication.init();
```

主要是读取 Consumer 端的配置信息. 

#### :one:Consumer 使用动态代理工厂 `ServiceProxyFactory` 获取代理对象

```
UserService userService = ServiceProxyFactory.getProxy(UserService.class);
```

然后就可以对 `userService` 像在本地一样调用其中的方法.

其中 `ServiceProxyFactory` 内部存储了 `ServiceProxy` 的实例 (使用 static 的单例存储, 避免过多的对象创建). 

#### :two:`ServiceProxy` 帮助 Consumer 完成对请求的封装, 发送, 并接受响应

在 `ServiceProxy` 中, 执行了以下关键内容:

1. 将调用者调用的方法, 传入的参数封装成标准化的 `RpcRequest rpcRequest`

2. 使用序列化器序列化 `rpcRequest`
3. 向 Provider 的的 RPC 服务端点发送请求

4. 待获取 RPC 的响应数据后, 反序列化它, 将其转化成 `RpcResponse rpcResponse`, 并抽取其中的数据字段 (这才是 Consumer 真正想拿到的东西)

### Provider (provider-example)

- 需引入 common-example & pyrpc
- 模拟服务提供者, 运行在服务器上
- 它实现了共享模块中的 `UserService` and `MusicService`

#### :zero:Provider 的初始化流程

1. 读入配置信息

2. 把自己想提供的服务注册到 `RpcLocalRegistry` (本地注册中心)

3. 开启一个Web 服务器, 监听 RPC 请求


#### :one:`RpcServer`帮助 Provider 完成对请求的接收, 解析, 并发送响应

RPC 框架帮助 Provider 接受并处理请求的核心逻辑在 `server` 包中, 即开启一个 Web 服务器来处理发送过来的 RPC 请求, 并把结果发送回去; 而其中的处理请求的逻辑, 则单独封装在 `ServerHandler` 中. 

`RpcServer` 中的逻辑是一个异步处理模型. 当有请求过来时, 遵循以下步骤: 请求解析 -> 服务方法调用 -> 结果封装 -> 响应返回. 

### RPC (pyrpc)

连接 Consumer 和 Provider 通信的枢纽, 接下来重点介绍.

## Main Components in pyRPC

### 数据组件和配置组件: `com.ypy.pyrpc.model` & `com.ypy.pyrpc.config`

其中定义了 `RpcRequest`, `RpcResponse`, `ServiceMetaInfo`, `ServiceRegisterInfo` 等基本数据类型.

以及RPC框架的配置数据类型 (`RpcConfig` and `RpcConfig.RegistryConfig`) 和一个用于读取配置文件的工具类 (`RpcConfigReader`).

本框架的配置信息可以写入`resources/application.properties`, 完整的配置信息如下:

```
pyrpc.serverHost=127.0.0.1
pyrpc.serverPort=8081
pyrpc.serverType=http || tcp
pyrpc.mock=false || true
pyrpc.registry.registry=no || etcd
pyrpc.registry.addr=http://127.0.0.1:2375
pyrpc.registry.username=xxx
pyrpc.registry.password=yyy
pyrpc.registry.timeout=10000
pyrpc.serializer=jdk || json || kryo
pyrpc.loadbalancer=round-robin || random || consistent-hash
pyrpc.retry=no || fixed-interval
pyrpc.tolerance=fail-fast || fail-safe
```

*\*其中 || 分割了可供选择的默认实现类的 key*

在 `resources/META-INF/prrpc/` 中, 有 `custom/` 和 `system/` 两个目录, 其中 `system/` 中的文件如下:

```
com.ypy.pyrpc.spi.loadbalancer.Loadbalancer
com.ypy.pyrpc.spi.registry.Registry
com.ypy.pyrpc.spi.retry.Retry
com.ypy.pyrpc.spi.serializer.Serializer
com.ypy.pyrpc.spi.tolerance.Tolerance
```

分别对应了提供 SPI 自定义功能实现类的接口名, 在相对应的文件内, 可以写入功能实现类以及其对应的 key. 框架中的默认实现类以及 key 如下:

```
round-robin=com.ypy.pyrpc.spi.loadbalancer.impl.RoundRobinLoadbalancer
random=com.ypy.pyrpc.spi.loadbalancer.impl.RandomLoadbalancer
consistent-hash=com.ypy.pyrpc.spi.loadbalancer.impl.ConsistentHashLoadbalancer
```

```
no=com.ypy.pyrpc.spi.registry.impl.NoRegistry
etcd=com.ypy.pyrpc.spi.registry.impl.EtcdRegistry
```

```
no=com.ypy.pyrpc.spi.retry.impl.NoRetry
fixed-interval=com.ypy.pyrpc.spi.retry.impl.FixedIntervalRetry
```

```
jdk=com.ypy.pyrpc.spi.serializer.impl.JdkSerializer
json=com.ypy.pyrpc.spi.serializer.impl.JsonSerializer
kryo=com.ypy.pyrpc.spi.serializer.impl.KryoSerializer
```

```
fail-fast=com.ypy.pyrpc.spi.tolerance.impl.FailFastTolerance
fail-safe=com.ypy.pyrpc.spi.tolerance.impl.FailSafeTolerance
```

### 启动组件 `com.ypy.pyrpc.bootstrap`

**:star:Consumer 启动类 `ConsumerBootstrap`**

帮助 Consumer 初始化, 其基本逻辑是调用 `com.ypy.pyrpc.app.RpcApplication`, 来完成对配置信息的读取. 本质上只是对 `com.ypy.pyrpc.app.RpcApplication` 进行了一层封装, 规范了代码结构.

**:star:Provider 启动类 `ProviderBoostrap`**

```java
public class ProviderBootstrap {
    public static void init(List<ServiceRegisterInfo> serviceRegisterInfoList) {
        RpcApplication.init();

        final RpcConfig rpcConfig = RpcApplication.getRpcConfig();

        for (ServiceRegisterInfo<?> serviceRegisterInfo : serviceRegisterInfoList) {
            RpcLocalRegistry.register(serviceRegisterInfo.getServiceInterfaceName(), serviceRegisterInfo.getServiceImplClass());

            RpcConfig.RegistryConfig registryConfig = rpcConfig.getRegistryConfig();
            Registry registry = RegistryFactory.getInstance(registryConfig.getRegistry());
            ServiceMetaInfo serviceMetaInfo = ServiceMetaInfo.builder()
                    .serviceName(serviceRegisterInfo.getServiceInterfaceName())
                    .serviceVersion(RpcConstant.DEFAULT_SERVICE_VERSION) // todo
                    .serviceHost(rpcConfig.getServerHost())
                    .servicePost(rpcConfig.getServerPort())
                    .build();

            try {
                registry.register(serviceMetaInfo);
            } catch (Exception e) {
                throw new RuntimeException(serviceRegisterInfo.getServiceInterfaceName() + " register failed", e);
            }
        }

        RpcServer rpcServer = RpcServerFactory.getInstance(RpcApplication.getRpcConfig().getServerType());
        rpcServer.start(rpcConfig.getServerPort());
    }
}
```

- 使用 `RpcApplication.init()` 初始化 RPC 框架, 读取配置信息
- 把 `serviceRegisterInfoList` 中的服务注册到本地注册中心 (`RpcLocalRegistry`)
- 根据全局配置, 通过注册中心代理工厂 (`RegistryFactory`) 获取相对应的注册中心对象, 把服务写入注册中心
- 开启PRC Web 服务器, 在相应的端口专门处理RPC请求

*注意: 把服务注册到本地和把服务注册到注册中心是不同的*

- 本地注册: 把服务接口信息和服务实现类以单例模式存入 `RpcLocalRegistry`, 方便处理RPC请求时调用
- 注册中心: 把服务的信息 (`com.ypy.pyrpc.model.ServiceMetaInfo`, 主要包含服务名和服务提供者的地址) 存入一个键值型数据库 (例如 ETCD 或 Redis), 这个数据库服务是独立与所有 Provider 的, 因此但不同的 Provider 提供不同的服务时, 注册中心充当了一个 "信息集散地": RPC 框架帮助 Consumer 在注册中心获取服务的地址, 并挑选合适的服务节点; 当允许多个 Provider 提供相同服务时, 可以使用负载均衡减少服务器压力

### 全局组件 `com.ypy.pyrpc.app`

**:star:RPC初始化工具类`RpcApplication`**

```java
@Slf4j
public class RpcApplication {
    private static volatile RpcConfig rpcConfig;

    /**
     * init RpcConfig
     * and Registry
     * @param newRpcConfig
     */
    public static void init(RpcConfig newRpcConfig) {
        // config
        rpcConfig = newRpcConfig;
        log.info("RpcApplication initialized with config: {}", rpcConfig);

        // registry
        RpcConfig.RegistryConfig registryConfig = rpcConfig.getRegistryConfig();
        Registry registry = RegistryFactory.getInstance(registryConfig.getRegistry());
        registry.init(registryConfig);
        log.info("Registry initialized with config: {}", registryConfig);
        Runtime.getRuntime().addShutdownHook(new Thread(registry::destroy));
    }

    public static void init() {
        RpcConfig newRpcConfig;
        try {
            newRpcConfig = RpcConfigReader.loadConfig(RpcConstant.DEFAULT_CONFIG_PREFIX);
        } catch (Exception e) {
            log.warn("Failed to load configuration file. Using default config.", e);
            newRpcConfig = new RpcConfig();
        }
        init(newRpcConfig);
    }

    public static RpcConfig getRpcConfig() {
        if (rpcConfig == null) {
            synchronized (RpcApplication.class) {
                if (rpcConfig == null) init();
            }
        }
        return rpcConfig;
    }
}
```

工具类, 是RPC框架初始化的公共逻辑, 其中都是静态方法和静态成员变量. 维护了一个静态的 `RpcConfig` 实例, 它包含了 RPC 的全部配置信息. 代码中使用工具类 `com.ypy.pyrpc.config.RpcConfigReader` 中的静态方法`loadConfig` 读取程序员在项目 `/resourses/application.properties` 中写的配置信息. 

**:star:本地注册中心 `RpcLocalRegistry`**

```java
public class RpcLocalRegistry {
    private static final Map<String, Class<?>> registeredServiceImpl = new ConcurrentHashMap<String, Class<?>>();

    public static void register(String serviceName, Class<?> serviceImpl) {
        registeredServiceImpl.put(serviceName, serviceImpl);
    }

    public static Class<?> getServiceImpl(String serviceName) {
        return registeredServiceImpl.get(serviceName);
    }

    public static void removeService(String serviceName) {
        registeredServiceImpl.remove(serviceName);
    }
}
```

工具类, 维护了一个静态的 `ConcurrentHashMap<String, Class<?>>` 用于存储 Provider 提供的服务名和对应实现类的Class类

### 代理组件 `com.ypy.pyrpc.proxy`

这一部分是 Consumer 发送 RPC 请求的核心

**:star:消费者服务代理 `ServiceProxy`**

```java
public class ServiceProxy implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String serviceName = method.getDeclaringClass().getName();
        RpcRequest rpcRequest = RpcRequest.builder()
                .serviceName(serviceName)
                .methodName(method.getName())
                .parameterTypes(method.getParameterTypes())
                .args(args)
                .build();
        Registry registry = RegistryFactory.getInstance(RpcApplication.getRpcConfig().getRegistryConfig().getRegistry());

        String serviceNameVer = ServiceMetaInfo.serviceNameVer(serviceName, RpcConstant.DEFAULT_SERVICE_VERSION);
        List<ServiceMetaInfo> serviceMetaInfoList = registry.serviceDiscovery(serviceNameVer);

        Loadbalancer loadbalancer = LoadbalancerFactory.getInstance(RpcApplication.getRpcConfig().getLoadbalancer());
        Map<String, Object> context = Map.of("serviceNameVer", serviceNameVer);
        ServiceMetaInfo serviceMetaInfo = loadbalancer.select(serviceMetaInfoList, context);

        RpcClient rpcClient = RpcClientFactory.getInstance(RpcApplication.getRpcConfig().getServerType());
        RpcResponse rpcResponse;
        try {
            Retry retry = RetryFactory.getInstance(RpcApplication.getRpcConfig().getRetry());
            rpcResponse = retry.retry(() -> rpcClient.request(rpcRequest, serviceMetaInfo));
        } catch (Exception e) {
            Tolerance tolerance = ToleranceFactory.getInstance(RpcApplication.getRpcConfig().getTolerance());
            rpcResponse = tolerance.tolerate(e, context);
        }
        return rpcResponse.getData();
    }
}
```

这个类可以说是为 Consumer 发送RPC请求的核心, 代码的主要逻辑是:

- 把由参数获取的服务接口名, 服务方法名, 参数类型及参数封装进入`rpcRequest`
- 根据全局静态配置信息获取注册中心 (`registry`) 并通过注册中心根据服务名和版本信息发现服务
- 根据全局静态配置信息获取负载均衡器 (`loadbalancer`) 并通过负载均衡器选择适合的服务 Provider 节点
- 使用 `RpcClient.request()` 静态方法发送 RPC 请求 (这里的逻辑在 RPC Server 部分解释)
- 根据全局静态配置信息获取重试和容错器, 在出现问题进行相应的处理步骤
- 获得 `rpcResonse` 返回其 `data` 部分

**:star:Mock服务代理 `MockServiceProxy`**

```java
@Slf4j
public class MockServiceProxy implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Class<?> methodReturnType = method.getReturnType();
        log.info("mock invoke {}", method.getName());
        return getDefaultObject(methodReturnType);
    }

    private Object getDefaultObject(Class<?> type) {
        // 8 primitive type
        if (type == boolean.class || type == Boolean.class) return false;
        if (type == byte.class || type == Byte.class) return (byte) 0;
        if (type == short.class || type == Short.class) return (short) 0;
        if (type == char.class || type == Character.class) return (char) 0;
        if (type == int.class || type == Integer.class) return 0;
        if (type == long.class || type == Long.class) return 0L;
        if (type == float.class || type == Float.class) return 0.0f;
        if (type == double.class || type == Double.class) return 0.0d;

        // handle common type
        if (type == String.class) return "<mock>";
        if (type == List.class) return Collections.emptyList();
        if (type == Map.class) return Collections.emptyMap();
        if (type == Set.class) return Collections.emptySet();

        // handle self-defined class
        try {
            Object instance = type.getDeclaredConstructor().newInstance();
            Field[] fields = type.getDeclaredFields();
            for (Field field : fields) {
                field.setAccessible(true);
                Class<?> fieldType = field.getType();
                field.set(instance, getDefaultObject(fieldType)); // recursion
            }
            return instance;
        } catch (Exception e) {
            log.warn("Failed to create mock object for type: {}", type.getName(), e);
            return null;
        }
    }
}
```

为开发者提供生成假数据的服务代理, 当 Consumer 开发者在不存在 Provider 的情况下, 也能调用假服务, 获取假数据

**:star:代理服务工厂 `ServiceProxyFactory`**

```java
public class ServiceProxyFactory {
    public static <T> T getProxy(Class<T> serviceInterface) {
        if (RpcApplication.getRpcConfig().isMock()) return getMockProxy(serviceInterface);

        return (T) Proxy.newProxyInstance(
                serviceInterface.getClassLoader(),
                new Class[]{serviceInterface},
                new ServiceProxy()
        );
    }

    public static <T> T getMockProxy(Class<T> serviceInterface) {
        return (T) Proxy.newProxyInstance(
                serviceInterface.getClassLoader(),
                new Class[]{serviceInterface},
                new MockServiceProxy()
        );
    }
}
```

- `Proxy.newProxyInstance` 是 JDK 提供的动态代理工具方法, 它需要三个参数: 
  - `ClassLoader`: 用于加载代理类的类加载器
  - `Class<?>[]`: 代理类需要实现的接口列表
  - `InvocationHandler`: 代理类的调用处理器, 负责拦截方法调用并执行自定义逻辑
- 在这里, `ServiceProxy` 和 `MockServiceProxy` 都实现了 `InvocationHandler` 接口, 分别用于处理真实的 RPC 调用和 Mock 调用

### 服务器组件 `com.ypy.pyrpc.server`

这一部分是 Provider 处理 RPC 请求的重点, 也是 RPC 通信的重点. 该包中有两个重要的接口: `RpcClient` 和 `RpcServer`. 

`RpcClient` 只有一个方法 `RpcResponse request(RpcRequest rpcRequest, ServiceMetaInfo serviceMetaInfo) throws Exception;`. 它是发送 RPC 请求的核心. 注意, 这里的发送请求不是 `ServiceProxy` 的普通业务逻辑. 这里的主要是要实现序列化/反序列化, 以及使用TCP协议发送请求. 

`RpcServer` 也只有一个方法 `void start(int port);`. 它.帮助 Provider 在相应端口构造 Web 服务, 并处理 RPC 请求.

同时, 它们都有各自对应的工厂类 (`RpcClientFactory` & `RpcServerFactory`), 以 `RpcClientFactory` 为例:

```java
public class RpcClientFactory {
    private static final Map<String, RpcClient> clientMap = new HashMap<>();

    static {
        clientMap.put(RpcServerTypeKeys.TCP, new TcpClient());
        clientMap.put(RpcServerTypeKeys.HTTP, new HttpClient());
    }

    public static RpcClient getInstance(String implClassKey) { return clientMap.get(implClassKey); }
}
```

由于我们不能每发一次 RPC 请求就 new 一个 `TcpClient` 或 `HttpClient` 实现类, 于是我们选择使用工厂模式, 维护一个静态的Map, 存储了对应的 `RpcClient` 的实现类, 会根据对应的 key 返回相应的实现类. `RpcServerFactory` 同理. 这样即避免了重复 new 对象, 同时也允许开发者配置自己喜欢的底层通信协议 (TCP or HTTP).

接下来我们主要看看使用 TCP 协议作为底层协议, 是如何实现 `RpcClient` 和 `RpcServer` 的:

#### 协议数据结构组件 `com.ypy.pyrpc.server.tcp.protocol`

这里主要定义了自定义协议的结构 (`Protocol`):

```java
@Data
@AllArgsConstructor
public class Protocol<T> {
    @Data
    @Builder
    public static class Header {
        private byte magic;
        private byte version;
        private byte serializer;
        private byte type;
        private byte status;
        private long requestId;
        private int bodyLength;
    }

    private Header header;
    private T body; // body: RpcRequest or RpcResponse, will be serialized into bytes
}
```

以及其它协议常量以及枚举

#### TCP 服务器实现组件 `com.ypy.pyrpc.server.tcp`

**:star:TCP 服务器实现 `TcpServer`**

```java
public class TcpServer implements RpcServer {
    @Override
    public void start(int port) {
        Vertx vertx = Vertx.vertx();
        NetServer server = vertx.createNetServer();
        server.connectHandler(new TcpServerHandler());
        server.listen(port, netServerAsyncResult -> {
            if (netServerAsyncResult.succeeded()) System.out.println("TcpServer started on port " + port);
            else System.out.println("TcpServer failed to start on port " + port);
        });
    }
}
```

代码的首先使用 `Vertx` 构造一个 Web 服务器: 这里的 `vertx` 虽然不是`static` 的, 但是因为整个 `TcpServcer` 会被以单例的形式存储在工厂类的静态变量中, 并且 `start` 方法只会在 Provider 初始化中被调用一次, 因此不存在重复实例化对象 (`Vertx` 实例) 的问题. 也就是说, 整个 Provider 中, 只会存在一个 `Vertx` 实例. 但是, 这并不影响RPC请求的并发处理, 因为:

- **`Vert.x` 的特性**：
  `Vert.x` 是一个事件驱动的框架, 它的核心思想是**非阻塞 I/O** 和**事件循环**. 当你在 `TcpServer` 中使用 `Vert.x` 的 `NetServer` 时，`Vert.x` 会自动处理多个并发连接, 并且每个连接的处理是非阻塞的. 这意味着: 
  - 多个 Consumer 发送的请求会被 `Vert.x` 并发处理，而不是顺次处理. 
  - `Vert.x` 的事件循环机制会确保高并发请求的高效处理. 
- **Provider 中的 `RpcServer` 实例**：
  在 Provider 中只启动了一个 `RpcServer` 实例 (例如 `TcpServer`), 但由于 `Vert.x` 的异步和非阻塞特性, 这个实例可以同时处理多个 Consumer 的请求. 每个请求都会由 `Vert.x` 的事件循环分配到不同的线程 (或事件循环线程) 中处理. 

最后, 使用 `.connectHandler()` 方法绑定处理器.

**:star:TCP 处理器 `TcpServerHandler`**

```java
public class TcpServerHandler implements Handler<NetSocket> {
    @Override
    public void handle(NetSocket netSocket) {
        TcpBufferHandlerWrapper bufferHandlerWrapper = new TcpBufferHandlerWrapper(buffer -> {
            Protocol<RpcRequest> protocol;
            try {
                protocol = (Protocol<RpcRequest>) ProtocolUtils.decode(buffer);
            } catch (Exception e) {
                throw new RuntimeException("Decode Err", e);
            }
            RpcRequest rpcRequest = protocol.getBody();
            RpcResponse rpcResponse = new RpcResponse();
            try {
                Class<?> serviceImpl = RpcLocalRegistry.getServiceImpl(rpcRequest.getServiceName());
                Method method = serviceImpl.getMethod(rpcRequest.getMethodName(), rpcRequest.getParameterTypes());
                Object result = method.invoke(serviceImpl.newInstance(), rpcRequest.getArgs());

                rpcResponse.setData(result);
                rpcResponse.setDataType(method.getReturnType());
                rpcResponse.setMsg("ok");
            } catch (Exception e) {
                e.printStackTrace();
                rpcResponse.setMsg(e.getMessage());
                rpcResponse.setException(e);
            }

            Protocol.Header header = protocol.getHeader();
            header.setType(ProtocolTypeEnum.RESPONSE.getCode());
            Protocol<RpcResponse> responseProtocol = new Protocol<>(header, rpcResponse);
            try {
                Buffer encodeBuffer = ProtocolUtils.encode(responseProtocol);
                netSocket.write(encodeBuffer);
            } catch (IOException e) {
                throw new RuntimeException("Encode Err", e);
            }
        });
        netSocket.handler(bufferHandlerWrapper);
    }
}
```

在 Handler 中, `netSocket.handler()` 传入一个 `Handler<Buffer>` 对象, 用于处理通信的字节流.

使用 `TcpBufferHandlerWrapper` 包裹, 以解决半包/粘包问题.

处理的主体逻辑是:

- 通过全局配置获取序列化器, 使用序列化器将传入的 buffer 反序列化成 `Protocol<RpcRequest> requestProtocol` , 获取其 `body`, 就是 `rpcRequest`
- 在 `rpcRequest` 中获取要调用服务的信息 (服务接口名, 方法名, 参数类型及参数列表), 通过 `RpcLocalRegistry` 获取服务实现类的 Class 对象, 并使用 `invoke` 调用获取方法返回结果 `result`
- 将结果封装进入 `rpcResponse`, 将 `rpcResponse` 封装进入 `Protocol<RpcResponse> responseProtocol`
- 通过之前的序列化器序列化 `responseProtocol`, 并使用 `netSocket.write(encodeBuffer);` 将 buffer 写入socket

**:star:TCP 客户端实现 `TcpClient`**

```java
public class TcpClient implements RpcClient {
    private static Vertx vertx = Vertx.vertx(); // signal instance

    private static NetClient netClient = vertx.createNetClient(new NetClientOptions()); // signal instance

    @Override
    public RpcResponse request(RpcRequest rpcRequest, ServiceMetaInfo serviceMetaInfo) throws ExecutionException, InterruptedException {
        CompletableFuture<RpcResponse> responseCompletableFuture = new CompletableFuture<>();

        netClient.connect(
                serviceMetaInfo.getServicePost(),
                serviceMetaInfo.getServiceHost(),
                netSocketAsyncResult -> {
                    if (!netSocketAsyncResult.succeeded()) {
                        System.err.println(netSocketAsyncResult.cause().getMessage());
                        return;
                    }
                    NetSocket netSocket = netSocketAsyncResult.result();

                    // make date
                    Protocol.Header header = Protocol.Header.builder()
                            .magic(ProtocolConst.MAGIC)
                            .version(ProtocolConst.VERSION)
                            .serializer(ProtocolSerializerEnum.getByKey(RpcApplication.getRpcConfig().getSerializer()).getCode())
                            .type(ProtocolTypeEnum.REQUEST.getCode())
                            .requestId(IdUtil.getSnowflakeNextId())
                            .build();
                    Protocol<RpcRequest> rpcRequestProtocol = new Protocol<>(header, rpcRequest);
                    rpcRequestProtocol.setHeader(header);
                    rpcRequestProtocol.setBody(rpcRequest);

                    // send data
                    try {
                        Buffer buffer = ProtocolUtils.encode(rpcRequestProtocol);
                        netSocket.write(buffer);
                    } catch (IOException e) {
                        throw new RuntimeException("Encode failed", e);
                    }

                    // get response data
                    TcpBufferHandlerWrapper bufferHandlerWrapper = new TcpBufferHandlerWrapper(buffer -> {
                        try {
                            Protocol<RpcResponse> responseProtocol =
                                    (Protocol<RpcResponse>) ProtocolUtils.decode(buffer);
                            responseCompletableFuture.complete(responseProtocol.getBody());
                        } catch (IOException e) {
                            throw new RuntimeException("Decode failed", e);
                        }
                    });
                    netSocket.handler(bufferHandlerWrapper);
                }
        );

        RpcResponse rpcResponse = responseCompletableFuture.get();
        return rpcResponse;
    }
}
```

`TcpClient` 的实例被存储在工厂类中, 但是, 由于 Consumer 要多次调用 `request` 方法, 因此选择把 `Vertx` 和 `NetClient` 实例作为静态变量, 避免每次调用 `request` 的时候都创建连接.

将以上的 `RpcServer` 和 `RpcClient` 分开构建的原因是: 为了满足一个节点作为 Consumer 的同时也提供服务, 作为 Provider. 

**:star:解决半包粘包处理器 `TcpBufferHandlerWrapper`**

为了解决 TCP 传输中的半包粘包问题, 构造一个处理器, 它实现并增强 `Handler<Buffer>` 接口. 这种做法是设计模式中的 **装饰者模式**, 使用 `RecordParser` 对原有的 `Buffer` 处理器的能力进行增强. 装饰者模式可以简单理解为给对象穿装备, 增强对象的能力. 

```java
public class TcpBufferHandlerWrapper implements Handler<Buffer> {
    private final RecordParser recordParser;

    private RecordParser initRecordParser(Handler<Buffer> bufferHandler) {
        RecordParser parser = RecordParser.newFixed(ProtocolConst.HEADER_LENGTH);
        parser.setOutput(new Handler<Buffer>() {
            int bodyLength = -1;
            Buffer resultBuffer = Buffer.buffer();

            @Override
            public void handle(Buffer buffer) {
                if (-1 == bodyLength) {
                    // 1. 首先解析 Header（固定长度 17 字节）
                    bodyLength = buffer.getInt(13); // 获取 Body 的长度，假设Header的索引 13..=16 字节表示 BodyLength
                    parser.fixedSizeMode(bodyLength); // 根据Body的长度设置接下来的解析模式
                    resultBuffer.appendBuffer(buffer); // 将Header数据缓存起来
                } else {
                    // 2. 接收 Body 数据
                    resultBuffer.appendBuffer(buffer); // 将接收到的Body数据添加到缓存中

                    if (resultBuffer.length() >= bodyLength + ProtocolConst.HEADER_LENGTH) {
                        bufferHandler.handle(resultBuffer); // 调用外部传入的回调，处理完整的消息
                        // 4. 处理完后，重置状态，准备处理下一个数据包
                        parser.fixedSizeMode(ProtocolConst.HEADER_LENGTH);
                        bodyLength = -1; // 重置Body长度
                        resultBuffer = Buffer.buffer(); // 重置缓存
                    }
                }
            }
        });
        return parser;
    }

    public TcpBufferHandlerWrapper(Handler<Buffer> bufferHandler) {
        this.recordParser = initRecordParser(bufferHandler);
    }

    @Override
    public void handle(Buffer buffer) {
        recordParser.handle(buffer);
    }
}
```

### SPI 组件 `com.ypy.pyrpc.spi`

**SPI (Service Provider Interface) ** 是一种服务发现机制, 用于在运行时动态加载实现类. 它是 Java 提供的一种标准扩展点机制, 允许框架或库定义接口, 并由第三方提供实现. SPI 的核心思想是 **解耦接口与实现**, 使得应用程序可以在不修改代码的情况下扩展功能. 说白了, 就是SDK中提供一些接口和默认实现类, 当开发者不想使用SDK提供的实现类时, 他们可以自己写一个实现类, 并通过配置让SDK使用自定义的实现类.

**:star:SPI导入器`SpiLoader`​**

```java
@Slf4j
public class SpiLoader {
    /**
     * store the class that has been load, {interfaceName: {implClassKey: implClass}}
     * className means full name, e.g. com.ypy.pyrpc.spi.serializer.Serializer
     * classKey means a short key, representing some implClass, e.g. jdk=JdkSerializer
     */
    private static final Map<String, Map<String, Class<?>>> spiMap = new ConcurrentHashMap<>();

    /**
     * cache of instance, avoid repeat "new"
     * {implClassName: implClassInstance}
     * full-name, not a key
     */
    private static final Map<String, Object> implClassInstanceCache = new ConcurrentHashMap<String, Object>();
    private static final String RPC_SYSTEM_SPI_DIR = "META-INF/pyrpc/system/";
    private static final String RPC_CUSTOM_SPI_DIR = "META-INF/pyrpc/custom/";
    private static final String[] SCAN_DIRS = new String[]{RPC_SYSTEM_SPI_DIR, RPC_CUSTOM_SPI_DIR};

    public static Map<String, Class<?>> load(Class<?> interfaceClass) {
        String interfaceName = interfaceClass.getName();
        Map<String, Class<?>> implKey2implClassMap = new HashMap<>(); // {implClassKey: implClass}
        for (String dir : SCAN_DIRS) {
            List<URL> resources = ResourceUtil.getResources(dir + interfaceName);
            for (URL resource : resources) {
                try {
                    InputStreamReader isr = new InputStreamReader(resource.openStream());
                    BufferedReader br = new BufferedReader(isr);
                    String line = null;
                    while ((line = br.readLine()) != null) {
                        String[] split = line.split("=");
                        if (split.length == 2) {
                            String implClassKey = split[0];
                            String implClassName = split[1];
                            implKey2implClassMap.put(implClassKey, Class.forName(implClassName));
                        }
                    }
                } catch (Exception e) {
                    log.error("spi resource load error", e);
                }
            }
        }
        spiMap.put(interfaceName, implKey2implClassMap);
        return implKey2implClassMap;
    }

    public static <T> T getInstance(Class<T> interfaceClass, String implClassKey) {
        String interfaceName = interfaceClass.getName();
        Map<String, Class<?>> implKey2implClassMap = spiMap.get(interfaceName);
        if (implKey2implClassMap == null) throw new RuntimeException("Interface " + interfaceName + " has no implement classes");
        if (!implKey2implClassMap.containsKey(implClassKey)) throw new RuntimeException("Interface " + interfaceName + " has no implement " + implClassKey);

        Class<?> implClass = implKey2implClassMap.get(implClassKey);
        if (!implClassInstanceCache.containsKey(implClass.getName())) {
            try {
                implClassInstanceCache.put(implClass.getName(), implClass.newInstance());
            } catch (InstantiationException | IllegalAccessException e) {
                throw new RuntimeException("Write " + implClass.getName() + " into cache failed", e);
            }
        }
        return (T) implClassInstanceCache.get(implClass.getName());
    }
}
```

使用两个`ConcurrentHashMap`存贮功能接口和实现类的信息, 并引入缓存机制:

- `spiMap` 的存储模式是: `{interfaceName: {implClassKey: implClass}}`, 例如 `{"Registry" : {"jdk": Class<JdkRegistry>, "json": Class<JsonRegistry>}}`
- `implClassInstanceCache` 的存储模式是: `{implClassName: implClassInstance}`, 例如 `{"JdkRegistry": JdkRegistry}`. 作为对象缓存, 避免重复new对象

使用SPI机制, 为以下四个功能模块提供了可定制化选择: 1) 注册中心; 2) 序列化器; 3) 负载均衡器; 4) 重试和容错器. 它们有对应的工厂类, 例如

**:stars:`RegistryFactory`**

```java
public class RegistryFactory {
    static { SpiLoader.load(Registry.class); }
    public static Registry getInstance(String implClassKey) { return SpiLoader.getInstance(Registry.class, implClassKey); }
}
```

当项目加载时, 工厂类的 `static` 部分会调用 `loader` 方法, 将默认实现类和用户实现类加入 `SpiLoader` 中的字典容器, 同时, 由于是先载入框架默认实现类后载入用户自定义实现类, 因此用户自定义的key会覆盖框架默认的key, 这样是更加合理的. 

下面分别介绍一下框架中的默认实现类.

#### 序列化器组件 `com.ypy.pyrpc.spi.serializer`

**:star:`Serializer` 接口**

```java
public interface Serializer {
    <T> byte[] serialize(T obj) throws IOException;
    <T> T deserialize(byte[] data, Class<T> type) throws IOException;
}
```

框架提供的默认实现类在 `impl` 包中, 有 JDK, JSON, KRYO 三种序列化器. 

#### 注册中心组件 `com.ypy.pyrpc.spi.registry`

**:smile:形象的例子**

食客 - 点餐平台 (美团) - 饭馆: 食客 = Consumer, 美团 = 注册中心, 餐馆 = Provider. 

食客从美团不是去吃菜 (调用服务), 而是获取餐馆和菜品信息, 然后再去餐馆吃菜 (发起 RPC 请求). 

餐馆 (Provider) 要先做菜 (把提供的服务存入本地注册中心 `RpcLocalRegistry`) 再把相应的菜品信息存入美团 (把提供的服务的信息提交给 `Registry`, `Registry` 把信息存入 ETCD), 这样顾客才能发现菜品. 

所谓服务发现缓存, 实际就是, 当一个食客经常去某餐馆吃饭, 他去多了, 也就没有必要次次都去到美团上查看关于餐馆的信息 (例如在哪里, 有哪些菜品). 

**:star:`Registry`​ 接口**

```java
public interface Registry {
    /**
     * initialize registry for both PROVIDER and CONSUMER
     * @param registryConfig
     */
    void init(RpcConfig.RegistryConfig registryConfig);

    /**
     * help PROVIDER to register their service (info) into registry
     * and these services should also be registered and managed by LocalRegistry
     * @param serviceMetaInfo
     * @throws Exception
     */
    void register(ServiceMetaInfo serviceMetaInfo) throws Exception;

    /**
     * help PROVIDER to unregister service
     * @param serviceMetaInfo
     * @throws Exception
     */
    void unregister(ServiceMetaInfo serviceMetaInfo) throws Exception;

    /**
     * help CONSUMER to search a group of service meta info by ServiceNameVersion
     * @param serviceNameVer: "serviceName:serviceVersion"
     * @return
     */
    List<ServiceMetaInfo> serviceDiscovery(String serviceNameVer);

    /**
     * when a PROVIDER offline, clear correlative service info it provided in registry
     */
    void destroy();

    /**
     * send update message to registry, confirming PROVIDED alive
     */
    void heartBeat();

    /**
     * help CONSUMER to watch PROVIDER, when a Provider offline (actively or passively), it will update local cache info of CONSUMER
     * @param fullRegistryServiceKey: /[RPC_ROOT_PATH]/[ServiceName]:[ServiceVersion]/[ServiceAddr]
     */
    void watch(String fullRegistryServiceKey, String serviceNameVer);
}
```

注册中心接口有六个方法, 分别是注册中心初始化, 服务注册, 服务注销, 服务发现, 节点关闭, 心跳检测, 以及节点监视. 

在 `impl` 包中, 提供了 ETCD 的默认实现 (`com.ypy.pyrpc.spi.registry.impl.EtcdRegistry`), 以及无注册中心的实现 `NoRegistry`. 

实现一个注册中心是一个难点, 我们详细说一下:

**:stars:`EtcdRegistry`​ ETCD 注册中心**

```java
public class EtcdRegistry implements Registry {
    private Client cli;

    private KV kvCli;

    private static final String ETCD_ROOT_PATH = "/pyrpc/";

    /**
     * fullRegistryServiceKey: /pyrpc/com.....UserService:1.0.0/http://127.0.0.1:8080
     * <p>
     * this collection stores data for PROVIDER
     */
    private final Set<String> loaclFullRegistryServiceKeySet = new HashSet<String>();

    /**
     * key is serviceNameVersion
     * <p>
     * this collection stores data for CONSUMER, make convenience through cache ServiceNameVer and their MetaInfo
     */
    private final Map<String, List<ServiceMetaInfo>> serviceNameVerCache = new ConcurrentHashMap<>();

    private final Set<String> watchingNodeKeySet = new HashSet<>();

    @Override
    public void init(RpcConfig.RegistryConfig registryConfig) {
        cli = Client.builder()
                .endpoints(registryConfig.getAddr())
                .connectTimeout(Duration.ofMillis(registryConfig.getTimeout()))
                .build();
        kvCli = cli.getKVClient();
        heartBeat();
    }

    @Override
    public void register(ServiceMetaInfo serviceMetaInfo) throws Exception {
        Lease leaseCli = cli.getLeaseClient();

        long leaseId = leaseCli.grant(30L).get().getID();

        String fullRegistryServiceKey = ETCD_ROOT_PATH + serviceMetaInfo.getServiceNameVerAddr();
        ByteSequence k = ByteSequence.from(fullRegistryServiceKey, StandardCharsets.UTF_8);
        ByteSequence v = ByteSequence.from(JSONUtil.toJsonStr(serviceMetaInfo), StandardCharsets.UTF_8);

        PutOption putOption = PutOption.builder().withLeaseId(leaseId).build();
        kvCli.put(k, v, putOption).get();
        loaclFullRegistryServiceKeySet.add(fullRegistryServiceKey);
    }

    @Override
    public void unregister(ServiceMetaInfo serviceMetaInfo) throws Exception {
        String fullRegistryServiceKey = ETCD_ROOT_PATH + serviceMetaInfo.getServiceNameVerAddr();
        kvCli.delete(ByteSequence.from(fullRegistryServiceKey, StandardCharsets.UTF_8));
        loaclFullRegistryServiceKeySet.remove(serviceMetaInfo.getServiceNameVerAddr());
    }

    @Override
    public List<ServiceMetaInfo> serviceDiscovery(String serviceNameVer) {
        List<ServiceMetaInfo> cachedServiceMetaInfos = serviceNameVerCache.get(serviceNameVer);
        if (cachedServiceMetaInfos != null) return cachedServiceMetaInfos;

        // search in etcd
        String searchPrefix = ETCD_ROOT_PATH + serviceNameVer + "/"; // add "/" means search for group
        try {
            GetOption getOption = GetOption.builder().isPrefix(true).build();
            List<KeyValue> kvs = kvCli.get(
                    ByteSequence.from(searchPrefix, StandardCharsets.UTF_8),
                    getOption
            ).get().getKvs();

            List<ServiceMetaInfo> serviceMetaInfoList = kvs.stream()
                    .map(kv -> {
                        String fullRegistryServiceKey = kv.getKey().toString(StandardCharsets.UTF_8);
                        watch(fullRegistryServiceKey, serviceNameVer);
                        String val = kv.getValue().toString(StandardCharsets.UTF_8);
                        return JSONUtil.toBean(val, ServiceMetaInfo.class);
                    }).collect(Collectors.toList());

            serviceNameVerCache.put(serviceNameVer, serviceMetaInfoList);
            return serviceMetaInfoList;
        } catch (Exception e) {
            throw new RuntimeException("Get service list failed", e);
        }
    }

    @Override
    public void destroy() {
        for (String key : loaclFullRegistryServiceKeySet) {
            try {
                kvCli.delete(ByteSequence.from(key, StandardCharsets.UTF_8));
            } catch (Exception e) {
                throw new RuntimeException(key + ": delete etcd node failed", e);
            }
        }
        if (kvCli != null) kvCli.close();
        if (cli != null) cli.close();

        System.out.println("Destroy Current Node");
    }

    @Override
    public void heartBeat() {
        // every 10 seconds, update lease for all services provided by current Provider
        CronUtil.schedule("*/10 * * * * *", new Task() {
            @Override
            public void execute() {
                for (String key : loaclFullRegistryServiceKeySet) {
                    try {
                        List<KeyValue> kvs = kvCli.get(ByteSequence.from(key, StandardCharsets.UTF_8))
                                .get()
                                .getKvs();
                        if (kvs.isEmpty()) continue;
                        KeyValue kv = kvs.get(0);
                        String val = kv.getValue().toString(StandardCharsets.UTF_8);
                        ServiceMetaInfo serviceMetaInfo = JSONUtil.toBean(val, ServiceMetaInfo.class);
                        register(serviceMetaInfo);
                    } catch (Exception e) {
                        throw new RuntimeException(key + ": updating lease failed", e);
                    }
                }
            }
        });

        CronUtil.setMatchSecond(true);
        CronUtil.start();
    }

    @Override
    public void watch(String fullRegistryServiceKey, String serviceNameVer) {
        Watch watchCli = cli.getWatchClient();
        if (watchingNodeKeySet.add(fullRegistryServiceKey)) {
            watchCli.watch(ByteSequence.from(fullRegistryServiceKey, StandardCharsets.UTF_8), watchResponse -> {
                for (WatchEvent event : watchResponse.getEvents()) {
                    switch (event.getEventType()) {
                        case DELETE:
                            serviceNameVerCache.remove(serviceNameVer);
                            break;
                        case PUT:
                        default:
                            break;
                    }
                }
            });
        }
    }
}
```

先看成员变量: 由于 `EtcdRegistry` 实例是被保存在 `SpiLoader` 中, 因此每次获取实例时并不会重复创建实例, 所以其中的成员变量都不需要设置为 `static`.

- `Client cli` 是 `jetcd` 中, 操作 ETCD 的客户端, `jectd` 是专门为 Java 操作 ECTD 的 SDK, 相当于 Jedis / Redission 之于 Redis
- `kvCli` 是由 `cli` 生成的操作键值对的客户端
- `ETCD_ROOT_PATH` 是存储在 ETCD 数据库中本项目的标识前缀, 用来与其它项目区分
- `Set<String> loaclFullRegistryServiceKeySet` 是存储本地 (也就是当前 Provider) 中提供的服务信息, 它里面存的是服务的全部信息, 其结构为 `/pyrpc/com.....UserService:1.0.0/http://127.0.0.1:8080` 即前缀 + 服务接口名 + 版本号 + Provider 地址
- `Map<String, List<ServiceMetaInfo>> serviceNameVerCache` 是为 **Consumer** 提供的服务发现缓存, 因为 Consumer 发送 RPC 请求是很频繁的, 我们没有必要每一次都向 ETCD 请求服务信息, 而是选择把对应的信息缓存起来, 这样 Consumer 就可以更加高效的获取服务信息, 再提供信息发送RPC请求了. 由于发现服务是 Consumer 提供服务接口名和版本号, 因此 `serviceNameVerCache` 的 key 结构为 `com.....UserService:1.0.0`
- `Set<String> watchingNodeKeySet` 是为 Consumer 提供的监视机制, 它存储了正在监视的结点, 里面存储的是 `fullRegistryServiceKey`, 也就是前缀 + 服务接口名 + 版本号 + Provider 地址的全部信息

再来看方法实现:

- `init` 是初始化方法, 包括创建相应的 Java ETCD 客户端
- `register` 是服务注册, 主要完成写入信息到 ETDC 和写入信息到本地 `loaclFullRegistryServiceKeySet`
- `serviceDiscovery` 是通过服务接口名和版本号来发现服务, 先查看缓存, 缓存没有再向 ETCD 拉取, 同时存入缓存. 存入缓存的服务需要监视其节点, 于是要调用 `watch(fullRegistryServiceKey, serviceNameVer);` 方法
- `destroy` 是 Provider 端的主动下线机制, 当一个 Provider 停机了, 会将情况先行上报给 ETCD 以避免 Consumer 的错误调用
- `heartBeat` 是 Provider 端的被动下线机制, 当一个 Provider 因为异常停机, 例如断电, 这时候就无法执行 `destroy` 了. 心跳检测的原理是 Provider 定时 (10 s) 向注册中心更新一次服务信息, ETCD 则对每一个节点的信息设置一个稍长的过期时间 (30 s). 当 Provider 因为异常停机, 其对应的节点就会因为无法续期而被 ETCD 删除. 因此异常停机造成的影响最多持续 30 s.
- `watch` 是为 Consumer 检测 Provider 状态的, 当一个 Provider 下线 (主动或被动), Consumer 就会收到 ETCD 的相应, 并删除对应的服务信息缓存

以上的做法维护了: 

- 数据一致性: 当某个 Provider 下线了, 注册中心需要即时剔除它的结点服务信息.
- 性能优化: Consumer 每次都要从注册中心获取服务, 可以使用缓存进行优化. 
- 高可用性: 保证注册中心不会宕机. 
- 高扩展性: 提供不同的注册中心实现类, 并使用 SPI 机制运行用户自定义注册中心实现类. 

#### 负载均衡组件 `com.ypy.pyrpc.spi.loadbalancer`

为 Consumer 从众多内容相同但 Provider 不同的服务结点中, 依据某种算法选择恰当的服务节点, 这样可以合理分配 Provider 端的压力. 

**:stars:`Loadbalancer` 接口**

```java
public interface Loadbalancer {
    /**
     *
     * @param serviceMetaInfoList
     * @param context: Provide additional contextual information for load balancing algorithms to more intelligently select service instances.
     * @return
     */
    ServiceMetaInfo select(List<ServiceMetaInfo> serviceMetaInfoList, Map<String, Object> context);
}
```

使用轮询, 随机, 和一致哈希等方法实现了默认的三种序列化器实现

#### 重试和容错组件 `com.ypy.pyrpc.spi.retry` & `com.ypy.pyrpc.spi.tolerance`

这一部分只做了简单的实现和占位, 开发者可以使用SPI机制自定义实现


