### `@SuppressWarnings`注解的解释
该注解用于告诉*编译器*在编译过程中忽略特定类型的警告信息,通过使用这个注解,你可以指示编译器不要发出某些警告,从而减少在编译时看到不相关的警告
使用方式如下: 在*类*、*属性*、*方法*、*方法参数*、*构造函数*或者*局部变量*上`@SuppressWarnings(value = {"警告类型1","警告类型2",...})`
或者更简洁地`@SuppressWarnings("警告类型")`,其中的value参数是一个字符串数组,用于指定要忽略的*警告类型*,这些警告类型可以是编译器定义的警告类型
也可以是一些自定义的字符串,常见的警告类型包括:

+ <font style="color:blue">all</font> : 忽略所有类型的警告,启用所有警告
+ <font style="color: blue">unused</font> : 忽略未使用的代码或变量的警告
+ <font style="color: blue">restriction</font> : 忽略使用了受限制的API的警告,通常用于访问非公开或不稳定的API
+ <font style="color: blue">UnusedReturnValue</font> : 忽略方法的返回值未被使用的警告
+ <font style="color:blue">auxiliaryclass</font> : 有关辅助类在源文件中隐藏,但在其他文件中使用的警告
+ <font style="color:blue">cast</font> : 有关使用了不必要转换的警告
+ <font style="color:blue">classfile</font> : 有关与类文件内容相关的问题的警告
+ <font style="color:blue">deprecation</font> : 忽略使用已过时的API的警告,有关使用了已过时项的警告
+ <font style="color:blue">dep-ann</font> : 有关项在JavaDoc中标记为已过时但未使用@Deprecated注释的警告
+ <font style="color:blue">divzero</font> : 有关除以常量整数0的警告
+ <font style="color:blue">empty</font> : 有关if之后没有语句的警告
+ <font style="color:blue">exports</font> : 有关与模块导出相关的问题的警告
+ <font style="color:blue">fallthrough</font> : 有关从switch语句的一个case向下顺序执行到下一个case的警告
+ <font style="color:blue">finally</font> : 有关finally子句未正常终止的警告
+ <font style="color:blue">missing-explicit-ctor</font> : 有关导出的程序包中的公共和受保护类中缺少显式构造器的警告
+ <font style="color:blue">module</font> : 有关模块系统相关问题的警告
+ <font style="color:blue">opens</font> : 有关与模块打开相关的问题的警告
+ <font style="color:blue">options</font> : 有关与使用命令行选项相关的问题的警告
+ <font style="color:blue">overloads</font> : 有关与方法重载相关的问题的警告
+ <font style="color:blue">overrides</font> : 有关与方法覆盖相关的问题的警告
+ <font style="color:blue">path</font> : 有关命令行上的路径元素无效的警告
+ <font style="color:blue">processing</font> : 有关与注释处理相关的问题的警告
+ <font style="color:blue">rawtypes</font> : 忽略使用不带泛型类型的原始类型的警告,有关使用了原始类型的警告
+ <font style="color:blue">removal</font> : 有关使用了标记为待删除的API的警告
+ <font style="color:blue">requires-automatic</font> : 有关在requires子句中使用自动模块的警告
+ <font style="color:blue">requires-transitive-automatic</font> : 有关requires过渡中的自动模块的警告
+ <font style="color:blue">serial</font> : 忽略未添加序列化字段的告警,有关未提供序列版本ID的可序列化类的警告.此外还警告有关可串行化元素对非公共成员的访问
+ <font style="color:blue">static</font> : 有关使用实例来访问静态成员的警告
+ <font style="color:blue">strictfp</font> : Warnaboutunnecessaryuseofthestrictfpmodifier
+ <font style="color:blue">synchronization</font> : 有关尝试在基于值的类的实例上同步的警告
+ <font style="color:blue">text-blocks</font> : 有关文本块缩进中的空格字符不一致的警告
+ <font style="color:blue">try</font> : 有关与使用try块(例如try-with-resources)相关的问题的警告
+ <font style="color:blue">unchecked</font> : 忽略未经检查的警告,通常在使用泛型时出现,有关未检查操作的警告
+ <font style="color:blue">varargs</font> : 有关潜在不安全的vararg方法的警告
+ <font style="color:blue">preview</font> : 有关使用预览语言功能的警告
+ <font style="color:blue">none</font> : 禁用所有警告