写完文章发现，原来的js脚本里已经提供了完全的解决方案，但是它没写使用方法，只是上传了脚本，导致我以为人家外国人全是英文，没有转码的问题。所以实现了自己的解决方法，既然写了，就分享出来吧。

jellyfin虽然很好用，但是果然在pc上播放器还是用potplayer舒服。直接说正题，如何分析电影和安装jellyfin不在此篇的讨论范围内。

先说一下原理，在windows里注册一个协议（和http，https一下，不过这里自己定义），然后使用协议打开potplayer，把本地真实的文件路径作为url作为播放参数传入。

首先要下载这个[js脚本][1]

先在注册表创建协议,创建一个txt文件，把这个复制进去，改后缀为reg，然后打开，导入注册表。

    Windows Registry Editor Version 5.00
    
    [HKEY_CLASSES_ROOT\pypot]
    @="URL:pypot Protocol"
    "URL Protocol"=""
    [HKEY_CLASSES_ROOT\pypot\shell]
    [HKEY_CLASSES_ROOT\pypot\shell\open]
    [HKEY_CLASSES_ROOT\pypot\shell\open\command]
    @="E:\\project\\py\\script\\open_in_potplayer.py \"%1\""

其中pypot这个是你要自定义的协议名字。最后的一行是你要打开的软件，因为直接打开potplayer会有转码上的问题，所以这里我写了个python脚本来解决这个转码的问题。

py脚本源码

    import os
    import sys
    from urllib.parse import quote, unquote, urlencode
    
    if __name__ == '__main__':
        url = sys.argv[1]
        url = unquote(url[8:len(url)])
        all_str="D:\软件\PotPlayer\PotPlayerMini64.exe " + url

        os.system("start D:\软件\PotPlayer\PotPlayerMini64.exe " + url)

这里我把py打包成了exe文件。
不打包的话需要修改注册表的最后一行
    
    @="python解释器的绝对路径 脚本的绝对路径 \"%1\""


  [1]: https://github.com/tccoin/Jellyfin-Potplayer
