### PYTHON 3.12
+ 语法特性
  + PEP 695 类型形参语法和type语句
  + PEP 701 f-字符串语法的改进
+ 解释器特性
  + PEP 684 
  + PEP 669 低开销的监控
  + 针对 NameError, ImportError 和 SyntaxError 异常 改进了 'Did you mean ...' 提示消息
+ 数据模型改进
  + PEP 688 使用Python的缓冲区协议
+ 标准库中的重大改进
  + pathlib.Path 类现在支持子类化
  + os 模块获得了多项针对 Windows 支持的改进
  + 在 sqlite3 模块中添加了 命令行界面
  + 基于 运行时可检测协议 的 isinstance() 检测获得了 2 至 20 倍的提速
  + asyncio 包的性能获得了多项改进，一些基准测试显示有 75% 的提速
  + 在 uuid 模块中添加了 命令行界面
  + 由于 PEP 701 中的更改，通过 tokenize 模块生成令牌（token）的速度最多可提高 64%
+ 安全改进
  + 用来自 HACL* 项目的经过正式验证的代码替代 SHA1, SHA3, SHA2-384, SHA2-512 和 MD5 的内置 hashlib 实现。 这些内置实现保留作为仅在当 OpenSSL 未提供它们时使用的回退选项
+ C API的改进
  + PEP 697 不稳定C API层
  + PEP 683 永生对象
+ CPython实现的改进
  + PEP 709 推导式内联化
  + 对Linux perf性能分析器的CPython支持
  + 在受支持的平台上实现栈溢出保护
+ 新的类型标注特性
  + PEP 692，使用 TypedDict 来标注 **kwargs
  + PEP 698，typing.override() 装饰器
+ 重要的弃用、移除或限制
  + PEP 623: 在 Python 的 C API 中移除 Unicode 对象中的 wstr，使每个 str 对象的大小缩减至少 8 个字节
  + PEP 632: 移除 distutils 包。 请参阅 迁移指南 了解有关替换其所提供的 API 的建议。 第三方 Setuptools 包将继续提供 distutils，如果你在 Python 3.12 及更高版本中仍然需要它的话
  + gh-95299: 不在使用 venv 创建的虚拟环境中预装 setuptools。 这意味着 distutils、setuptools、pkg_resources 和 easy_install 默认将不再可用；要访问这些工具请在 激活的 虚拟环境中运行 pip install setuptools
  + 移除了 asynchat、asyncore 和 imp 模块，以及一些 unittest.TestCase 方法别名