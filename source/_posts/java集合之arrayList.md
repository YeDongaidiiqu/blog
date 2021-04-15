---
title: java集合之arrayList
date: 2021-04-08 09:41:52
tags: java
---

#####  construct

![image-20210408174805354](image-20210408174805354.png)

```
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
    
 public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
 可以看到，在无参构造的时候并没有初始化数组，而是在add的时候初始化了一个10的容量，
```
<!--more-->
##### add

```
无参构造的时候会初始化大小
private void add(E e, Object[] elementData, int s) {
        if (s == elementData.length)
            elementData = grow();
        elementData[s] = e;
        size = s + 1;
    }
    
```

##### delete

```
private void fastRemove(Object[] es, int i) {
        modCount++;
        final int newSize;
        if ((newSize = size - 1) > i)
            System.arraycopy(es, i + 1, es, i, newSize - i);
        es[size = newSize] = null;
    }
remove 实际上有一个arrayCopy 的动作    
```

##### set

```
 ArrayList<Object> objects = new ArrayList<>(10);
 objects.set(5,1);
 经典bug  ，源码中有判断  Objects.checkIndex(index, size); 这里随便容量是10，但size还是0
```

