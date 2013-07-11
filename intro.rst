简介
==================================================

:Author: Ashley Kitson
:Copyright: 2010, Ashley Kitson, UK and XBMC.org (http://xbmc.org)
:License: Creative Commons Attribution-NonCommercial-ShareAlike 3.0
|    License details
|    See Headlines: http://creativecommons.org/licenses/by-nc-sa/3.0/
|    See Legal Definitions: http://creativecommons.org/licenses/by-nc-nd/3.0/legalcode

本文目的
--------------------------------------------------
 * 为XBMC Python plugin插件脚本开发者提供指南
 * 为脚本开发者提供起步和快速提升的框架
 * 为脚本开发者提供权威信息源链接

提醒注意
--------------------------------------------------
 * 本文不打算教你如何用Python编程
 * 本文未涵盖XBMC核心系统的开发

印刷约定
--------------------------------------------------
需要在终端键入的命令以如下方式显示

    >>> sudo apt-get install xbmc

程序样例，也即需要键入的代码以如下方式显示::

    import urllib, urllib2, re, xbmcplugin, xbmcgui
    # TV DASH - by You 2008.
    def CATEGORIES():
        addDir('','',1,'')
        addDir('','',1,'')

前提条件
--------------------------------------------------

开发者具备下面条件中的前两个那可以了。如果你不是一个程序员，那也无妨，只不过需要用更长点的时间而已。
 * 具备应用开发知识，尤其是GUI环境下的应用开发知识
 * 具备Python脚本知识
 * 需要XBMC 10（Dharma）或以上安装版本。不需要开发用的源代码