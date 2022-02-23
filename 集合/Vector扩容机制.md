# 介绍

>  Vector：相比ArrayList，Vector拥有线程安全

Vector扩容机制也分2种情况：

无参构造器：第一次会初始化10容量的Object[]数组，每次扩容都会旧容量 + 旧容量，也就是2倍容量

有参构造器：如果传了initialCapacity和capacityIncrement，那么第一次初始化一个initialCapacity容量的Object[]数组，每次扩容如果capacityIncrement  > 0，那就是旧容量 + capacityIncrement，否则则是旧容量 + 旧容量



# 扩容机制

```java
List list = new Vector();
for (int i = 0; i < 10; i++) {
  list.add(i);
}
```

我们直接debug一下

```java
public Vector() {
  this(10);
}
```

哦豁，这里它直接调了有参构造器，传了10数值进去

```java
public Vector(int initialCapacity) {
  this(initialCapacity, 0);
}
```

随后这个构造器也同样去调用了另外一个构造器并且传多了一个0 ，这个我们后面解释

```java
public Vector(int initialCapacity, int capacityIncrement) {
  super();
  if (initialCapacity < 0)
    throw new IllegalArgumentException("Illegal Capacity: "+
                                       initialCapacity);
  this.elementData = new Object[initialCapacity];
  this.capacityIncrement = capacityIncrement;
}
```

```java
this.elementData = new Object[initialCapacity];
this.capacityIncrement = capacityIncrement;
// 这里进行了赋值的操作
// this.capacityIncrement 我们后面才知道作用
```

随后我们自动装箱后，来到了add

```java
public synchronized boolean add(E e) {
  modCount++;
  ensureCapacityHelper(elementCount + 1);
  elementData[elementCount++] = e;
  return true;
}
```

我们直接看看ensureCapacityHelper(elementCount + 1);

```java
private void ensureCapacityHelper(int minCapacity) {
  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
```

```java
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);

// minCapacity = 1
// 这里其实是不成立的，为什么？
// 因为 1 - 10 > 0 是False
// 也就是不构成扩容
```

```java
elementData[elementCount++] = e;
// 随后就直接赋值了
```

我们直接debug到可以扩容到部分，这里Vector的写法和ArrayList还是不同的

```java
private void grow(int minCapacity) {
  // overflow-conscious code
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                   capacityIncrement : oldCapacity);
  if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
  if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
  elementData = Arrays.copyOf(elementData, newCapacity);
}
```

```java
int oldCapacity = elementData.length;
int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                 capacityIncrement : oldCapacity);

// 我们看看这里的newCapacity
// 到这里我们就知道capacityIncrement的作用了
// 原来如果 capacityIncrement > 0 的话，那么每次就是旧容量 + capacityIncrement
// 不然就是 旧容量 + 旧容量 就是2倍
```



总结：

无参构造器：第一次会初始化10容量的Object[]数组，每次扩容都会旧容量 + 旧容量，也就是2倍容量

有参构造器：如果传了initialCapacity和capacityIncrement，那么第一次初始化一个initialCapacity容量的Object[]数组，每次扩容如果capacityIncrement  > 0，那就是旧容量 + capacityIncrement