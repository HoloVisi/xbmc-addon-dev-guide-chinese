附录 C – Code for working  example 3
==================================================
创建目录 *./xbmc/addons/script.rss-chippyash*

将以下内容粘贴到addon.xml::

    <?xml version="1.0" encoding="UTF-8"?>
        <!--
            Document	: addon.xml
            Package	    : A better RSS reader?
            Author	    : Ashley Kitson
            Copyright	: 2010, Ashley Kitson, UK
            License	    : Gnu General Public License - see LICENSE.TXT 
            Description : XBMC Addon settings for XBMC RSS Reader
        -->
        
        <addon id="script.rss-chippyash" version="0.0.1" name="A better RSS Reader?" provider-name="Ashley Kitson">
            <extension point="xbmc.python.pluginsource" library="rss-example.py">
                <provides>audio</provides>
                <requires>
                    <import addon="script.rss.editor" version="1.5.9"/>
                </requires>
            </extension>
            <extension point="xbmc.addon.metadata">
                <summary>A better RSS Reader?</summary>
                <description>Example dynamic listing program for the Addon Developer's Guide</description>
                <platform>all</platform>
            </extension>
        </addon>
    </xml>


将以下内容粘贴到rss-example.py::

    """
    Document	: rss-example.py
    Package	    : A better RSS Reader?
    Author	    : Ashley Kitson
    Copyright	: 2010, Ashley Kitson, UK
    License	    : Gnu General Public License - see LICENSE.TXT
    Description : Main program script for package
    """
    """
    This file is part of "A better RSS Reader?"
    
        "A better RSS Reader?" is free software: you can redistribute it and/or modify it under the terms of the GNU 
        General Public License as published by the Free Software Foundation, either version3 of the License,
        or(at your option) any later version.
        
        "A better RSS Reader?" is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; 
        without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
        See the GNU General Public License for more details.
        
        You should have received a copy of the GNU General Public License along with "A better RSS Reader?".
        If not, see <http://www.gnu.org/licenses/>.
    
    """
    #
    # 第一步 – 加载核心支持库，准备环境
    #
    import os
    import sys
    import xbmcplugin
    import xbmcaddon
    
    # rwparris rss编辑器组件名
    __RSSEDITOR_NAME__ = "script.rss.editor"
    
    # 准备RSS编辑器库路径
    _path = xbmcaddon.Addon(__RSSEDITOR_NAME__).getAddonInfo('path')
    sys.path.append(xbmc.translatePath(os.path.join(_path, 'resources', 'lib')))
    
    # 准备XML解析器语言引导
    __language__ = xbmcaddon.Addon(__RSSEDITOR_NAME__).getLocalizedString
    
    #（魔法）magic; 当前插件实例id – 整型
    _thisPlugin = int(sys.argv[1])
    
    # 组件id – 组件目录名
    _id = 'script.rss-chippyash'
    
    # 准备库路径
    sys.path.append(xbmc.translatePath(os.path.join(os.getcwd(), 'resources', 'lib')))
    
    # 为模块导入worker类
    import rss_chippyash as worker
    
    #
    # 第二步 – 初始化支持类
    #
    creator = worker.creator(_id)
    sender = worker.sender(_thisPlugin, _id)
    
    #
    # 第三步 – 运行程序
    #
    
    # 查看是否有URL参数
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
        # 显示新条目
        sender.displayNews(url)


将以下内容粘贴到resources/lib/rss_chippyash.py::

    """
    Document	: rss-example.py
    Package	    : A better RSS Reader?
    Author	    : Ashley Kitson
    Copyright	: 2010, Ashley Kitson, UK
    License	    : Gnu General Public License - see LICENSE.TXT
    Description : Main program script for package
    """
    """
    This file is part of "A better RSS Reader?"
    
        "A better RSS Reader?" is free software: you can redistribute it and/or modify it under the terms of the GNU 
        General Public License as published by the Free Software Foundation, either version3 of the License,
        or(at your option) any later version.
        
        "A better RSS Reader?" is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; 
        without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
        See the GNU General Public License for more details.
        
        You should have received a copy of the GNU General Public License along with "A better RSS Reader?".
        If not, see <http://www.gnu.org/licenses/>.
    
    """
    # make xbmc and system modules available
    import subprocess
    import sys
    import xbmcplugin
    import xbmcaddon
    import xbmcgui
    from xmlParser import XMLParser
    import urllib2
    from xml.dom import minidom

    # define global constants for settings xml tags
    __GPOPATH_TAG__ = 'gpoPath'
    __GPOUPDT_TAG__ = 'gpoUpdate'

    # Addon Title for dialogs
    __TITLE__ = 'A Better RSS Reader?'

    # Browser command
    __BROWSER__ = 'google-chrome'

    class rssFeed:
        """
        A very simple RSS feed class
        """
        feedLinks = []
        feedTitles = []
        feedDescs = []
        feedCount = 0

        def __init__(self, feedUrl):
            """
            gather the news items in an RSS feed
            """
            fileRequest = urllib2.Request(feedUrl)
            fileOpener = urllib2.build_opener()
            fileFeed = fileOpener.open(fileRequest).read()
            fileXml = minidom.parseString(fileFeed)
            itemNode = fileXml.getElementsByTagName("item")
            for item in itemNode:
                self.feedTitles.append(item.childNodes[1].firstChild.data)
                self.feedDescs.append(item.childNodes[2].firstChild.data)
                self.feedLinks.append(item.childNodes[3].firstChild.data)
                self.feedCount += 1

    class creator:
        """
        负责创建要显示的条目列表
        """
        #
        # 私有方法
        #

        # 当前插件实例标识符
        # _pluginId = 0
        # pluginname
        _pluginName = ''
        # this addon class
        _thisaddon = None
        # rss feed list
        _feedList = []
        #rss feed content
        feeds = []

        def __init__(self, pluginName):
            """
            constructor
            @parm int pluginId - Current instance of plugin identifier
            @param string pluginName - Name of plugin calling us
            """
            # self._pluginId = pluginId
            self._pluginName = pluginName
            self._thisaddon = xbmcaddon.Addon(pluginName)
            # get current feeds
            feeds = XMLParser().getCurrentRssFeeds()
            for setNum in sorted(feeds.keys()):
                for feed in feeds[setNum]['feedslist']:
                    self._feedList.append(feed['url'])
                    #get the feed contents
                    self.feeds.append(rssFeed(feed['url']))


        def _createListAll(self):
            """
            创建所有内容的动态列表

            @param list dirContent - list of __PLAYLIST__ files in gpodder directory
            @param string dir - gpodder directory location
            @access private
            @return list
            """

            #create listing
            listing = []
            for feed in self.feeds:
                c = range(0, feed.feedCount - 1)
                for x in c:
                    listing.append([feed.feedLinks[x],feed.feedTitles[x], ''])

            return listing

        #
        # 公有API
        #
        def get(self):
            """
            Refresh and retrieve the current list for display
            @access public
            @returns list
            @usage	c=example2.creator()
            list=c.get()
            """
            return self._createListAll()

        def getparams(self):
            """
            Pickup parameters sent in via commandline
            @return dict list of parameters
            @thanks Team XBM -I lifted this straight out of the shoutcast addon
            """
            param = []
            paramstring = sys.argv[2]
            if len(paramstring)>= 2:
                params = sys.argv[2]
                cleanedparams = params.replace('?', '')
                if(params[len(params) - 1] == '/'):
                    params = params[0 : len(params) - 2]
                    pairsofparams = cleanedparams.split('&')
                    param = {}
                    for i in range(len(pairsofparams)):
                        splitparams = {}
                        splitparams = pairsofparams[i].split('=')
                        if(len(splitparams)) == 2:
                            param[splitparams[0]] = splitparams[1]
            return param


    class sender:
        """
        Responsible for sending output to XBMC
        """
        # current instance of plugin identifier
        _pluginId = 0
        # pluginname
        _pluginName = ''

        def __init__(self, pluginId, pluginName):
            """
            constructor
            @parm int pluginId - current instance of plugin identifier
            """

            self._pluginId = pluginId
            self._pluginName = pluginName
            pass

        def send(self, listing):
            """
            Send output to XBMC
            @param list listing - the list of items to display
            @return void
            """
            # create listing items
            # item[0] = list label
            # item[1] = item uri
            # item[2] = image uri
            for item in listing:
                listItem=xbmcgui.ListItem(item[0])
                url="plugin://"+self._pluginName+"?url=" + item[1]
                xbmcplugin.addDirectoryItem(self._pluginId, url, listItem)

        def displayNews(self, url):
            """
            Display the news item in a browser
            @param string url - Url to display
            """
            subprocess.Popen([__BROWSER__, url])

