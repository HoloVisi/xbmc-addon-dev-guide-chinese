附录 A – Code for working  example 1
==================================================

在 *.xbmc/addons* 目录下创建以下目录和文件

|   plugin.audio.addon-dev-ex1
|       addon.xml
|        addondev1.py

将以下内容粘贴到addon.xml::

    <?xml version="1.0" encoding="UTF-8"?>
        <addon
            id="plugin.audio.addon-dev-ex1" version="0.0.1"
            name="XBMCAddonDevelopersGuide–Example1" provider-name="Ashley Kitson"
        >
            <requires>
                <import addon="xbmc.gui"/>
                <import addon="xbmc.plugin"/>
            </requires>

            <extension point="xbmc.python.pluginsource" library="addondev1.py">
                <provides>audio</provides>
            </extension>
            <extension point="xbmc.addon.metadata">
                <summary>XBMC Addon Developers Guide</summary>
                <summary lang="fr">XBMC Developers Guide Addon</summary>

                <description>Demonstrates basic code loop for anXBMC plugin type addon</description>

                <description lang="fr">Démontre boucle code de base pour un addon type de plugin XBMC</description>
                <platform>all</platform>
            </extension>
        </addon>
    </xml>

将以下内容粘贴到addondev1.py::

    """
    XBMC Addon Developer's Guide
    示例1 - 基本插件结构
            演示静态列表的创建过程

    注意 本例使用函数 - 也可用“类”来实现

    作者：Ashley Kitson
    """

    # 步骤 1 - 加载xbmc核心支持库，设置环境
    import xbmcplugin
    import xbmcgui
    import sys

    # 魔法；该插件id - 强制转为整型
    thisPlugin = int(sys.argv[1])

    # 步骤 2 - 创建支持函数（或 类）

    def createListing():
        """
        创建XBMC作为目录显示的列表

        @return list
        """
        listing = []
        listing.append('Thefirstitem')
        listing.append('Theseconditem')
        listing.append('Thethirditem')
        listing.append('Thefourthitem')

        return listing


    def sendToXbmc(listing):
        """
        发送列表给XBMC作为目录列表显示
        插件始终产生列表

        @param list listing
        @return void
        """
        # 存取全局变量 - 插件id
        global thisPlugin

        # 将每个条目发送给xbmc
        for item in listing:
            listItem = xbmcgui.ListItem(item)
            xbmcplugin.addDirectoryItem(thisPlugin, '', listItem)

        # 告诉XBMC已经完成目录列表的创建
        xbmcplugin.endOfDirectory(thisPlugin)

    # 步骤 3 - 运行程序
    endToXbmc(createListing())

重启XBMC，然后在 *音乐* 部分选择运行该组件。