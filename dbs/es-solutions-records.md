---
description: 记录日常使用过程中遇到的一些问题以及解决方案。
---

# elasticsearch 问题记录

## 收藏置顶

**问题描述：** 一个文档有多人收藏，把zhangsan收藏的文章置顶。

**解决方案：** 定义字段tags\(类型为Array\)。通过es的script进行排序，排序脚本默认使用的是Groovy语法。搜索如下：

```text
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

### 



