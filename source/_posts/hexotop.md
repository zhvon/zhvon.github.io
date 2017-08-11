---
title: HEXO置顶
tags:
  - hexo
  - 置顶
categories:
  - hexo
date: 2017-08-11 17:06:03
---
<Excerpt in index | 首页摘要> 
``Hexo``默认只提供了按发布日期的排序，只好网上找了些资料修改。

原理：在``Hexo``生成首页HTML时，将``top``值高的文章排在前面，达到置顶功能。

实现：修改``JS``文件之后，只需要在``front-matter``中设置需要置顶文章的``top``值，将会根据``top``值大小来选择置顶顺序。（大的在前面）

修改Hexo文件夹下的``node_modules/hexo-generator-index/lib/generator.js``，在生成文章之前进行文章top值排序。
<!-- more -->
<The rest of contents | 余下全文>
需要添加代码：
```javascript
posts.data = posts.data.sort(function(a, b) {
    if(a.top && b.top) { // 两篇文章top都有定义
        if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
        else return b.top - a.top; // 否则按照top值降序排
    }
    else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
        return -1;
    }
    else if(!a.top && b.top) {
        return 1;
    }
    else return b.date - a.date; // 都没定义按照文章日期降序排
});
```

然后编写文章的时候在``front-matter``中加上``top``参数和对应的数值：
```shell
title: xxx
tags:
  - xx
categories:
  - xx
date: 2017-08-11 15:20:09
top: 3333
```

至此，``HEXO``已完美支持置顶。

- ``需要注意的是，这个文件不是主题的一部分，也不是Git管理的，备份的时候比较容易忽略。``
- Reference：[解决Hexo置顶问题](http://www.netcan666.com/2015/11/22/%E8%A7%A3%E5%86%B3Hexo%E7%BD%AE%E9%A1%B6%E9%97%AE%E9%A2%98/)
