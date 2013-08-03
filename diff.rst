其它XBMC扩展所需的不同之处
==================================================

Scripts（脚本）
--------------------------------------------------
<extension point="xbmc.python.script">组件出现在XBMC主界面 *程序* 菜单部分，它们需要处理所有用户交互，
典型地构造用于在窗口内运行或简单地在终端向用户显示一些状态。它们能像其它组件一样有用户在高亮显示程序名时可通过上下文菜单存取的设置。

进一步的最新信息参见

http://wiki.xbmc.org/index.php?title=Script_Sources


Repository（存储库）
--------------------------------------------------
存储库组件只需两个文件，它允许XBMC把你的SVN或GIT存储库包括进用户可下载有效存储列表中，
有关<extension point="xbmc.addon.repository">格式的最新详细信息，参见：

http://wiki.xbmc.org/index.php?title=Addon_Repositories#Repository_Addon