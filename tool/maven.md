### Dependency Management(依赖管理)
+ 依赖传递
  maven依赖传递是maven的核心机制之一,能够在一定程度上简化maven的依赖配置,如下:
  ```mermaid
  graph LR
  A --> B
  B --> C
  A -.-> C
  ```
  项目A依赖于项目B,项目B又依赖于项目C,此时B是A的直接依赖,C是A的间接依赖
  > maven的依赖传递机制是指:不管maven项目存在多少间接依赖,pom中都只需要定义其直接依赖,不必定义任何间接依赖,
  > maven会自动读取当前项目各个直接依赖的pom,将那些必要的间接依赖以传递性依赖的形式引入到当前项目中.maven的
  > 依赖传递机制能够帮助用户一定程度上简化pom的配置.
  
  基于A、B、C三者的依赖关系,根据maven的依赖传递机制,我们只需要在项目A的pom中定义其直接依赖B,在项目B的pom中
  定义其直接依赖C,maven会解析A的直接依赖B的pom,将间接依赖C以传递性依赖的形式引入到项目A中.
  通过这种依赖传递关系,可以使依赖关系树迅速增长到一个很大的量级,很有可能会出现依赖重复,依赖冲突等情况,maven针对
  这些情况提供了如下功能进行处理.
  + 依赖范围(Dependency scope)
  + 依赖调解(Dependency mediation)
  + 可选依赖(Optional dependencies)
  + 排除依赖(Excluded dependencies)
  + 依赖管理(Dependency management)
+ 依赖范围
  maven对项目进行编译、测试和运行时,分别使用三套不同的classpath,项目构建时,在不同阶段引入到classpath中的依赖是不同的.
  我们可以在pom的依赖声明中使用`scope`元素来控制依赖与三种classpath(编译classpath、测试classpath、运行classpath)之间的关系,这就是依赖范围.
  
  |    文件    |         目录          |                    描述                     |
  |:--------:|:-------------------:|:-----------------------------------------:|
  | java源代码  |    src/main/java    |              开发者编写业务逻辑代码的目录               |
  | java资源文件 | src/main/resources  |          开发者管理逻辑代码需要的资源和配置文件的目录           |
  |  测试源代码   |    src/test/java    |               开发者编写测试代码的目录                |
  |  测试资源文件  | src/test/resources  |          开发者编写的测试所需要的资源和配置文件的目录           |
  |  打包输出文件  |       target        | 编译后的代码(字节码),都在当前项目target目录下,也就是程序真正要运行的文件 |
  |  编译输出文件  |   target/classes    |  存放src/main目录编译后的class文件,又称为编译classpath   |
  |  编译输出文件  | target/test-classes |  存放src/test目录编译后的class文件,又称为测试classpath   |
  + 项目实际运行的是target/classes目录下的字节码文件,而src/main/java文件夹下面是源代码文件.
  + 同理,实际运行的测试是运行的target/test-classes目录下的字节码文件,src/test/java也是源代码文件.
  > 可选的配置有*compile*、*test*、*provided*、*runtime*、*system*、*import*,若不指定默认是 *`compile`*,target/classes和test-classes目录是不存依赖的jar文件的,项目依赖的jar直接使用的是本地maven仓库的,依赖范围更为通俗的理解,
  > 其实就是给依赖的jar打标记,例如:将A依赖包标记为*compile*,maven就知道A依赖包不仅运行classes目录的代码要用,运行test-classes也要用,最终的生成的jar/war包也要用(最终的打包也是会把依赖的jar打到包里给程序使用)
  
  + compile(编译依赖范围):使用此依赖范围的maven依赖,对于编译、测试、运行三种classpath都有效,典型的例子是spring-core,在编译、测试和运行的时候都需要使用该依赖
  + test(测试依赖范围):只对测试classpath有效,在编译主代码或者运行项目时将无法使用此类依赖,典型的例子是junit,它只有在编译测试代码及运行测试的时候才需要.
  + provided(提供依赖范围):对于编译和测试classpath有效,但在运行时无效,典型的例子是servlet-api,编译和测试项目的时候需要该依赖,如果需要打成war包在tomcat等servlet容器中运行,由于环境已经提供了该依赖,不需要maven重复引入,所以scope设置为provided不会参与项目的打包(如果是打成jar,该scope不会产生实际影响)
    > 这里需要跟<dependency>下的字标签<optional>有一个关联比较,两者的区别在于
    > 1. <optional>为true表示依赖不会传递,例如:x依赖B,B又依赖于A(x->B->A),则A中设置<optional>为true的依赖不会被传递到x中,假如当前项目某个依赖<optional>为true并不会影响该依赖在当前项目的打包,他只会影响所依赖当前项目的其他项目打包
    > 2. <scope>为provided代表的是该依赖不参与打war包  
    
    在目标环境中已经提供了这个依赖,无需再提供
  + runtime(运行时依赖范围):对于测试和运行classpath有效,但在编译主代码时无效(对编译的classpath无效),典型的例子是jdbc驱动实现,项目主代码的编译只需要JDK提供的JDBC接口,只有在执行测试或者运行项目的时候才需要实现上述接口的具体jdbc驱动.
  + system(系统依赖范围):需要引用第三方本地jar包的时候使用.
  + import(导入依赖范围):该依赖范围只能与`dependencyManagement`元素配合使用,其功能是将目标pom.xml文件中dependencyManagement的配置导入合并到当前pom.xml的dependencyManagement中.
依赖范围与三种classpath的关系一览表:

|   依赖范围   | 编译classpath | 测试classpath | 运行classpath |      例子       |
|:--------:|:-----------:|:-----------:|:-----------:|:-------------:|
| compile  |     ✔︎      |     ✔︎      |     ✔︎      |     log4j     |
|   test   |      -      |     ✔︎      |      -      |     junit     |
| provided |     ✔︎      |     ✔︎      |      -      |  servlet-api  |
| runtime  |      -      |     ✔︎      |     ✔︎      |  JDBC-driver  |
|  system  |     ✔︎      |     ✔︎      |      -      | 非maven仓库的本地依赖 |