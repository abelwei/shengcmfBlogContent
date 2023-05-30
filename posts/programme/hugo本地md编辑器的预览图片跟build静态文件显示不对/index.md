---
title: "hugo本地md编辑器的预览图片跟build静态文件显示不对"
date: 2023-05-29T09:58:58+08:00
draft: false
categories: ["编程算法"]
tags: ["hugo", "MarkText"]
---

我写hugo的博客是用MarkText写md文件的，什么都很完美（这里我必须要安利一下[MarkText](https://github.com/marktext/marktext)，世界上最好的Markdown编辑器），但有一个问题就是图片问题。


我跟大多数人的做法就是将其放在一个目录下，比如说public/static目录下。

但在编写md的时候MarkText却没引用到，因为你不可能去引用绝对路径，你还是要上传到网络上的。

我又去网上逛逛找解决方案。

最终还是找到了这篇博客[Hugo 图片插入问题](https://sur.moe/post/hugo%E5%9B%BE%E7%89%87/)

就是将文件名作为目录，md文件放到这个文件名目录下之后，改为index.md，将图片放到这个文件名目录下。index.md文件里的图片用相对路径就行了。

比如：

原来通过命令`hugo new posts/hugo本地md编辑器的预览图片跟build静态文件显示不对.md`生成的`hugo本地md编辑器的预览图片跟build静态文件显示不对.md`文件。

变成->

`posts/hugo本地md编辑器的预览图片跟build静态文件显示不对/index.md`将图片放到`posts/hugo本地md编辑器的预览图片跟build静态文件显示不对/`目录下就行。

看这个图片：

![MarkText图标](1685335195642.jpg)


资料引用：

[Hugo 图片插入问题](https://sur.moe/post/hugo%E5%9B%BE%E7%89%87/)

[Hugo 的文件管理方案](https://www.isyin.cn/post/2018-05-03-hugo-%E7%9A%84%E6%96%87%E4%BB%B6%E7%AE%A1%E7%90%86%E6%96%B9%E6%A1%88/)
