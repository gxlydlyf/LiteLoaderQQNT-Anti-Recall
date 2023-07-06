# LiteLoaderQQNT - anti-recall

LiteLoaderQQNT插件，用于简易的防撤回。
使用前需要安装[LiteLoaderQQNT](https://github.com/mo-jinran/LiteLoaderQQNT)，并在QQNT新版上使用。

## 使用方法

clone或下载zip文件解压，保留文件夹结构（文件夹名称为`插件名`，内容为github上的内容），将文件夹移动至`LiteLoaderQQNT数据目录/plugins/`下面，重启QQNT即可。

为什么在简介里说是简易的呢？因为这个防撤回原理是通过拦截撤回消息的IPC，这样消息就不会被删除。可是，若是重新进入消息列表，NTQQ似乎会重新向服务器拉取一次消息（这和旧版QQ的表现不一致），所以导致消息仍然被撤回了。

所以，目前防撤回仅当你打开聊天窗口时生效，重新进入就失效了。

如果有人知道如何解决，欢迎ISSUE或PR。

## 协议及免责

MIT | 禁止用于任何非法用途，插件开发属学习与研究目的，仅自用，未提供给任何第三方使用。任何不当使用导致的任何侵权问题责任自负。
