调试脚本
==================================================
开发脚本时有些事可能会出错，未配对的标签引起的XML验证错误，错误的字符串字号错误（比如用 “” instead of ""，常发生于字处理软件，
而不是从纯文本文件中复制的结果）


调试策略
--------------------------------------------------

XBMC调试log
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
在XBMC系统设置中，打开调试开关然后查看它生成的log文件，log文件创建在.xbmc/temp 目录下。

Linux 用户可以用

    >>>tail -f xbmc.log

命令查看log。

可以用普通编辑器或在你平台上可用的其它工具来查看log。

脚本调试
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
目前，还不能通过XBMC Python代码调试，因为Python模块是直接从C++中注入的，因此没有普遍意义上的Python库，显示这需要继续完善 [#]_。

也可以在代码中植入打印语句，它们将会在调试log中出现，由于会生成大量内容，采取以下形式。
::

    print '#######################################'
    print 'what I actually want to see'


可以帮助你很容易定位它。

可从以下链接中找到一些安装Python 调试器的有用指导：
* http://wiki.xbmc.org/index.php?title=HOW-TO_debug_Python_Scripts
* http://wiki.xbmc.org/index.php?title=HOW-TO_debug_Python_Scripts_with_Eclipse

NetBeans Python组件内置了一个调试器。

.. rubric:: 脚注

.. [#] 参见 http://forum.xbmc.org/showthread.php?t=80984
