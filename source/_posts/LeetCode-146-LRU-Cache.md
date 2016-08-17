---
title: LeetCode_146_LRU_Cache
date: 2016-08-17 20:48:17
tags: 刷题
categories: 学习
---

# LeetCode 146. LRU Cache

> 链接：https://leetcode.com/problems/lru-cache/
>
> 难度：Hard
>
> 标签：Design
>
> 描述：Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: `get` and `set`.
>
> `get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
> `set(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

设计并实现一个用于LRU cache的数据结构，支持`get`和`set`操作。

## LRU 算法

### 原理

LRU（**Least Recently Used**，最近最少使用）算法根据数据的历史访问情况淘汰cache中的数据，其核心思想是：如果数据最近被访问过，那么将来被访问的几率也更高。

最常见的实现方法是使用一个链表来保存缓存数据，详细过程如下：

![LRUcache](http://7xnh8y.com1.z0.glb.clouddn.com/LRU.png)

1. 每当有新数据到来时，将其放置在链表头部；
2. 每当链表中的数据命中时，将其移至链表头部；
3. 当链表满时，丢弃链表末尾的数据。

## 实现

题目描述中，有两个操作需要能够实现，考虑使用双向链表+STL的map实现。这么做的原因是，使用双向链表可以很好地维护链表的形状，查询命中后可以很方便地将其提前而保持其他结点位置关系不变；至于使用map是为了提高查询效率，否则每次查询就需要遍历链表才行了。

为了省事，双向链表就用STL里的list实现啦。

```c++
struct cacheNode{
    int key;
    int value;
    cacheNode(int k, int v) : key(k), value(v) {}
};

class LRUCache{
public:
    LRUCache(int capacity) {
        size = capacity;
    }
    
    int get(int key) {
        if(cacheMap.find(key) != cacheMap.end()) {
            auto it = cacheMap[key];
            // 查询命中，更新位置
            cacheMem.splice(cacheMem.begin(), cacheMem, it);
            cacheMap[key] = cacheMem.begin();
            return cacheMem.begin()->value;
        }
        else return -1;
    }
    
    void set(int key, int value) {
        if(cacheMap.find(key) == cacheMap.end()){
            // 新数据需要插入
            if(cacheMem.size() == size) {
                // cache已满，需要清楚尾端数据
                cacheMap.erase(cacheMem.back().key);
                cacheMem.pop_back();
            }
            cacheMem.push_front(cacheNode(key, value));
            cacheMap[key] = cacheMem.begin();
        }
        else {
            // key已经在cache中
            // 但仍需更新其在cache中的value和位置
            auto it = cacheMap[key];
            cacheMem.splice(cacheMem.begin(), cacheMem, it);
            cacheMap[key] = cacheMem.begin();
            cacheMem.begin()->value = value;
        }
    }
private:
    int size;
    list<cacheNode> cacheMem;
    map<int, list<cacheNode>::iterator > cacheMap;
};
```

