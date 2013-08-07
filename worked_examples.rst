实例
==================================================
以下实例位于XBMC *音乐* 部分

静态列表
--------------------------------------------------
这是一个最简单的组件，用来介绍开发'xbmc.python.pluginsource' 扩展组件所需基本构成。

目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 * 介绍任何xbmc.python.pluginsource附加组件的基本构成
 * 演示一个实际组件

编码
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
到附录A，复制那里文件的内容并将其粘贴至指定地方。这是组件基本启动脚本的内容，如果到此你已依照实例，现在你应有一个addons.xml文件指向你的启动脚本。
::

    1 """
    2 XBMC AddonDeveloper's Guide
    3 示例1 - 基本插件结构
    4 演示创建一个静态列表
    5
    6 注意，这是用函数做的，你也可以用类来实现
    7
    8 作者: Ashley Kitson
    9 """
    10
    11 # 步骤1 - 加载xbmc核心支持，建立环境
    12 import xbmcplugin
    13 import xbmcgui
    14 import sys
    15
    16 # 魔法；该插件实例id - 整型
    17 thisPlugin = int(sys.argv[1])
    18
    19 # 步骤2 - 创建支持函数(或类)
    20
    21  def createListing():
    22      """
    23      创建XBMC能显示的目录列表
    24
    25      @return list
    26      """
    27	    listing = []
    28	    listing.append('Thefirstitem')
    29	    listing.append('Theseconditem')
    30      listing.append('Thethirditem')
    31	    listing.append('Thefourthitem')
    32	    return listing
    33
    34
    35  def sendToXbmc(listing):
    36	    """
    37	    发送一个列表给XBMC作为目录列表显示
    38	    插件总是产生一个列表
    39
    40	    @param list listing
    41	    @return void
    42	    """
    43	    # 存取全局插件id
    44	    global thisPlugin
    45
    46	    # 将每个条目发送给xbmc
    47	    for item in listing:
    48	        listItem = xbmcgui.ListItem(item)
    49	        xbmcplugin.addDirectoryItem(thisPlugin, '', listItem)
    50
    51	    # 告诉xbmc已经完成创建目录列表
    52	    xbmcplugin.endOfDirectory(thisPlugin)
    53
    54  # 步骤3 - 运行程序
    55 sendToXbmc(createListing())

分析
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
插件（plugin）类型的组件只需做一件事，显示一个XBMC能够解释成某种播放内容或调用额外功能的链接的列表。程序示例说明了基本的程序结构：

 * 创建要显示内容的列表
 * 将其给XBMC 显示

为使介绍更清晰，我将这些分解成两个部分，语言纯化论者（purist）也许会说你应该同时创建和显示，而且你也能够这样做。但是，如果可能，从维护的角度看，
把功能分解成小块是个不错的主意。随着程序越来越大，越来越复杂，也许运行就越来越慢。（假如你正做屏幕抓取，需要从别的网络抓取数据），
也许需要恢复到同时创建和显示，这样程序在做什么有用的事情时，不会给用户留下空白屏幕。

该示例创建列表。它提供的列表目前还什么都不做，所以先别失望，示例2会扩展它做些事。

L11 – 需要导入一些支持库，在python核心“sys”库之外，我们始终需要加入XBMC GUI（xbmcgui）和 Addon（xbmcaddon）库。
XBMC也在其核心库（xbmc）之外提供一个插件帮助库（xbmcplugin），这些都通过Pydocs脚本文档化，参见当前XBMC Python 库API参考。

L17 – 这是在本例之外文档中找不到的一小段“魔法”（magic）脚本，这一行获取当前运行插件实例也即你的id，需要能够辨识正在处理的是哪一个，
如果在脚本前面获取和设置该id，就可以在以后需要时访问它。大量XBMC功能都需要这个id。在函数定义中（或实际编写的类中），
总可以通过告诉函数这是个全面变量来访问它，这些是第44行语句所做的。它使得函数可用范围的变量在函数内部也可用。

L21 – 定义一个函数，它的工作是创建要显示的列表，真正的工作由此继续，我用的是原生python列表对象（与PHP等语言中的数组类似）来保存列表信息。
正如你所见，这是一个非常简单的列表。

L35 – 定义一个函数，它将列表条目发送给XBMC显示为一个标准目录列表。

L47 – 遍历列表条目，给每一项创建一个xbmcgui.ListItem()，然后通过xbmcplugin.addDirectoryItem()发送给XBMC， 
ListItem()和 addDirectoryItem()方法都有额外参数，可用于对生成的列表做些有用的事，但此刻为清晰起见，我没有用到这些参数。

L52 – 需要告诉XBMC发送显示条目完毕，这一行就干这事，它也是脚本中最后的有效行，控制已经交还给XBMC，而我们的工作也算完成了。

L55 – 运行主程序，调用sendToXbmc() 函数，将调用createListing()生成的结果传入，可以将它写成这样两行::

    list = createListing()
    sendToXbmc(list)

但是在较简单的情形下，在主代码清单中这种缩写方式也是可接受的。把第48-49行写成一行::

    xbmcplugin.addDirectoryItem(thisPlugin, '', xbmcgui.ListItem(item))

正如你所见，包装行使得可读性不那么好，另一个替代方案是将其分解成这样几行::

    xbmcplugin.addDirectoryItem(
        thisPlugin,
        '',
        xbmcgui.ListItem(item)
    )

这就比较好懂了。无论选择何种风格，为你自己或要继续你工作的人考虑，请努力保持可读性。

动态列表 – 不是基于刮削器（scraper）
--------------------------------------------------
建立在之前示例之上，说明如何动态生成目录列表来显示，本例使用你机器中的由其它应用控制的数据。

要使例子工作，需要安装Gpodder，一款podcasting软件，Gpodder在Linux 和 Windows 平台上都可使用。
可以从 http://gpodder.org/ 或在Fedora 和 Ubuntu 中通过包管理器获得Gpodder。

将软件安装在你的开发机上，启动它，添加一个新订阅确保它能播放（如果不能，就设法解决该问题），现在添加更多订阅并下载一些剧集。

现在打开偏好对话框，点击高级按钮，记下 **download_dir** 位置，以后将用到它。

现在，XBMC可以由它自己播放Gpodder下载的内容，想证明这一点，点XBMC *音乐* 部分，添加新媒体源，并指定之前记下download_dir，
在XBMC中浏览新媒体源，点击一个文件，如果列表显示'.m3u' 后缀，并且只有一条，就会播放该剧集，否则它将显示可播放剧集子目录列表。
OK，你会说那又怎样，好，在列表中也会看到下载文件所在的文件夹，也就是说，看起来得到了一个重复的列表，有些困惑是不是？
本例向你说明如何通过一个组件消除这些。

目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
* 利用外部媒体源动态生成的信息创建一个简单列表
* 说明如何与xbmc域外python库接口打交道
* 将模块引入开发过程

编码
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
需要给本例添加一些额外的文件，前往附录B，复制文件内容然后粘贴在指定地方，你也需要之前收集到的download_dir值。

addondev2.py
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    1     """
    2     XBMCAddonDeveloper'sGuide
    3     Example2 - 继续
    4     	演示创建动态列表
    5     	演示使用自己的模块和类
    6
    7     注意，这是用函数做的 – 你也可以用类来实现
    8
    9     作者: Ashley Kitson
    10    """
    11    #
    12    # 第一步 – 加载核心支持并建立环境
    13    #
    14    import sys
    15    import xbmcplugin
    16
    17    # addon id - 组件目录名
    18    _id = 'plugin.audio.addon-dev-ex2'
    19    # 资源目录
    20    _resdir = "special://home/addons/" + _id + "/resources"
    21    # 把我们的库添加到python搜索路径
    22    sys.path.append(_resdir + "/lib/")
    23
    24    #从模块中导入worker类
    25    import gpodder as worker
    26
    27    #魔法（magic）；该插件实例id – 整理
    28    _thisPlugin = int(sys.argv[1])
    29
    30    #
    31    # 第二步 – 初始化支持类
    32    #
    33    creator = worker.creator(_thisPlugin, _id)
    34    sender = worker.sender(_thisPlugin)
    35
    36    #
    37    # 第三步 – 运行程序
    38    #
    39    sender.send(creator.get())
    40    xbmcplugin.endOfDirectory(_thisPlugin)

resources/lib/gpodder.py
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

    """
    4    XBMC组件开发者指南
    5    示例2 – 演示从Gpodder目录创建动态列表
    6	 该模块提供创建和显示内容的类
    7	 create and display the contents
    8
    9    作者: Ashley Kitson
    10   """
    11   # 使XBMC和系统模块可用
    12   import xbmc
    13   import xbmcplugin
    14   import xbmcgui
    15   import dircache
    16   import fnmatch
    17
    18   # 为settings xml标签定义全局常量
    19   __GPOPATH_TAG__ = 'gpoPath'
    20
    21   # 定义类
    22
    23   class creator:
    24	     """
    25	     负责创建将要显示的条目列表
    26	     """
    27	     #
    28	     # 私有方法
    29	     #
    30
    31	     # 当前插件实例标识符
    32	     _pluginId = 0
    33	     # 插件名
    34	     _pluginName = ''
    35
    36	     def __init__(self, pluginId, pluginName):
    37	        """
    38	        构造函数
    39	        @parm int pluginId - 当前插件实例标识符
    40	        @param string pluginName - 插件名
    41	        """
    42	        self._pluginId = pluginId
    43	        self._pluginName = pluginName
    44
    45	     def _createList(self):
    46	        """
    47	        创建动态列表
    48	        @access private
    49	        @returns list
    50	        """
    51	        # 为gpodder目录获取用户设置
    52	        dir = xbmcplugin.getSetting(self._pluginId, __GPOPATH_TAG__)
    53	        # 获取gpodder目录内容
    54	        dirContent = dircache.listdir(dir)
    55	        # 解析所有.m3u文件内容
    56	        dirContent = fnmatch.filter(dirContent, '*.m3u')
    57
    58	        # 创建列表
    59	        listing = []
    60	        for file in dirContent:
    61	            uri = xbmc.translatePath(dir + '/' + file)
    62	            label = file.replace('.m3u', '')
    63	            listing.append([label, uri])
    64
    65	        return listing
    66
    67
    68	    #
    69	    # 公有API
    70	    #
    71
    72	    def get(self):
    73	        """
    74	        刷新和获取当前显示列表
    75	        @access public
    76	        @returns list
    77	        @usage	c = example2.creator()
    78	        list = c.get()
    79	        """
    80	        return self._createList()
    81
    82  class sender:
    83	"""
    84	负责发送输出到XBMC
    85	"""
    86	# current instance of plugin identifier
    87	_pluginId = 0
    88
    89	    def __init__(self,pluginId):
    90	        """
    91	        构造函数
    92	        @parm int pluginId -当前插件实例标识符
    93	        """
    94	        self._pluginId = pluginId
    95
    96
    97	    def send(self, listing):
    98	        """
    99	        发送输出到XBMC
    100	        @param list listing - 显示条目列表
    101	        @return void
    102	        """
    103	        # 创建条目列表
    104	        # item[0] = itemlabel（条目标签）
    105	        # item[1] = itemuri（条目uri）
    106	        for item in listing:
    107	            listItem = xbmcgui.ListItem(item[0])
    108	            xbmcplugin.addDirectoryItem(
                        self._pluginId, item[1], listItem)

分析
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
该组件扩展我们已经学过的东西，首先是一些简单的事情；

该组件使用了一个设置文件(resources/settings.xml)，其基本格式在本文档前面解释过了，我们需要这个是因为不同机器上Gpodder下载文件的位置各不相同。
本例并不说明如何在线编辑其值，它会在今后示例中出来。此刻如果需要改变它的值，可以简单地在编辑器中完成。

该组件利用一个模块扩展其核心功能，首要原因是gpodder.py模块文件中的代码有潜力在其它组件中重用；第二个原因是把功能分解在小文件里更好管理，
它允许我们向您展示如何从组件目录层次中的分立模块中调取自己的类。

Addondev2.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
L14 – 导入（调取）所需外部模块

L17 至 22 – 需要告诉Python到哪里去找到模块文件，这些行建立路径并将其添加到python搜索路径。_id变量以后也会用到

L25 – 已经告诉Python 哪里可以找到库模块，现在装载它们，我把Godder模块重命名为'worker'，这不是必需的，但从语义可读性上看，有时这样做比较好

L33 和 34 – 现在初始化将要使用的类，这些有效地替换了前个例子中的函数，运作类需要传入变量值

L39 – 运行程序。像之前示例，运用缩写方式运行一个方法

L40 – 与例1不同，显式告诉XBMC我们已经完成工作，任务分离使得易于通过主程序脚本添加功能，而不用担心模块退出目录列表

resources/lib/gpodder.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
L19 – Python其实并不以PHP等语言同样的方式真正支持常量，惯例是用TAG来标识你想以常量对待的变量，该变量匹配我们要从设置文件中读取的设置名

L23 – 这里我们定义将要负责创建内容列表的类，它在L36（第36行）的构造器只是简单地保存传过来的值，以待后用。

L45 – 在此我们定义执行创建列表实际工作的方法，它的名字里有一个位置下划线，再说一下，Python并不真正支持私有或保护方法的概念，它纯粹是个惯例

L52 – 从当前用户设置文件里为gpoPath设置获取设置，该xbmc方法可时获取当前用户组件变量简单易行

L54 – 用Python dircache模块来获取gpodder目录内容

L56 – 使用Python fnmatch库去除目录里不是以'.m3u'结尾的内容

我们也可以把之前三行合并连接在一起::

    dirContent = fnmatch.filter(
    dircache.listdir(
    xbmcplugin.getSetting(self._pluginId,__GPOPATH_TAG__)
    ),'*.m3u')

L60 至 63 – XBMC确实需要两个信息来操作目录列表：

 * 给用户显示的标签– 这是我们在例1里做过的
 * 实际动作URI，它可以是一个url (http://), 一个文件位置(/home/user/bal/blah.mp3) 或任何URI格式。

 XBMC知道如何处理.m3u（播放列表文件），因此我们要做的只是给出.m3u文件位置的列表。

L61 – **重要的一点** ，这是一个我们需要用来确保所用文件路径被转换成XBMC运行平台能够理解的东西

L62 – 获取标签，只是简单把'.m3u'从文件名中剔除

L72 – 一个完成实际工作函数的“公用”包装器

L82 – 输出列表到XBMC的发送类的定义

L106 至 108 – 本例和例1处理的唯一区别是我们传递了一个额外参数给xbmcplugin.addDirectoryItem()。
第二个参数是当用户点击列表时我们要显示内容的URI

家庭作业
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
上述实例在每个条目被浏览到时没有显示podcast图片文件，看一看Gpodder如何在下载目录里存放这些信息，提取文件位置添加到列表中。

Dynamic listing – 更好的RSS阅读器？
--------------------------------------------------
XBMC的一个比较令人失望的地方是不能读取RSS新闻来源，有一些办法可以做到这点，但是打开浏览器阅读新闻条目如何？继续读下去。 

目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* 演示利用其它插件（addon）作为模块库（出于重用目的）
* 演示从目录列表中调用plugin插件
* 演示在xbmc内部启动另一个程序

要让该实例工作，首先需要安装XBMC团队提供的Rss编辑器（缺省组件库）。

编码
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
本实例脚本包含在附件C中，将其复制和粘贴至./xbmc/addons目录下script.rss- chippyash 目录，完整的代码清单也演示了一些符合XBMC要求的注释内容，这些注释含有版权许可信息，如果你要发布该组件，应将其加入到代码中，与再在此处复制整个代码清单相比，我更倾向直接从源代码中抽出相关行来讨论。

分析
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
addon.xml
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
这里唯一真正有趣的事是“requires”
::

    <requires>
        <import addon="script.rss.editor" version="1.5.9"/>
    </requires>

它告诉系统我们依赖于RSS编辑器组件

rss-example.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

行35 至 42
::

    # rwparris rss编辑器组件名
    __RSSEDITOR_NAME__ = "script.rss.editor"
    # 给RSS编辑器创建库路径
    _path = xbmcaddon.Addon(__RSSEDITOR_NAME__).getAddonInfo('path')
    sys.path.append(xbmc.translatePath(os.path.join(_path, 'resources', 'lib')))
    # 建立XML解析器语言引导程序
    __language__ = xbmcaddon.Addon(__RSSEDITOR_NAME__).getLocalizedString

这是我们准备系统并将RSS编辑器代码包括进脚本搜索路径的地方。__language__全局变量是必需的，因为将要使用的XML解析类需要。

行66 至 79
::

    # 是否有URL参数
    params = creator.getparams()
    try:
        url = params["url"]
    except:
        url = None

    if url is None:
        # 列表
        sender.send(creator.get())
        xbmcplugin.endOfDirectory(_thisPlugin)
    else:
        # 显示新闻条目
        sender.displayNews(url)

此处，我们获得命令行传入的所有参数，看是否有一个参数名为“url”。如果有，就调用sender.displayNews(url)；否则，与平常一样，创建一个列表并显示它。

resources/lib/rss_chippyash.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
L33
::

    from xmlParser import XMLParser

从RSS编辑器导入XML解析器类。

L45
::

    __BROWSER__ = 'google-chrome'

设置启动浏览器命令，我的系统里装的是Google Chrome，你也可以换成别的。

L47 – 定义rssFeed类，没什么特别的，简单地获得一个馈送（feed）URL，让它提取标题和文章链接信息。

L101
::

    feeds = XMLParser().getCurrentRssFeeds()

使用RSS编辑器的XMLParser类获取XBMC Rss馈送文件并解释之。

L106
::

    self.feeds.append(rssFeed(feed['url']))

给每个已有馈送（feed）创建一个rssFeed类对象

L108 – _createListAll方法被修改用来通过馈送数据结构里的信息创建用于发送类（sender）以标准格式编列的列表。

L141 – getparams()方法直接从Shoutcast组件而来，说明如何从命令行获取参数。

L196
::

    url="plugin://"+self._pluginName+"?url="+item[1]

仅需稍微修改一下send()方法，就可以创建一个XBMC将其解释为用Y参数列表运行插件X的特殊url。然后用此方法就可回调自己的组件来实现所需的额外功能。 

L199 – 定义displayNews()方法。告诉系统用一个参数 – 要读取得条目的url – 打开一个新进程（此例中为浏览器）。

现在，纯化论者又要说七说八了，你怎么能用遥控器而没有键盘来控制浏览器，他们绝对正确，这也是目前为止浏览器没有被引入XBMC的原因之一。
但是，希望这个例子能够帮助你洞悉一些实际工作中可能会用到的技术。

家庭作业
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
使用到目前为止，你已学到的知识，

 #. 将本例改造成一个程序脚本（Program script），而非组件，看看它运作时会有什么效果。
 #. 与打开浏览器不同，考虑用XBMC对话框显示新闻条目。有关屏幕抓取（scraping）和GUI相关资料的更多信息，查看:

    * http://wiki.xbmc.org/?title=HOW-TO_write_plugins_for_XBMC
    * http://wiki.xbmc.org/?title=HOW-TO_write_Python_Scripts_for_XBMC