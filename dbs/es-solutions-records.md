**问题描述：** 一个文档有多人收藏，把zhangsan收藏的文章置顶。

**解决方案：** 定义字段tags(类型为Array)。搜索如下：

``` GET _search?_source=tags
{
   "sort" : {
      "_script" : { 
        "script" : "doc['tags'].values.contains('zhangsan') ? 1 : 0",
        "type" : "string",
        "order" : "desc"
      }
   }
}
```

这里用到了通过es的script进行排序，排序脚本默认使用的是Groovy语法。

---
