附录 B – Code for working  example 2
==================================================
在 *.xbmc/addons* 目录下创建以下目录和文件

|   plugin.audio.addon-dev-ex2
|       addon.xml
|       addondev2.py
|       /resources
|            settings.xml
|            /lib
|                gpodder.py

将以下内容粘贴到addon.xml::

    <?xml version="1.0" encoding="UTF-8"?>
        <addon
            id="plugin.audio.addon-dev-ex2" version="0.0.1"
            name="XBMC Addon Developers Guide – Example 1" provider-name="AshleyKitson"
        >
            <requires>
                <import addon="xbmc.gui"/>
                <import addon="xbmc.plugin"/>
            </requires>
            
            <extension point="xbmc.python.pluginsource" library="addondev2.py">
                <provides>audiovideo</provides>
            </extension>
            <extension point="xbmc.addon.metadata">
                <summary>XBMC Addon Developer sGuide</summary>
                <summary lang="fr">XBMC Developers Guide Addon</summary>
                <description>Demonstrates creating dynamic listings for an XBMC plugin type addon</description>
                <description lang="fr">Montre la création d'annonces dynamiques pour un addon type de plugin XBMC</description>
                <platform>all</platform>
            </extension>
        </addon>
    </xml>

将以下内容粘贴到addondev2.py::

    """
    XBMC Addon Developer's Guide
    示例 2 - 继续
            演示动态列表的创建
            演示使用自己的模块和类
    注意 本例使用函数 - 也可用“类”来实现
    
    作者：Ashley Kitson
    """
    #
    # 步骤 1 - 加载xbmc核心支持库，设置环境
    #
    import sys
    import xbmcplugin
    
    
    # 组件 id - 组件目录名
    _id = 'plugin.audio.addon-dev-ex2'
    # 资源目录
    _resdir = "special://home/addons/" + _id + "/resources"
    # 将自己的库添加到python搜索路径
    sys.path.append(_resdir + "/lib/")
    
    # 从我们的模块中导入 worker 类
    import gpodder as worker
    
    
    # magic; id of this plugin's instance - cast to integer
    _thisPlugin = int(sys.argv[1])
    
    #
    # 步骤 2 - 初始化支持类
    #
    creator = worker.creator(_thisPlugin, _id)
    sender = worker.sender(_thisPlugin)
    
    #
    # 步骤 3 - 运行程序
    #
    sender.send(creator.get())
    xbmcplugin.endOfDirectory(_thisPlugin)


将以下内容粘贴到resources/settings.xml::

    <?xml version="1.0" encoding="UTF-8"?>
    <!--
        Document    : settings.xml
        Created on  : September 9, 2010, 1:45PM
        Author  	: Ashley Kitson
        Description : Settings for gpodder example (XBMC Addons Developer Guide example 2)
    -->
    <settings>
        <setting id="gpoPath" type="text" label="PathtoGpodderdownloads" default=""/>
    </settings>

现在将你的 *download_dir* 值加到gpoPath默认设置，然后保存文件。

将以下内容粘贴到resources/lib/gpodder.py::

    """
    XBMC Addon Developer's Guide

    示例 2 - 演示从Gpodder目录创建动态列表
    该模块提供创建和显示内容的类

    作者：Ashley Kitson
    """

    # 使xbmc和系统模块可用
    import xbmc
    import xbmcplugin
    import xbmcgui
    import dircache
    import fnmatch

    # 为设置xml标记定义全局常量
    __GPOPATH_TAG__ = 'gpoPath'
    # 定义类
    class creator:
        """
        负责创建要显示的条目列表
        """
        #
        # 私有方法
        #

        # 当前插件实例标识符
        _pluginId = 0
        # 插件名
        _pluginName = ''

        def __init__(self, pluginId, pluginName):
            """
            构造器
            @param int pluginId - 当前插件实例标识符
            @param string pluginName - 调用插件的名字
            """
            self._pluginId = pluginId
            self._pluginName = pluginName

        def _createList(self):
            """
            创建动态列表
            @access private
            @returns list
            """
            # 取得用户gpodder目录设置
            dir = xbmcplugin.getSetting(self._pluginId, __GPOPATH_TAG__)
            # 取得gpodder目录内容
            dirContent = dircache.listdir(dir)
            # 解析所有.m3u文件内容
            dirContent = fnmatch.filter(dirContent,'*.m3u')

            # 创建列表
            listing = []
            for file in dirContent:
               uri = xbmc.translatePath(dir + '/' + file)
               label = file.replace('.m3u', '')
               listing.append([label,uri])

            return listing
        #
        # 公有API
        #
        def get(self):
            """
            刷新和检索当前列表以显示
            @access public
            @return list
            @usage	c=example2.creator()
            list=c.get()
            """
            return self._createList()


    class sender:
        """
        负责发送输出至XBMC
        """
        # 当前插件实例标识符
        _pluginId=0


        def __init__(self,pluginId):
            """
            构造器
            @param int pluginId - 插件当前实例标识符
            """
            self._pluginId = pluginId

        def send(self,listing):
            """
            Send output to XBMC
            @param list listing - 要显示的条目的列表
            @return void
            """
            # 创建列表条目
            # item[0] = listlabel
            # item[1] = item uri
            for item in listing:
                listItem = xbmcgui.ListItem(item[0])
                xbmcplugin.addDirectoryItem(self._pluginId,item[1], listItem)

