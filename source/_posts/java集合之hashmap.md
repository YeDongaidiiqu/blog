---
title: java集合之hashmap
date: 2021-04-06 06:55:37
tags: java
---

##### 数据结构

1. 基本数据结构是数组，hash碰撞后采用链表，链表长度超过8后，采用红黑树（1.8）

##### 源码分析
<!--more-->
![image-20210406151808653](image-20210406151808653.png)

```
1. 创建过程
	1.四个构造方法
		不带参数  this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted 默认的扩容因子 0.75f 
			初始划大小 static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16  1左移4位
		指定初始化大小
        指定初始化大小，和负载因子
        从指定的map,创建一个新的hashmap
    2. put 添加值
    	if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length; //可以看到，这是第一次扩容    
       if ((p = tab[i = (n - 1) & hash]) == null)   //计算槽位 (n - 1) & hash]，为啥这么写呢，正常的思维是取模，这种位运算效率更高
            tab[i] = newNode(hash, key, value, null);      		
      if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k)))) //看存在的判断，先判断hashcode是否相等，再判断值，这里为啥要覆写equals 方法得到体现
      else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  //当node超过8时转成了红黑树
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
                
```

##### hashMap 死循环

```
1.7 的时候，hash 重复的时候采用的是头插，直接替换之前的元素，替换下来的元素放在链表中,所以会有概率形成环型链表
1.8 的时候采用的尾插，resize 的时候链表顺序不变
```

