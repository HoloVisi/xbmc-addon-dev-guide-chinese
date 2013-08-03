脚本基础
==================================================

附加组件的基本构成
--------------------------------------------------
从XBMC V10（Dharma）以后，所有组件脚本包都有相同的结构，每个附加组件（现在它们被分别单独称为插件、脚本等）都有自己的目录，

该目录位于.xbmc/addons目录下。

目录名
--------------------------------------------------
目录名称应遵循以下惯例 [#]_ ：

    <addon_ype>[.mediaType>].<yourPluginName>

名称部分大小写敏感且应为小写，每个名字部分以符号“.”分隔。如果愿意，可以在插件名（<yourPluginName>）部分用更多的点或中划线“-”分隔。

其它非字母数字字符不允许使用。

AddonType是以下列表 [#]_ 之一

    +---------------------+------------------------------------------------------------------------+
    | **AddonType**       | **Description**                                                        |
    +=====================+========================================================================+
    | Repository          | A repository definition file that allows users to add new              |
    |                     | repositories to the XBMC addon manager.                                |
    |                     | Media type is not required.                                            |
    +---------------------+------------------------------------------------------------------------+
    | Plugin              | A plugin script or module that adds to the functionality of XBMC.      |
    |                     | Plugins appear under the relevant media section of the main home menu. |
    +---------------------+------------------------------------------------------------------------+
    | Script              | A runnable program file that will appear in the Program section of the |
    |                     | main home menu.                                                        |
    +---------------------+------------------------------------------------------------------------+
    | Skin                | An XBMC skin definition and its supporting script files.               |
    |                     | Media type is not required.                                            |
    +---------------------+------------------------------------------------------------------------+

并非在所有情况下都需要mediaType，下表描述了可用附加组件可用的mediaType。如果愿意，组件可提供多个mediaType，这样将会在多个部分中显示。
所需绝大多数情况下一个媒体类型就足够了，最好有多个组件，每个提供单一媒体类型，而不要用一个组件干完所有活。

    +--------------------+---------------------+--------------------------------------------------------+
    | **Addon**          | **Media Type Name** | **Description**                                        |
    +====================+=====================+========================================================+
    |                    | Audio               | A music addon that will appear in the main menu        |
    |                    |                     | *music* section                                        |
    |  Plugin            +---------------------+--------------------------------------------------------+
    |  Script            | Video               | A video addon that will appear in the main menu        |
    |                    |                     | *video* section                                        |
    |                    +---------------------+--------------------------------------------------------+
    |                    | Picture             | A picture addon that will appear in the main menu      |
    |                    |                     | *picture* section                                      |
    |                    +---------------------+--------------------------------------------------------+
    |                    | Weather             | A weather addon that will appear in the main menu      |
    |                    |                     | *weather* section                                      |
    +--------------------+---------------------+--------------------------------------------------------+
    |                    | Module              | A script plugin that will not appear under a category  |
    |  Script            +---------------------+ or within the Addons manager,                          |
    |                    | Service             | but provides support for other addons                  |
    +--------------------+---------------------+--------------------------------------------------------+

插件名字取决于你，但要留意别人在你之前是否使用过。因此，例如，如果你正创建一个把Gpodder软件和XBMC集成起来用于音频播放的插件，
可将你的目录命名为：

    plugin. Audio.gpodder-xbmc [#]_

如果你正创建一个从MyGreatTv.com的展示电视剧的屏幕刮削器（screen scraper），它可为：

    plugin.video.my-great-tv-com

ping你正在twitter上的朋友，告诉他们你现在在家的脚本可被称为：

    script.ping-twits-i-am-home

选定名字，在.xbmc/addons目录下创建该目录。

目录结构
--------------------------------------------------
你的目录中包含操作附加组件所需的所有资源。该目录必须为只读，且不用于存储任何对话或瞬时数据，有其它机制来干这件事（更多介绍参见后述）。
目录中必须包含一个名为“addon xml”的文件。
目录可包含以下文件（可选）：

 * icon.png
 * fanart.jpg
 * changelog.txt

运行组件也许还需要其它文件，最值得注意的是组件的首要或启动脚本应是一段python代码。比较好的实践是把组件的各种资源和支持代码模块以如下方式组织：

|    addon.xml
|    icon.png
|    fanart.jpg
|    changelog.txt
|    addon.py（一般命名为default.py [#]_ ）
|    /resources
|        settings.xml
|        /language
|        /lib
|        /data
|        /media

也就是说，任何不应在组件根目录下的东西被看作是资源，应该放在/resources目录及其子目录下。

文件内容
--------------------------------------------------
icon.png
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
除非组件永远不会被最终用户看到，否则应提供图标，它会在XBMC内许多地方显示。

 * 应该以图形方式传达组件是干什么的信息。
 * 应为256×256PNG
 * 在其之上，不应有任何阴影、光泽或其它覆盖层——XBMC的皮肤系统将会处理这些。
 * 许多情况下最好是在一个素净背景（不透明）之上的logo。例如对从在线服务获取媒体内容的组件，使用该服务的logo[只要你是被允许这么做]

fanart.jpg
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
这是“最好有”的，它可使XBMC图形更丰富，fanart.png应为16:9图像，一些简单原则：

 * 它被用作背景，因此应简单，并且无特别原因，其上尽量不要有文字。
 * 1280×720JPG图形。肯定不能大于1920×1080。
 * 至于文件大小应保持尽量小，记住将有成千上万的人下载它:-)

changelog.txt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
一个包括每个发行版本所做改动的文本文件，它将显示在XBMC组件安装和更新系统，建议格式为版本倒序。对每个版本中大的改动做简单说明（新功能、大补丁），
以作者的观点，太多组件忽略了这个信息，使得很多用户很难判断他们之前遇到的某个问题是否得到解决。

resources/settings.xml
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
一个包含组件当前配置的XML文件，如果组件有要用户配置的条目，把它们放在这里。当用户在XBMC内点击组件设置时，该文件决定了用户能看到什么，
使用这项功能不必编写代码。
设置文件的格式如下例所示，相当直接::

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
        <settings>
            <category label="General">
                <setting id="username" type="text" label="2000" default=""/>

                <setting id="password" type="text" option="hidden" label="2001" enable="!eq(-1,)" default=""/>
                <setting type="sep"/>

                <setting id="debug" type="bool" label="2002" default="false"/>
            </category>
        </settings>
    </xml>

需要至少一个category元素。
category（类别）和settings（设置）中的lable（标签）属性可以是一段文字，或者语言文件中的字符串id。

设置类型和附加属性可为下表之一：

    +-----------+--------------------+--------------------------------+----------------------------------------+
    | **Type**  | **Description**    | **Value attribute(value=””)**  | **Notes**                              |
    +===========+====================+================================+========================================+
    |text       | Creates keyboard   |                                |Can have 'option' attribute set         |
    |           | input              |                                |“true” or “false”, will hide the        |
    |           |                    |                                |text value if false.                    |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |file       | Creates a file     |                                |                                        |
    |           | selector           |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |folder     | Creates a folder   |                                |                                        |
    |           | selector           |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |enum       | Creates a select   | Pipe separated list of values  |Using xbmcplugin.getSetting(pluginId,'  |
    |           | box                | e.g. "1|3|5|10|All"            |mytagname') on an enum will             |
    |           |                    |                                |return the index into the list, not     |
    |           |                    |                                |the value itself. Also be aware         |
    |           |                    |                                |that 1 digit indexes may be             |
    |           |                    |                                |returned as '01', '02' etc              |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |labelenum  |                    |                                |Same as enum except the value           |
    |           |                    |                                |is returned by getSetting()             |
    |           |                    |                                |instead of the index                    |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |ipaddress  | Creates IP dialog  |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |integer    | Creates numeric    |                                |                                        |
    |           | dialog             |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |bool       | Creates radio      |                                |Set default or value attribute to       |
    |           | button             |                                |to “true” or “false” (note case).       |
    |           |                    |                                |                                        |
    |           |                    |                                |Using                                   |
    |           |                    |                                |xbmcplugin.getSetting(pluginId,'        |
    |           |                    |                                |mytagname') on a bool will              |
    |           |                    |                                |return 'true' or 'false'. Use           |
    |           |                    |                                |appropriate conversion to turn          |
    |           |                    |                                |into real boolean.                      |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |sep        | Creates a separator|                                |                                        |
    |           | line in the dialog |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |music      |                    |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |video      |                    |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |pictures   |                    |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |programs   |                    |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |local      |                    |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |fileenum   |Create a file       |Set value to root of            |Optional 'mask' attribute ?             |
    |           |selector based on   |directory you want to use       |                                        |
    |           |path set in         |for selection                   |                                        |
    |           |value attribute     |                                |                                        |
    +-----------+--------------------+--------------------------------+----------------------------------------+
    |action     |Executes a script   |                                |Set the 'action' attribute to the       |
    |           |when selected       |                                |name of your function to                |
    |           |                    |                                |execute. e.g.                           |
    |           |                    |                                |action="RunPlugin(plug                  |
    |           |                    |                                |in://video/Apple Movie                  |
    |           |                    |                                |Trailers Plugin/?                       |
    |           |                    |                                |update=newest)"                         |
    |           |                    |                                |                                        |
    |           |                    |                                |Optionally set the 'option'             |
    |           |                    |                                |attribute to “close” if you want to     |
    |           |                    |                                |close the settings dialog when          |
    |           |                    |                                |you click the setting.                  |
    +-----------+--------------------+--------------------------------+----------------------------------------+

设置可以有附加属性
 * source=""
    - “video”、“music”、“pictures”、“programs”、“file”，或者为空。如果source为空，共享有效，就使用共享类型；共享无效，就使用本地和网盘

 * visible=""
    - “true”、“false”。决定设置是否在设置对话框中出现（缺省为“true”）

 * enable=""
    - 确定该设置根据另一个设置的值是否显示，有三个有效比较符
        + eq() 等于
        + gt() 大于
        + lt() 小于

    - 用加号（+）表示“与”，用“！”表示“非”。每个比较符有两个操作符
        + 用于比较的设置的相对位置
        + 要比较的值

    - 因此，如果在设置文件中设置项目顺序如
        + 第1项设置
        + 第2项设置
        + 第3项设置

    - 对第3项设置可以加上
        + enable=”gt(-2,3) + lt(-2,10)” [第1项设置大于3，小于10]

resources/language
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
把语言翻译的东西放在这个目录下，有些XBMC部件认为语言字符串在这个目录里，所以如果用到它们，它们必须在这儿。
对每一种你想要支持的语言创建一个要包含的语言命名的子目录。核心XBMC系统支持的语言列表：

    +-----------------------------------------------+-----------------------------------------------+
    | **Language (and language folder name)**       | **Lang code used in addons.xml**              |
    +===============================================+===============================================+
    | Bulgarian                                     | bg                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Catalan                                       | ca                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Chinese (Simple)                              | zh                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Chinese (Traditional)                         | zh                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Croatian                                      | hr                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Czech                                         | cs                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Danish                                        | da                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Dutch                                         | nl                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | English                                       | en                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | English (US)                                  | en                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Esperanto                                     | eo                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Finnish                                       | fi                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | French                                        | fr                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | German                                        | de                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Greek                                         | el                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Hebrew                                        | iw                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Hungarian                                     | hu                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Icelandic                                     | is                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Indonesian                                    | in                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Italian                                       | it                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Japanese                                      | ja                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Korean                                        | ko                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Maltese                                       | mt                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Norwegian                                     | no                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Polish                                        | pl                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Portuguese                                    | pt                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Portuguese (Brazil)                           | bg                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Romanian                                      | ro                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Russian                                       | ru                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Serbian                                       | sr                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Serbian (Cyrillic)                            | sh                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Slovak                                        | sk                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Slovenian                                     | sl                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Spanish                                       | es                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Spanish (Mexico)                              | es                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Swedish                                       | sv                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Turkish                                       | tr                                            |
    +-----------------------------------------------+-----------------------------------------------+
    | Ukrainian                                     | uk                                            |
    +-----------------------------------------------+-----------------------------------------------+

备注，在多种语言共享一个语言代码的地方，XBMC根据用户在偏好设置中设定的语言和地区来决定语言变量。强烈建议插件有支持多语言能力，
这可使得组件在最大范围的使用。XBMC缺省语言为英语（注意不是美式英语, English(US)），因此，如果要提高额外的语言支持，
在你所支持的其它语言之外，至少要提供英语。

例如，要支持English、English(US)和French。在目录下，应创建3个子目录:

    /resources/language/English

    /resources/language/English(US)

    /resources/language/French

在每个目录，创建“strings.xml”。文件格式为::

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
        <strings>
            <string id="n">Mytext</string>
        </strings>
    </xml>

id是一个数字，可以用自己的数字系列，但组件的惯例是用30000-30999之间的数字，因此我们创建的三个语言为

English/strings.xml
::

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
        <strings>
            <!--组件名-->
            <string id="30000">MyAddon</string>
            <!--一些其它字符串-->
            <string id="30100">Hello</string>
            <!--设置-->
            <string id="30200">Username</string>
            <string id="30210">Password</string>
            <string id="30220">Debugging(restart script to apply)</string>
        </strings>
    </xml>

English(US)/strings.xml
::

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
        <strings>
            <!--组件名-->
            <string id="30000">MyAddon</string>
            <!--some other strings-->
            <string id="30100">Hi</string>
            <!--Settings-->
            <string id="30200">Username</string>
            <string id="30210">Password</string>
            <string id="30220">Debugging(restart script to apply)</string>
        </strings>
    </xml>

French/strings.xml
::

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
        <strings>
        <!--Addon Name-->
        <string id="30000">MonAddon</string>
        <!--some other strings-->
        <string id="30100">Bonjour</string>
        <!--Settings-->
        <string id="30200">Pseudo</string>
        <string id="30210">Motdepasse</string>

        <string id="30220">Débogage(redémarrer le script à appliquer)</string>
        </strings>
    </xml>

Google和Yahoode Babelfish翻译是基础翻译的两个好工具，特别是对短字符串的翻译。但还是应该尽可能让使用这种母语的人仔细核对你的翻译，
因为这两个工具可能不能恰当的应用惯用语法。最糟糕的可能是你的翻译给使用母语的人带来理解上的困扰。

实例部分详细说明了脚本中如何用翻译。

resources/lib
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
将任何模块定义或第三方软件库放在该目录下

resources/data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
你的应用所需的其它静态数据结构都保存在此。例如，XLT/XSD文件或包含查询表的XML文件等等。

resources/media
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
该目录下保存静态媒体文件（图片、语音、视频等）

记住，以上只是给你的组件的一个建议大纲，如果你需要以更多或更少的目录来组织工作，尽可去改动它，唯一绝对必需的文件是

addon.xml
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
addon.xml告诉XBMC:
 * 组件提供什么
 * 组件工作依赖什么
 * 启动时运行哪个脚本

文件的基本格式为::

    <?xml version="1.0" encoding="UTF-8"?>
        <addon id="" version="" name="" provider-name="">
            <requires></requires>
            <extension point="seenotes"></extension>
            <extension point="xbmc.addon.metadata">
                <summary></summary>
                <description></description>
                <platform></platform>
            </extension>
        </addon>
    </xml>

<addon>起始标记是必需的

 * id 是用于该组件的唯一标识符，它必须是唯一的，且只能是小写字母、句点、下划线、中划线和数字。该标识符也作为包含组件的文件夹的名字。参见前面关于目录命名的说明
 * version XBMC用来判定更新是否有效，应使用类似1.0.3这样的格式
 * name 组件出现在用户界面上的名字，只能为英文，且不可翻译
 * provider provider-name属性用于作者域，它可以是一个团队也可以是单个人

<requires>标记是可选的，但如存在，则以下述格式::

    <requires>
        <import addon="" version=""/>
    </requires>

也就是，若干<import>标记组成<requires>标记，每个<import>标记有两个属性：
 * addon 组件依赖的库的名字
 * version 所需库的最低版本

例子::

    <requires>
        <import addon="xbmc.gui" version="2.11"/>
        <import addon="script.module.simplejson" version="2.0.10"/>
    </requires>

在这个例子中，组件需要XBMC自带的GUI包和一个称为“script.module.simplesjon”，在另一个组件包中交付的第三方库（仍在XBMC控制内）

注意，如果模块依赖第三方模块，它们要在安装你的模块之前被安装，假设第三方模块在现有存储库中可得，XBMC会在用户安装你的模块时自动安装它们，
XBMC域之外的库必须由你的代码来加载，而不能成为<requires>语句的部分，因为XBMC不知道如何与它们打交道。

<extension>元素是必需的，你至少需要其中两个：第一个总是以point属性“xbmc.addon.metadata”来命名，所有组件都必须提供这个，
它告诉组件管理器有关你的组件用来显示给用户或管理组件本身的重要信息。第二个<extension>通过point属性告诉XBMC在其层次结构中的什么地方定位该组件，
至少需要这两个扩展点，更多扩展点也是可能的，譬如说，组件扩展音乐和视频。

<extension point="xbmc.addon.metadata">标记有以下标记：

 * <summary [lang=""]> 一个或多个summary元素提供组件干什么的简短描述，应该就一句话。它也许会被翻译为多种语言，其中每种都包含一个lang=“ch”属性，没有lang属性的标为英语
 * <description [lang=""]> 组件做什么的较为详细的描述，再说一遍，这些也能被翻译
 * <disclaimer [lang=""]> 一个或多个disclaimer权鉴指明用户使用该组件应知晓的一些事（如果有），如果不想要，则无需此标签，但是有些东西在特定国家需要设置，或只能在那里使用，那你要在此处声明
 * <platform>标记，指定组件运行在哪个平台上（操作系统、硬件），许多组件都可在所有平台上运行，因此“all”和“linux”、“osx”、“windx”、“wingl”一样也是选项。如果platform标签缺失，那么假定组件可运行于所有平台。

要扩展XBMC的哪个部分取决于第二个<extension>中的属性是什么。如果需要，组件也许指定附加扩展点。

插件组件开发者将要用到的最常见的扩展点是“xbmc.python.pluginsource”。下面addon.xml例子展示了Gpodder插件组件的准备工作。
::

    <?xml version="1.0" encoding="UTF-8"?>
        <addon
            id="plugin.audio.gpodder-xbmc" version="0.0.1"
            name="GpodderXBMCIntegrator" provider-name="AshleyKitson"
        >
        <requires>
            <import addon="xbmc.gui" version="2.11"/>
            <import addon="gpodder.api"/>
        </requires>

        <extension point="xbmc.python.pluginsource" library="gpodder-xbmc.py">
            <provides>audiovideo</provides>
        </extension>
        <extension point="xbmc.addon.metadata">
            <summary>XBMC Integration to Gpodder</summary>
            <summary lang="fr">Intégration de XBMC Gpodder</summary>

            <description>Provides the ability to control Gpodder podcatching
            application from within XBMC including automating podcast downloads,
            managing subscriptions and episodes
            </description>

            <description lang="fr">Fournit la capacité de contrôler gpodder baladodiffusion application
            àpartir de XBMC,y compris l'automatisation téléchargements podcast,
            la gestion des abonnements et desépisodes
            </description>
            <platform>linux</platform>
        </extension>
        </addon>
    </xml>

<extension point="xbmc.python.pluginsource">标记有额外属性：
 * Library 插件激活时将运行的python脚本（启动脚本）名字，该文件必须存在于组件根目录下。

Extension有额外子元素：
 * <provides>由空格分隔的列表，其内容为image、video、audio、executable。它决定你的组件在XBMC系统的什么区域（或上下文）可见。
    - image=>图片
    - video=>视频
    - audio=>音乐
    - executable=>程序
    - <blank>=>当前不可见

现在，XBMC API库没有提供内置方法来判断当前运行环境（上下文），参见附录D，有一个可以包含在你的脚本中的判断上下文的hack。

关于扩展类型（extension type）的更多信息，参见：

http://wiki.xbmc.org/index.php?title=Plugin_Sources

如何使组件对XBMC可见
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
目前XBMC只在启动时扫描组件，将来这个可能会改变，现在，假设你已经把文件放在正确的地方了（也就是此前描述的，xbmc/addons目录内）。
检查一下在期望的地方能够看到你的组件，如果没有，那就该调试了，参见以下调试部分的内容。

将脚本打包通过XBMC组件存储库发布
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
一旦组件完工，就准备将其发布出去，或者至少发布到XBMC的内置存储库，XBMC要求你优先将组件入驻（host）在Git或SVN兼容的存储库中，
并将其放在XBMC可以访问的公共服务器上，更多细节，参见：

http://wiki.xbmc.org/index.php?title=Official_Addons_Repository

记住让组件多少尽些责任，首要的就是避免程序出现错误，对于屏幕抓取作者而言，网站做些诸如修改页面布局（layout）、
css或其它html元素这样的事就会遇到问题。如果随着时间推移，你想强化组件，这事由你。当是，我要建议，如果某时你不再想维护组件了，
请找到继任者，或将其从发布中移去。
另外替代的方案是，你也可创建自己的面向公众的存储库，然后将存储库细节提交给XBMC即可，
（用<extension point="xbmc.addon.repository">组件配置），第三个替代方案是将组件打包成zip文件在你自己的网站上发布，
因为XBMC能够从zip文件安装组件。

.. rubric:: 脚注

.. [#] 这种约定不是强制性的，但反映了最佳实践。更多信息，参见addon.xml
.. [#] reST对中文表格似乎还不太好，因此表格部分内容都未翻译（译者）
.. [#] Gpodder例子将在本文档中使用
.. [#] 译者注