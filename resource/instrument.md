### DATABASE
+ NoSQL数据库 [cassandra](https://cassandra.apache.org/) <font style='color:red'>*</font>[couchbase](https://www.couchbase.com/)
+ 时序数据库 <font style='color:red'>*</font>[InfluxDB](https://www.influxdata.com/)
+ 图数据库 <font style='color:red'>*</font>[neo4j](https://neo4j.com/)
+ 数据库开发运维工具(开源数据库管理工具) [bytebase](https://www.bytebase.com/)
+ 键值对快速存储引擎 [rocksdb](https://rocksdb.org/)
+ 内存数据库 <font style='color:red'>*</font>[dragonfly](https://www.dragonflydb.io/) [redict(redis的分支)](https://redict.io) [garnet](https://microsoft.github.io/garnet/) [keydb](https://docs.keydb.dev) [memcached](http://memcached.org)
+ 分布式内存数据网格 <font style='color:red'>*</font>[hazelcast](https://hazelcast.com/)
+ 文档数据库 <font style='color:red'>*</font>[mongodb](https://www.mongodb.com/)
+ 键值对分布式存储 [etcd](https://etcd.io/) [tikv](https://tikv.org/)
+ 流处理型数据库 [ksqlDB](https://ksqldb.io)
+ 小型快速数据库 <font style='color:red'>*</font>[sqlite](https://www.sqlite.org)
+ 小型内存数据库 [h2](http://www.h2database.com)
+ 关系型数据库 [postgresql](https://www.postgresql.org)
+ 高性能实时分析数据库 [doris](https://doris.apache.org/)

### DISTRIBUTED
+ 分布式配置 [apollo](https://www.apolloconfig.com/) [nacos](https://nacos.io/)
+ 分布式消息系统 [pulsar](https://pulsar.apache.org/) [kafka](https://kafka.apache.org/)
+ 对象存储 <font style='color:red'>*</font>[minio](https://min.io/)
+ 分布式事务管理 <font style='color:red'>*</font>[atomikos](https://www.atomikos.com/)
+ 基于zookeeper的分布式协调服务 [curator](https://curator.apache.org/)
+ 实时工作负载存储服务 [bookkeeper](https://bookkeeper.apache.org)

### PROJECT_MANAGER
+ JVM项目管理工具 [gradle](https://gradle.org) [maven](https://maven.apache.org/)

### JVM_LIBRARY
+ java http库 [Apache HttpComponents](https://hc.apache.org/index.html)
+ java bean映射辅助工具 [mapstruct](https://mapstruct.org/)
+ java响应式编程库 [projectreactor](https://projectreactor.io/)
+ java redis操作库 [lettuce](https://lettuce.io/)
+ java高性能并发库 [disruptor](https://lmax-exchange.github.io/disruptor/)
+ jvm测试平台和库 [arquillian](https://arquillian.org/) [junit](https://junit.org/) [testng](https://testng.org/) 
  + 断言库 [hamcrest](https://hamcrest.org/) [assertj](https://assertj.github.io/doc/)
  + json处理库 [JSONassert](http://jsonassert.skyscreamer.org) [JSONPath](https://goessner.net/articles/JsonPath/)
  + 同步异步控制库 [awaitility](https://github.com/awaitility/awaitility/wiki/Getting_started)
+ java mock库 [mockito](https://site.mockito.org/) [easymock](https://easymock.org/) [jmock](http://jmock.org/) [jmockit](https://jmockit.github.io/)
+ 高并发、分布式、容错的事件驱动库 [akka](https://akka.io/)
+ orm(数据库对象关系映射框架) <font style='color:red'>*</font>[jooq](https://www.jooq.org/) [mybatis](https://mybatis.org/mybatis-3/index.html)
  + [mybatis-generate](https://mybatis.org/generator/index.html) [mybatis-dynamicSQL](https://mybatis.org/mybatis-dynamic-sql/docs/introduction.html)
+ java容错库 [resilience4j](https://resilience4j.readme.io/)
+ 基于redis内存的分布式数据网格java库 [redisson](https://redisson.org/)
+ jvm网络应用框架 [netty](https://netty.io/) [mina](https://mina.apache.org) [grizzly](https://javaee.github.io/grizzly/) [coralreactor](https://www.coralblocks.com/index.php/category/coralreactor/)
+ java字节码操作库 [asm](https://asm.ow2.io/) [javassist](https://www.javassist.org/) [bytebuddy](https://bytebuddy.net/)
+ java编译器检查 [checkerframework](https://checkerframework.org/)
+ 响应式数据库连接驱动库 [r2dbc](https://r2dbc.io/)
+ graphql的java实现 [graphql-java](https://www.graphql-java.com/)
+ java纯函数式编写库 [vavr](https://docs.vavr.io/)

### JVM_FRAMEWORK
+ 云原生后端开发框架 [quarkus](https://quarkus.io/) [micronaut](https://micronaut.io/) [helidon](https://helidon.io/)

### COMMUNICATION PROTOCOL
+ 支持Reactive Streams语义的二进制通讯协议 [rsocket](https://rsocket.io/)

### TOOL
+ java应用编译运行平台 [graalvm](https://www.graalvm.org)
+ java热加载 [jrebel](https://www.jrebel.com)
+ 静态java质量管理工具 [findbugs](https://findbugs.sourceforge.net/) [archunit](https://www.archunit.org/) [checkstyle](https://checkstyle.org/) [OWASP DependencyCheck](https://owasp.org/www-project-dependency-check/)
+ java源代码分析工具 [javaparser](https://javaparser.org)
+ 移动端应用debugger工具 [fbflipper](https://fbflipper.com)
+ 代码覆盖工具 [emma](https://emma.sourceforge.net/index.html) [jacoco](https://www.jacoco.org/jacoco/)
+ CI/CD工具 [jenkins](https://www.jenkins.io) [argo-cd](https://argo-cd.readthedocs.io/en/stable/) [tekton](https://tekton.dev) <font style='color:red'>\*</font>[zadig](https://koderover.com/) <font style='color:red'>\*</font>[circleci](https://circleci.com/) <font style='color:red'>*</font>[drone CI](https://www.drone.io/)
+ 应用服务器 [wildfly](https://www.wildfly.org/)
+ web服务器(servlet容器) [jetty](https://eclipse.dev/jetty/) [undertow](https://undertow.io/)
+ java大数据流处理工具 [flink](https://flink.apache.org/) [spark](https://spark.apache.org/)
+ 云原生容器编排技术 [kubernetes](https://kubernetes.io/)
+ 云原生容器监控工具 [prometheus](https://prometheus.io/)
+ 云原生构建工具 [buildpacks](https://buildpacks.io/)
+ 云原生容器技术 <font style='color:red'>*</font>[docker](https://www.docker.com/) [podman](https://podman.io/) [containerd](https://containerd.io/)
+ 云原生包管理工具 [helm](https://helm.sh/)
+ 云原生服务网格 [istio](https://istio.io/)
+ 云可观测平台 [retina](https://retina.sh) [middleware](https://middleware.io) [hyperdx](https://www.hyperdx.io) [streamdal](https://streamdal.com)
+ 网络安全 <font style='color:red'>*</font>[defguard](https://defguard.net) <font style='color:red'>\*</font>[zitadel](https://zitadel.com)
+ 事件通信监控平台 <font style='color:red'>*</font>[statuspal](https://www.statuspal.io)
+ 服务发现 [consul](https://www.consul.io)
+ 敏感数据加密密钥 [vault](https://www.vaultproject.io)
+ 虚拟化工具 [virtualbox](https://www.virtualbox.org) [vagrant](https://www.vagrantup.com)
+ webGPU引擎 [orillusion](https://www.orillusion.com)
+ 开源markdown编辑器 [joplin](https://joplinapp.org)
+ 开源代码编辑器 [zed](https://zed.dev)
+ github热点速递 [helloGitHub](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzA5MzYyNzQ0MQ==&action=getalbum&album_id=1332112026222641153&scene=173&subscene=&sessionid=undefined&enterid=0&from_msgid=2247517230&from_itemidx=1&count=3&nolastread=1&scene=21#wechat_redirect)
+ 基础设施应用平台 [nix](https://nixos.org) [walrus](https://www.seal.io) [opentofu](https://opentofu.org) [terraform](https://www.terraform.io)

### LANGUAGE
+ jvm编程语言 [kotlin](https://kotlinlang.org) [scala](https://www.scala-lang.org) [groovy](https://groovy-lang.org/) [clojure](https://www.clojure.org)
+ 编程语言[mojo](https://docs.modular.com/mojo) [python](https://www.python.org/) [go](https://go.dev) [rust](https://www.rust-lang.org) [nim](https://nim-lang.org/) [elixir](https://elixir-lang.org/) [elm](https://elm-lang.org/) [julia](https://julialang.org/)
+ javascript语言超集 [typescript](https://www.typescriptlang.org/)

### OTHER_LANGUAGE_FRAMEWORK
+ kotlin平台依赖注入框架 [koin](https://insert-koin.io/)
+ kotlin应用开发框架 [ktor](https://ktor.io/)
+ java/scala web框架 [play](https://www.playframework.com/)
+ go云原生微服务框架 [kratos](https://go-kratos.dev/) [go-zero](https://go-zero.dev/)

### RESOURCE
+ *云原生计算基金会(CNCF Cloud Native Computing Foundation)* [CNCF](https://www.cncf.io/)
+ *apache软件基金会* [apache foundation](https://www.apache.org)
+ Java社区进程(java发展) [jcp](https://www.jcp.org/en/home/index)
+ jvm剖析工具 [visualvm](https://visualvm.github.io/)
+ java版本年鉴 [javaalmanac](https://javaalmanac.io/)
+ redhat开发资源站 [redhat-developer](https://developers.redhat.com/)
+ javascript学习资源网站 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/)
+ python学习资源 [python](https://docs.python-guide.org/)
+ 软件产品、工具交互web平台 [jupyter](https://jupyter.org/)
+ markdown 图像处理库 [mermaid](https://mermaid.js.org/)
+ android开发者网站 [android](https://developer.android.google.cn/)
+ openjdk提供商 [adoptium](https://adoptium.net/) [azul](https://www.azul.com/)

### JAVASCRIPT_TOOL
+ javascript运行时 [node](https://nodejs.org/) [bun](https://bun.sh/) [deno](https://deno.com/)
+ javascript包依赖管理 [npm](https://docs.npmjs.com/)
+ 前端打包工具 [webpack](https://webpack.js.org/)
+ javascript任务运行时 [grunt](https://gruntjs.com/) [gulp](https://gulpjs.com/)

### JAVASCRIPT_FRAMEWORK
+ javascript前端开发框架 [solidjs](https://www.solidjs.com/) [svelte](https://svelte.dev/) [nuejs](https://nuejs.org/) [react](https://react.dev/) [reactnative](https://reactnative.dev/) [nextjs](https://nextjs.org/)
+ javascript web框架(for node) [express](https://expressjs.com/) [koa](https://koa.bootcss.com/)
+ node服务端开发框架 [nestjs](https://nestjs.com/)
+ 跨平台桌面应用程序开发框架 [electron](https://www.electronjs.org/)
+ 用于基于堆栈的虚拟机的二进制指令格式(web平台) [webassembly](https://webassembly.org/)

### C/C++_TOOL
+ c/c++构建工具 [cmake](https://cmake.org/)

---

标<font style='color:red'>*</font>的代表有收费项目
