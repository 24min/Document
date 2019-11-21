<img src="../images/bowerlist.jpg" alt="bowerlist"  />

# 前言

前几天，在一个`vue`项目中的`package.json`文件中偶然看到有个`browserslist`的配置

```json
{
    "browserslist": [
    "> 1%",
    "last 2 versions"
  ]
}
```

第一反应是，这是啥？浏览器列表？那么下面的`> 1%`以及`last 2 versions`又代表什么呢？这个配置有什么用？

![what](../images/what.png)

带着这些疑问，带上我的键盘，开始疯狂在网上冲浪。

# 1.[browserslist](https://github.com/browserslist/browserslist)

原来`browserslist`的配置是指该为哪些浏览器适配代码，其中的每个字段都代表的是查询条件。比如上述的`last 2 versions`代表的是为最近两个版本的浏览器适配代码。那么一大串的问题又接踵而来了，都有哪些查询条件？我可以看到查询出来的浏览器吗？适配的代码是包括`javascript`以及`css`吗？and more...

![q](../images/q.png)



# Q1：查询的数据来自哪里？

**解：**

>  `Developers set versions list in queries like `last 2 version` to be free from updating versions manually. Browserslist will use [Can I Use](https://caniuse.com/) data for this queries.` 

显而易见，来自[Can I Use](https://caniuse.com/)。

# Q2：都有哪些查询条件？

**解：**

关于有哪些查询条件（不区分大小写）我们也可以在`browserslist`的`github`仓库中找到原文。下面将会做一些搬运以及翻译工作。

-   `> 5%`: browsers versions selected by global usage statistics. `>=`, `<` and `<=` work too. （统计全球范围内使用率大于n%的浏览器版本，也可以使用大于等于，小于 以及小于等于）
-  `> 5% in US`: uses USA usage statistics. It accepts [two-letter country code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements). （同上，只不过地域限制在美国。统计在美国范围内使用率大于n%的浏览器版本）
-  `> 5% in alt-AS`: uses Asia region usage statistics. List of all region codes can be found at [`caniuse-lite/data/regions`](https://github.com/ben-eb/caniuse-lite/tree/master/data/regions). （同上述，只不过区域 被限制在了亚洲。）
-  `> 5% in my stats`: uses [custom usage data](https://github.com/browserslist/browserslist#custom-usage-data). （同上述，只不过支持在自定义数据中查找）
-  `cover 99.5%`: most popular browsers that provide coverage. （全球范围内最受欢迎的浏览器，同样上述也支持地域参数）
-  `maintained node versions`: all Node.js versions, which are [still maintained](https://github.com/nodejs/Release) by Node.js Foundation. （所有官方仍在维护的node版本）

