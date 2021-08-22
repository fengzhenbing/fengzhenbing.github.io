---
title: "docusaurus构建website"
date: 2021-08-22
description: docusaurus构建website
tags:
    - 博客
    - 网站
    - react
categories:
    - 博客
    - 网站
    - react
---

## docusaurus构建website

### 环境

-  Node.js version >= 12.13.0 以上(node -v 查看)，使用了国际化i18n，则Node.js version >=14以上

- Yarn version >= 1.5 ( yarn --version查看). mac下可以使用[n管理node版本](https://fengzhenbing.github.io/p/mac%E4%B8%8Bnode%E5%8D%87%E7%BA%A7/)

### Title logo等文案，首页展示

待讨论

### 菜单调整

- Nav : 文档  社区 新闻 博客 links  国际化切换  搜索

- Documentation  Community  News Blog  Links

- Footer: 

### 首页

- 下载按钮  文档按钮  star按钮修改

- 样式修改

### 国际化语言

```shell
yarn write-translations  --locale  zh
```

参考https://docusaurus.io/zh-CN/docs/cli#docusaurus-write-translations-sitedir

中英文两个版本的文件名称保持一致。文档中没有指定sidebar_position时，默认按文件名称在菜单栏排序



看中文效果

```shell
 yarn run start -- --locale zh
```



### 多版本

```shell
yarn run docusaurus docs:version 2.3.0

yarn run docusaurus docs:version 2.4.0
```

历史版本的国际化参考

https://docusaurus.io/zh-CN/docs/api/plugins/@docusaurus/plugin-content-docs#i18n

归档目录

![image-20210822214033782](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210822214033782.png)

归档文件翻译目录

![image-20210822214124926](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210822214124926.png)

### hugo文档迁移注意事项

- 约定大于配置：很多目录都是约定好的。

  建议快速通读一遍[文档](https://docusaurus.io/zh-CN/docs)，遇到编译问题，能快速定位到具体章节去查看。

- keywords必须为数组,原来写的字符串

   keywords: ["Apache shenyu"]

- 不支持md文件里的html标签 比如 <font></font>

- sidebar_position: 1 指定文档在菜单栏的顺序第一位。

- md文件中title就是菜单栏显示的名称，无法显示不一样的菜单名和文档标题名。

- md中<img src="/img/shenyu/dataSync/data-sync-dir-zh.png" width="60%" height="50%" />  类似的标签，在发布时生效。

  本地想看效果，可以yarn build.然后将静态文件通过nginx代理访问。



### 参考

https://docusaurus.io/zh-CN/docs
