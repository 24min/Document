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
-  > 5% in alt-AS: uses Asia region usage statistics. List of all region codes can be found at [`caniuse-lite/data/regions`](https://github.com/ben-eb/caniuse-lite/tree/master/data/regions). （同上述，只不过区域 被限制在了亚洲。）
-  `> 5% in my stats`: uses [custom usage data](https://github.com/browserslist/browserslist#custom-usage-data). （同上述，只不过支持在自定义数据中查找）
-  `cover 99.5%`: most popular browsers that provide coverage. （全球范围内最受欢迎的浏览器，同样上述也支持地域参数）
-  `maintained node versions`: all Node.js versions, which are [still maintained](https://github.com/nodejs/Release) by Node.js Foundation. （所有官方仍在维护的node版本）
-  `node 10` and `node 10.4`: selects latest Node.js `10.x.x` or `10.4.x` release. （最新的大版本为10或者中版本为10.4的node版本）
-  `current node`: Node.js version used by Browserslist right now. （目前被Browserslist 使用的node版本）
-  `extends browserslist-config-mycompany`: take queries from `browserslist-config-mycompany` npm package. （从npm包`browserslist-config-mycompany`中查询）
-  `ie 6-8`: selects an inclusive range of versions. （选择特定浏览器的浏览版本）
-  `Firefox > 20`: versions of Firefox newer than 20. `>=`, `<` and `<=` work too. It also works with Node.js. （火狐20之上的版本，node也可以这么使用）
-  `iOS 7`: the iOS browser version 7 directly. （IOS7自带的浏览器版本，也可以类推到ios8等）
-  `Firefox ESR`: the latest [Firefox ESR] version. （最新的火狐ESR版本 ）
-  `PhantomJS 2.1` and `PhantomJS 1.9`: selects Safari versions similar to PhantomJS runtime. （选择和PhantomJS 运行时相似的Safari版本）
-  `unreleased versions` or `unreleased Chrome versions`: alpha and beta versions. （还处于内测阶段的Chrome版本）
-  `last 2 major versions` or `last 2 iOS major versions`: all minor/patch releases of last 2 major versions. （最近的两个发行版，包括所有的此版本号和补丁版本号变更的浏览器版本）
-  `since 2015` or `last 2 years`: all versions released since year 2015 (also `since 2015-03` and `since 2015-03-10`). （从2015年开始的所有浏览器发行版本或者最近两年内所有浏览器的发行版本，同样支持具体到月份的选择）
-  `dead`: browsers without official support or updates for 24 months.（那些超过24个月未更新或者官方宣布放弃的浏览器版本）
-  `last 2 versions`: the last 2 versions for *each* browser. （所有浏览器的最近两个版本）
-  `last 2 Chrome versions`: the last 2 versions of Chrome browser. （Chrome浏览器的最近两个版本）
-  `defaults`: Browserslist’s default browsers (`> 0.5%, last 2 versions, Firefox ESR, not dead`). （Broeserslist的默认配置）
-  `not ie <= 8`: exclude browsers selected by previous queries. （排除ie版本在8以下的）

**总结：**

todo

# Q3:我可以看到查询出来的浏览器吗?

**解：**



# Q4：其他工具是如何“利用”它的？

**解：**

# Q5：暂未想到，先休息一下明天写



# 参考资料

- [前端工程基础知识点--BrowsersList(基于官方文档翻译)](https://juejin.im/post/5b8cff326fb9a019fd1474d6)
- [Browserslist的git仓库](https://github.com/browserslist/browserslist)

