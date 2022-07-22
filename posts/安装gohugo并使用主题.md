---
title: "安装gohugo并使用主题"
date: 2022-07-22T15:52:02+08:00
draft: false
---

# 安装gohugo并使用主题

mac下安装

`brew install hugo`

创建博客工程

`hugo new site mysite`

创建一篇文章

```bash
cd mysite
hugo new posts/first.md
```

安装主题

```bash
cd ../themes

git clone https://github.com/dillonzq/LoveIt.git
```

启动调试服务

```bash
hugo serve
```

访问: [http://localhost:1313](http://localhost:1313) ,发现一遍空白.是因为主题没配置好.

配置主题信息

```bash
cd LoveIt
vim config.toml
```

将这个文本覆盖进去

```toml
baseURL = "http://example.org/"

# 更改使用 Hugo 构建网站时使用的默认主题
theme = "LoveIt"

# 网站标题
title = "我的全新 Hugo 网站"

# 网站语言, 仅在这里 CN 大写 ["en", "zh-CN", "fr", "pl", ...]
languageCode = "zh-CN"
# 语言名称 ["English", "简体中文", "Français", "Polski", ...]
languageName = "简体中文"
# 是否包括中日韩文字
hasCJKLanguage = true

# 作者配置
[author]
  name = "xxxx"
  email = ""
  link = ""

# 菜单配置
[menu]
  [[menu.main]]
    weight = 1
    identifier = "posts"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = ""
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "文章"
    url = "/posts/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
  [[menu.main]]
    weight = 2
    identifier = "tags"
    pre = ""
    post = ""
    name = "标签"
    url = "/tags/"
    title = ""
  [[menu.main]]
    weight = 3
    identifier = "categories"
    pre = ""
    post = ""
    name = "分类"
    url = "/categories/"
    title = ""

# Hugo 解析文档的配置
[markup]
  # 语法高亮设置 (https://gohugo.io/content-management/syntax-highlighting)
  [markup.highlight]
    # false 是必要的设置 (https://github.com/dillonzq/LoveIt/issues/158)
    noClasses = false
```

当你运行了 `hugo serve` 命令的时候发现显示不了创建的文章.是因为默认的时候,所有的文章都是草稿没发布的,你需要用 `hugo serve -D` 命令.


### 标签和分类

标签和分类都是在config.toml中默认存在的.

要想在页面上的分类列表中列出文章,需要在文章的头部信息那里写上`categories: ["小目录", "第二个小目录"]`.<u>重新启动刷新就能看到</u>

要想在页面上的标签列表中列出文章,需要在文章头部信息那里写上`tags: ["c", "ccc"]``.<u>重新启动刷新就能看到</u>

### 页面菜单项

页面头部菜单列表,需要在config.toml配置文件中设置.

格式大概如此:

```toml
[menu]
  [[menu.main]]
    weight = 1
    identifier = "posts"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = ""
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "文章"
    url = "/posts/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
  [[menu.main]]
    weight = 2
    identifier = "tags"
    pre = ""
    post = ""
    name = "标签"
    url = "/tags/"
    title = "标签列表"
  [[menu.main]]
    weight = 3
    identifier = "categories"
    pre = ""
    post = ""
    name = "分类"
    url = "/categories/"
    title = ""
```

添加文章用命令行`hugo new posts/first.md`, 会出现在文章列表下.

###### 如果需要特别指定专题目录需要config.toml配置两项:

```toml
[taxonomies]
  series = "series"
  qwer = "qwer"
```

和

```toml
[menu]
  [[menu.main]]
    weight = 4
    identifier = "series"
    pre = ""
    post = ""
    name = "丛书"
    url = "/series/"
    title = ""
  [[menu.main]]
    weight = 5
    identifier = "qwer"
    pre = ""
    post = ""
    name = "测试目录"
    url = "/qwer/"
    title = ""
```

添加文章命令`hugo new series/one.md`或者 `hugo new qwer/two.md`

### 生成静态html

直接用`hugo`命令就行了.

注意:文本里的头部 `draft: false` 草稿必须为false才能生成html显示出来.


### 配置细节

参考`./themes/LoveIt/config.toml`这个目录下的配置说明.

里面有详细备注.并且这里全都是默认值.

`./config.toml`这里面的配置值会替换`./themes/LoveIt/config.toml`里的值.

### 参考资料

[Hugo 完整使用教程](https://www.jianshu.com/p/b979669cf706)

[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/)