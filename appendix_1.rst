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
    Example1 - The basic plugin structure
               Demonstrates creating a static list

    NB This is done using functions - you could use classes

    Author: Ashley Kitson
    """

    # Step 1 - load in xbmc core support and setup the environment
    import xbmcplugin
    import xbmcgui
    import sys

    # magic; id of this plugin - cast to integer
    thisPlugin = int(sys.argv[1])

    # Step 2 - create the support functions(or classes)

    def createListing():
        """
        Creates a listing that XBMC can display as a directory listing

        @returnlist
        """
        listing = []
        listing.append('Thefirstitem')
        listing.append('Theseconditem')
        listing.append('Thethirditem')
        listing.append('Thefourthitem')

        return listing


    def sendToXbmc(listing):
        """
        Sends a listing to XBMC for display as a directory listing
        Plugins always result in a listing

        @param list listing
        @return void
        """
        # access global plugin id
        global thisPlugin

        #send each item to xbmc
        for item in listing:
        listItem = xbmcgui.ListItem(item)
        xbmcplugin.addDirectoryItem(thisPlugin, '', listItem)

        # tell xbmc we have finished creating the directory listing
        xbmcplugin.endOfDirectory(thisPlugin)

    #Step 3 - run the programs
    endToXbmc(createListing())

重启XBMC，然后在 *音乐* 部分选择运行该组件。