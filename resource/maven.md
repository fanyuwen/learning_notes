### Dependency Management(依赖管理)
[本文地址](https://blog.csdn.net/weixin_43888891/article/details/130517016)
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
  + system(系统依赖范围):需要引用第三方本地jar包的时候使用.[system解析](https://blog.csdn.net/weixin_43888891/article/details/130611728)
  + import(导入依赖范围):该依赖范围只能与`dependencyManagement`元素配合使用,其功能是将目标pom.xml文件中dependencyManagement的配置导入合并到当前pom.xml的dependencyManagement中.[import解析](https://blog.csdn.net/weixin_43888891/article/details/130520345)
依赖范围与三种classpath的关系一览表:

|   依赖范围   | 编译classpath | 测试classpath | 运行classpath |      例子       |
|:--------:|:-----------:|:-----------:|:-----------:|:-------------:|
| compile  |     ✔︎      |     ✔︎      |     ✔︎      |     log4j     |
|   test   |      -      |     ✔︎      |      -      |     junit     |
| provided |     ✔︎      |     ✔︎      |      -      |  servlet-api  |
| runtime  |      -      |     ✔︎      |     ✔︎      |  JDBC-driver  |
|  system  |     ✔︎      |     ✔︎      |      -      | 非maven仓库的本地依赖 |

+ 依赖范围对传递依赖的影响  
  项目A依赖于项目B,B又依赖于项目C,此时我们可以将A对于B的依赖称之为第一直接依赖,B对于C的依赖称之为第二直接依赖.
  B是A的直接依赖,c是A的间接依赖,根据maven的依赖传递机制,间接依赖C会以传递性依赖的形式引入到A中,但这种引入并不是无条件的,它会受到依赖范围的影响.
  传递性依赖的依赖范围受第一直接依赖和第二直接依赖的范围影响,如下表所示:
  
  |          | compile  | test | provided | runtime  |
  |:--------:|:--------:|:----:|:--------:|:--------:|
  | compile  | compile  |  -   |    -     | runtime  |
  |   test   |   test   |  -   |    -     |   test   |
  | provided | provided |  -   | provided | provided |
  | runtime  | runtime  |  -   |    -     | runtime  |
  注:上表中,左边第一列表示第一直接依赖的依赖范围,上边第一行表示第二直接依赖的依赖范围.交叉部分的单元格的取值为传递性依赖的依赖范围,若交叉单元格取值为"-",则表示该传递性依赖不能被传递.
  通过上表,可以总结出以下规律:
  + 当第二直接依赖的范围是compile时,传递性依赖的范围与第一直接依赖的范围一致.
  + 当第二直接依赖的范围是test时,传递性依赖不会被传递.
  + 当第二直接依赖的范围是provided时,只传递第一直接依赖的范围也为provided的依赖,且传递性依赖的范围也为provided.
  + 当第二直接依赖的范围是runtime时,传递性依赖的范围与第一直接依赖的范围一致,但compile例外,此时传递性依赖的范围为runtime.
+ 依赖调节  
  maven的依赖传递机制可以简化依赖的声明,用户只需要关心项目的直接依赖,而不必关心这些直接依赖会引入哪些间接依赖.但当一个间接依赖存在多条引入路径时,为了避免出现依赖重复的问题,maven通过依赖调节来确定间接依赖的引入路径.
  依赖调节遵循以下两条原则:
  + 引入路径短者优先.
  + 先声明者优先.  
  以上两条原则,优先使用第一条原则解决,第一条原则无法解决,再使用第二条原则解决.
  **引入路径短者优先**
  > 引入路径短者优先,顾名思义,当一个间接依赖存在多条引入路径时,引入路径短的会被解析使用.  
  
  例如,A存在这样的依赖关系:
  + A -> B -> C -> D(1.0)
  + A -> X -> D(2.0)  
  D是A的间接依赖,但两条引入路径上有两个不同的版本,很显然不能同时引入,否则造成重复依赖的问题.
  根据maven依赖调节的第一个原则:引入路径短者优先,D(1.0)的路径长度为3,D(2.0)的路径长度为2,
  因此该间接依赖D(2.0)将从A -> X -> D(2.0)路径引入到A中.
  **先声明者优先**
  > 先声明者优先,顾名思义,在引入路径长度相同的前提下,pom文件中依赖声明的顺序决定了间接依赖会不会被解析使用,
  > 顺序靠前的优先使用.  

  例如,A存在以下依赖关系:
  + A -> B -> D(1.0)
  + A -> X -> D(2.0)  
  D是A的间接依赖,其两条引入路径的长度都是2,此时maven依赖调节的第一原则已经无法解决,需要使用第二原则:先声明者优先.
  A的pom文件中配置如下:
  ```xml
     <dependencies>
        <!-- ...-->
        <dependency>
            <!-- ...-->
            <artifactId>B</artifactId>
            <!-- ...-->
        </dependency>
        <!-- ...-->
        <dependency>
            <!-- ...-->
            <artifactId>X</artifactId>
            <!-- ...-->
        </dependency>
        <!-- ...-->
     </dependencies>
  ```
  有以上配置可以看出,由于B的依赖声明比X靠前,所以间接依赖D(1.0)将从A -> B -> D(1.0)路径引入到A中.
+ 可选依赖
  可选依赖就是指的`optional`标签,关于optional的详解:[optional标签](https://blog.csdn.net/weixin_43888891/article/details/130510971)
+ 排除依赖
  传递性依赖可以帮助我们简化项目依赖的管理,但是同时也会带来其他的不必要的风险,例如:会隐式地引入一些依赖,这些依赖可能并不是我们希望引入的,或者这些隐式引入的依赖是`SNAPSHOT`版本的依赖,依赖的不稳定导致了我们项目的不稳定.
  在我们的项目中,`spring-boot-starter-test`依赖中排除了`junit-vintage-engine`依赖是由于我们使用的spring-boot版本是2.2.6-RELEASE,对应的junit版本是5.x,但`junit-vintage-engine`依赖中包含了4.x版本的junit,
  此时我们就可以将该依赖排除.
  ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
  ```
  关于exclusions元素及排除依赖说明如下:
  + 排除依赖是控制当前项目是否使用其直接依赖传递下来的间接依赖.
  + exclusions元素下可以包含若干个exclusion子元素,用于排除若干个间接依赖.
  + exclusion元素用来设置具体排除的间接依赖,该元素包含两个子元素`groupId``artifactId`,用来确定需要排除的间接依赖的坐标信息.
  + exclusion元素中只需要设置`groupId``artifactId`就可以确定需要排除的依赖,无需指定版本`version`.
+ 依赖归类
  在我们实际的开发过程中,我们可能会需要整合很多第三方框架,在整合这些框架的时候,往往需要在pom.xml里面添加多个依赖来完成整合,而这些依赖往往是需要保持相同版本的,
  在升级框架的时候,都是要统一升级到一个相同的版本.
  如下图,我们可以看到,在引入dubbo框架的时候,我们需要引入两个相关的依赖,而且版本号是相同的,这个时候,我们就可以把对应的版本号提取出来,放到`properties`标签里面,
  作为一个全局参数来使用.类似于Java语言中抽象的思想.
  ```xml
  <project>
    <properties>
        <java.version>1.8</java.version>
        <dubbo.version>2.7.3</dubbo.version><!-- 定义共用properties-->
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>${dubbo.version}</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo</artifactId>
            <version>${dubbo.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
  </project>
  ```
  这时候,我们可以看到,如果在将来的某一天我们需要升级dubbo框架对应的版本,只需要修改`properties`中的版本号,就能将所有依赖的版本一起升级
+ 依赖管理
  依赖管理就是指的`dependencyManagement`标签,直接看这一篇文章即可:[dependencyManagement解析](https://blog.csdn.net/weixin_43888891/article/details/130520345)