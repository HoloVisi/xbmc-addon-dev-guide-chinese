附录 D – 代码片段
==================================================
获取当前环境（Get Current Context）
::

    class context:
        """
        Hack to get current context that addon is running in

        @usage	context=context().getContext()
        @author chippyash
        @thanks amet - xbmc.org
        @link http://wiki.xbmc.org/index.php?title=Window_IDs
        """
        # Setup window Ids for the various contexts
        _ctxt_audio = (10005, 10500, 10501, 10502)
        _ctxt_video = (10006, 10024, 10025, 10028)
        _ctxt_image = (10002)
        _ctxt_executable = (10001, 10020)
        #current window id
        _currId = 0

        def __init__(self):
            self._currId = xbmcgui.getCurrentWindowId()

        def getContext(self):
            """
            Returns the current system context
            @return string
            ('audio', 'video', 'image', 'executable', 'unknown')
            """
            if self._currId in self._ctxt_audio:
                return 'audio'
            elif self._currId in self._ctxt_video:
                return 'video'
            elif self._currId in self._ctxt_image:
                return 'image'
            elif self._currId in self._ctxt_executable:
                return 'executable'
            else:
                return 'unknown'

This allows you to put multiple values in the <extension><provides> tag in addons.xml schema and
determine at run time what section of XBMC you are in so that your addon can provide different responses dependent on
the context. At some point the XBMC Python API may incorporate this into it's provision.
