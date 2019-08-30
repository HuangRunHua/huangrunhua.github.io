博客的搭建教程修改自 [qiubaiying](https://github.com/qiubaiying/qiubaiying.github.io) 

更为详细的教程戳这 [《利用 GitHub Pages 快速搭建个人博客》](http://www.jianshu.com/p/e68fba58f75c) 或 [wiki](https://github.com/qiubaiying/qiubaiying.github.io/wiki/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E6%95%99%E7%A8%8B)

>
### [查看博客戳这里 👆](https://huangrunhua.github.io)



### 使用Open Graph 和 适配Twitter Card

在原先的基础上，我还添加了一些网页链接预览的功能。从图中可以看到，当你通过iMessage、Facebook、Twitter分享你的博客网站给其他人的时候，就不再只显示单一的链接，还可以有图片和文字说明。具体的做法是打开head.html文件，往<head> </head>里面添加：

```html
    <meta property="og:image" content="https://huangrunhua.github.io/img/head.jpg" />
    <meta property="og:description" content="Welcome to Joker's Blog" />

    <!-- 添加Twitter Card支持 -->
    <meta name="twitter:card" content="summary" />
    <meta name="twitter:description" content="Welcome to Joker's Blog" />
    <meta name="twitter:site" content="@myblog" />
    <meta name="twitter:image" content="https://huangrunhua.github.io/img/head.jpg" />
    <meta name="twitter:title" content="Joker's Blog" />
    <meta name="twitter:creator" content="@HookJoker" />
```

就可以啦，详细的内容可以参考[Apple的开发人员文档](https://developer.apple.com/documentation/businesschatapi/messages_sent/sending_rich_link_messages?language=data) 以及 [Twitter的开发人员文档](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/guides/getting-started)。有关Open Graph的详细内容可以参考[Open Graph协议](https://ogp.me)。



### 撰写博文

要发表的文章一般以 **Markdown** 的格式放在这里`_posts/`，你只要看看这篇模板里的文章你就立刻明白该如何设置。

yaml 头文件长这样:

```
---
layout:     post
title:      定时器 你真的会使用吗？
subtitle:   iOS定时器详解
date:       2016-12-13
author:     Joker
header-img: img/post-bg-ios9-web.jpg
catalog: 	 true
tags:
    - iOS
    - 定时器
---

```


## 致谢

1. 这个模板是从这里 [qiubaiying](https://github.com/qiubaiying/qiubaiying.github.io) fork 的, 感谢这个作者。 
2. 感谢 Jekyll、Github Pages 和 Bootstrap!

## License

遵循 MIT 许可证。有关详细,请参阅 [LICENSE](https://github.com/qiubaiying/huangrunhua.github.io/blob/master/LICENSE)。

