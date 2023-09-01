# LiteLoaderQQNT - anti-recall

LiteLoaderQQNT插件，用于比较完善的防撤回。
使用前需要安装[LiteLoaderQQNT](https://github.com/mo-jinran/LiteLoaderQQNT)，并在QQNT新版上使用。

## 使用方法

建议直接前往LLQQNT应用商店安装本插件。

你也可以前往release中下载压缩包，保留文件夹结构（文件夹名称为`插件名`，内容为github上的内容），将文件夹移动至`LiteLoaderQQNT数据目录/plugins/`下面，重启QQNT即可。

直接克隆源码的话，需要手动进行`npm install`。

## 太长不看版

在NTQQ打开期间无论怎样操作，**反撤回均能生效**。

若开启数据库储存（**默认关闭**，请前往配置界面手动开启），NTQQ重启后可恢复已撤回的消息，目前已测试文本、表情、图片、转发消息均能正常恢复；文件若没下载则不能打开，但还能看见文件消息本身；其他消息类型未测试，不提供保证。

若不满意撤回的颜色、阴影和提示，也请前往配置界面手动调整。

若反撤回消息太多，累积在数据库里可能造成加载卡顿，现在已经做了优化，一般来说不需要清理，但如果觉得卡顿，如有必要，还是可以**隔一段时间**通过插件配置界面清理一次已储存的消息。

## 详细说明

所有被撤回的消息会被带上红框，下方会出现“已撤回”的小提示（均可通过配置关闭）。**重新进消息界面已撤回的消息仍然可以正常显示。**

**使用LevelDB进行数据持久化储存**，默认**不**启用，需要手动去设置里开启。如果不开启，重启QQ消息会丢失。若开启，当前版本重启QQ后**已能恢复文字、图片、转发和表情消息**，其他消息不提供保证，若不能恢复请提`ISSUE`。

`LevelDB`有时候启动的时候会加载失败，导致无法加载反撤回数据库内容。如果发现之前的反撤回失效，重启QQ一般都能解决（目前正在尽力避免这种问题）。

如果你需要调整撤回样式，请前往插件的设置界面，手动修改调整。

不建议手动修改配置文件，修改后不会实时生效（需要重启QQ），建议用配置界面修改。



现在，即使图片在撤回之前未打开过，撤回后也能继续查看了，并且不止缩略图，点开看也是可以的！

但是如果不是在眼前撤回的，而是撤回后你才点进聊天界面去看的，图片仍然会转圈一段时间（请耐心等待），猜测因为QQ会先检查图片是否存在，撤回后还没下载当然不存在，所以转圈；等插件下载好图片后，需要等待QQ重新检查，才能显示图片。

图片如果撤回太快，反撤回后有可能会一直加载不出来，这个可能是图片已经从服务器上删除了，这种情况下，**无法恢复**。

## 原理介绍

**之前版本（过时）：** 为什么在简介里说是简易的呢？因为这个防撤回原理是通过拦截撤回消息的IPC，这样消息就不会被删除。可是，若重新打开消息界面，NTQQ似乎会重新向逻辑层拉取一次消息（旧版QQ的防撤回直接从通信上就拦截了撤回包，所以没有这个问题；而本插件仅拦截到了渲染层的IPC，逻辑层还是一个黑盒），所以导致消息仍然被撤回了。

**现在版本的变更：** 在接到消息后会将消息存于内存中，进入消息界面后，若发现撤回会拦截撤回包，并显示红色边框以及给文字附加删除线。若重新进入消息页面，会遍历消息列表，若发现撤回的提示，会寻找之前保存过的消息，若找到，则将撤回提示替换为之前保存的消息，并显示红色边框以及给文字附加删除线；若未找到，则仍然显示撤回提示。这样，重进消息界面**仍然可以正确反撤回**（包括打开独立聊天界面**也可以**）。

- 为避免太占内存，保存消息的上限默认为10000条。如有需要，可自行修改`main.js`中的`MAX_MSG_SAVED_LIMIT`常量。这个值的意义是，假设默认最多存10000条，那么总接到消息的10000条之前的消息若被撤回则无法恢复。**不过，若一条消息在撤回后你回去看了，并且反撤回生效（也就是只要你看到了这条消息出现红框和删除线），则这条消息会被额外储存到专门的“已撤回消息数组”中，（若开启数据库，也会同时存进数据库），这个数组没有容量上限，不会被本限制所约束。**
- 为避免可能存在的并发问题，消息超过上述限制后，每次接到消息后默认会删除保存的消息列表中的前500条，直到低于上述上限。这个500条你也可以通过修改`main.js`中的`DELETE_MSG_COUNT_PER_TIME`常量来变更。建议修改为你平均每秒接到的消息数量。
- 若开启数据库储存功能，则任何撤回的消息都会存入本地的数据库（位于LLQQNT的插件数据目录（`plugin_data`里），以便于重启后读取已撤回的消息记录。

相关详细的原理和说明请参阅源码。

## 协议及免责

MIT | 禁止用于任何非法用途，插件开发属学习与研究目的，仅自用，未提供给任何第三方使用。任何不当使用导致的任何侵权问题责任自负。
