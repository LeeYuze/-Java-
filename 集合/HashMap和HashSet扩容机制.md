# 介绍

HashSet扩容机制，其实也是有分无参构造器和有参构造器

无参构造器：

有参构造器：



# 无参构造器下扩容

```java
Set set = new HashSet();
for (int i = 0; i < 13; i++) {
  set.add(i);
}
```

二话不说，直接debug走一走

```java
public HashSet() {
  map = new HashMap<>();
}
```

无参构造器 实例化HashMap赋值给map

难道大佬都说HashSet其实就是HashMap

我们接下来往add方法，debug走一走

```java
private static final Object PRESENT = new Object();

public boolean add(E e) {
  return map.put(e, PRESENT)==null;
}
```

这里有意思 HashSet的add 其实就是HashMap里添加一个键值对，这里的key就是我们添加的值，而value是一个静态常量PRESENT 它是一个Object对象

```java
public V put(K key, V value) {
  return putVal(hash(key), key, value, false, true);
}
```

接下来就是进到HashMap里面的put方法，它将我们的key转成hash值

我们来看看hash值是怎么得到的

```java
static final int hash(Object key) {
  int h;
  return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

原来是先判断key是不是为null，不为null则调用该对象的hashCode()方法获取hashCode值，随后进行一个无符号右移16位，再来个按位异或

接下来我们在debug下

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
  Node<K,V>[] tab; Node<K,V> p; int n, i;
  if ((tab = table) == null || (n = tab.length) == 0)
    n = (tab = resize()).length;
  if ((p = tab[i = (n - 1) & hash]) == null)
    tab[i] = newNode(hash, key, value, null);
  else {
    Node<K,V> e; K k;
    if (p.hash == hash &&
        ((k = p.key) == key || (key != null && key.equals(k))))
      e = p;
    else if (p instanceof TreeNode)
      e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
    else {
      for (int binCount = 0; ; ++binCount) {
        if ((e = p.next) == null) {
          p.next = newNode(hash, key, value, null);
          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
            treeifyBin(tab, hash);
          break;
        }
        if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
          break;
        p = e;
      }
    }
    if (e != null) { // existing mapping for key
      V oldValue = e.value;
      if (!onlyIfAbsent || oldValue == null)
        e.value = value;
      afterNodeAccess(e);
      return oldValue;
    }
  }
  ++modCount;
  if (++size > threshold)
    resize();
  afterNodeInsertion(evict);
  return null;
}
```

是不是非常长其实并不可怕

```java
if ((tab = table) == null || (n = tab.length) == 0)
  n = (tab = resize()).length;
```

这里提一下table，Node<K,V>[] table 是一个Node数组

很明显table数组的初始化是一个null，所以该if语句成立的

接下来我们看看resize()

```java
final Node<K,V>[] resize() {
  Node<K,V>[] oldTab = table;
  int oldCap = (oldTab == null) ? 0 : oldTab.length;
  int oldThr = threshold;
  int newCap, newThr = 0;
  if (oldCap > 0) {
    if (oldCap >= MAXIMUM_CAPACITY) {
      threshold = Integer.MAX_VALUE;
      return oldTab;
    }
    else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
             oldCap >= DEFAULT_INITIAL_CAPACITY)
      newThr = oldThr << 1; // double threshold
  }
  else if (oldThr > 0) // initial capacity was placed in threshold
    newCap = oldThr;
  else {               // zero initial threshold signifies using defaults
    newCap = DEFAULT_INITIAL_CAPACITY;
    newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
  }
  if (newThr == 0) {
    float ft = (float)newCap * loadFactor;
    newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
              (int)ft : Integer.MAX_VALUE);
  }
  threshold = newThr;
  @SuppressWarnings({"rawtypes","unchecked"})
  Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
  table = newTab;
  if (oldTab != null) {
    for (int j = 0; j < oldCap; ++j) {
      Node<K,V> e;
      if ((e = oldTab[j]) != null) {
        oldTab[j] = null;
        if (e.next == null)
          newTab[e.hash & (newCap - 1)] = e;
        else if (e instanceof TreeNode)
          ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
        else { // preserve order
          Node<K,V> loHead = null, loTail = null;
          Node<K,V> hiHead = null, hiTail = null;
          Node<K,V> next;
          do {
            next = e.next;
            if ((e.hash & oldCap) == 0) {
              if (loTail == null)
                loHead = e;
              else
                loTail.next = e;
              loTail = e;
            }
            else {
              if (hiTail == null)
                hiHead = e;
              else
                hiTail.next = e;
              hiTail = e;
            }
          } while ((e = next) != null);
          if (loTail != null) {
            loTail.next = null;
            newTab[j] = loHead;
          }
          if (hiTail != null) {
            hiTail.next = null;
            newTab[j + oldCap] = hiHead;
          }
        }
      }
    }
  }
  return newTab;
}	
```

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
static final float DEFAULT_LOAD_FACTOR = 0.75f;

newCap = DEFAULT_INITIAL_CAPACITY; // 16
newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); //12

threshold = newThr;
Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
table = newTab;

return newTab;
```

第一次初始化，我们只需要关注这几条代码就行

一句话就是获取新的容量和新阈值，创建一个16容量大小的Node数组赋值给table，随后返回一个newTab

```java
if ((tab = table) == null || (n = tab.length) == 0)
  n = (tab = resize()).length;
if ((p = tab[i = (n - 1) & hash]) == null)
  tab[i] = newNode(hash, key, value, null);
```

随后我们回到刚刚 看看putVal下的方法，这里有个很有意思的算法

tab[i = (n - 1) & hash]，根据hash值来确定插入的索引，如果该索引下没有Node对象，就会创建一个新的Node对象

```java
++modCount;
if (++size > threshold)
  resize();
afterNodeInsertion(evict);
return null;
```

再往下走，我们就可以看到这几条代码，它的作用就是判断一下当前数组数量有没有超过阈值（12），如果超过了那就进行扩容，然后返回null

```java
public boolean add(E e) {
  return map.put(e, PRESENT)==null;
}
```

随后回到这里因为返回null == null 所以是true

写到这里，我们就可以下总结了？当size超过12阈值就会扩容？

非也，我们都知道扩容是resize里扩容的，那我们看看那里可以触发扩容

## 怎么会触发扩容？

```java
// 第一处
if ((tab = table) == null || (n = tab.length) == 0)
  n = (tab = resize()).length;

// 第二处 
if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
  treeifyBin(tab, hash);

// 第三处
if (++size > threshold)
  resize();
```

我一共找到了3处会触发扩容的地方

第一处：第一次添加的时候

第二处：当前索引一共有8个node对象时

第三处：超过阈值时

```java
newCap = oldCap << 1;
newThr = oldThr << 1; 
```

每次大小都会右移一位，也就是 * 2

16(12) -> 32(24) -> 64(48) -> 128(96) -> ....