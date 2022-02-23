# 介绍

ArrayList扩容其实分2种情况：

无参构造器：将会把容量扩容到10，然后每次扩容（旧容量 + (旧容量 / 2)）

有参构造器：会根据传入的数字进行初始化，然后每次扩容（旧容量 + (旧容量 / 2)）



# 无参构造器下扩容

```java
List list = new ArrayList();
for (int i = 1; i <= 10; i++) {
  list.add(i);
}
```

根据上面代码，我们追一下debug，首先会去无参构造器里

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
 
public ArrayList() {
   this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
 }
// 首先会走到无参构造器，将elementData引向空的object[]数组
// 为什么这里的常量 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 要用static修饰？
// 因为JVM虚拟机会做底层优化处理，直接引用的话不会出发类的加载
```

接下来自动装箱，然后直接会跳到add里

```java
public boolean add(E e) {
  ensureCapacityInternal(size + 1);  // Increments modCount!!
  elementData[size++] = e;
  return true;
}
// 往下走肯定会来这里确认一下 elementData的容量
```

我们来看下ensureCapacityInternal里面的代码

```java
private void ensureCapacityInternal(int minCapacity) {
  ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
// 它接收了一个minCapacity，也就是外面的size + 1
// 由于我们是首次加载 size 为 0 ，也就是此时 minCapacity = 1
```

接下来肯定会走calculateCapacity(elementData, minCapacity)，我们一起来看看calculateCapacity里面的代码

```java
private static final int DEFAULT_CAPACITY = 10;
private static int calculateCapacity(Object[] elementData, int minCapacity) {
  if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
    return Math.max(DEFAULT_CAPACITY, minCapacity);
  }
  return minCapacity;
}
// 接收Object[] elementData, int minCapacity
// elementData , 1
```

无参构造器将this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA，所以这里的if语句是成立的，那么就会进入 Math.max(DEFAULT_CAPACITY, minCapacity)，也就是Math.max(10, 1)，很明显这里return 10;

等价于 ensureExplicitCapacity(10);

```java
private void ensureExplicitCapacity(int minCapacity) {
  modCount++;

  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
// 形参 minCapacity = 10
```

if (minCapacity - elementData.length > 0)，这条if语句成立，因为10 - 0 > 0

那么就会触发里面的扩容函数grow(minCapacity);

```java
private void grow(int minCapacity) {
  // overflow-conscious code
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity + (oldCapacity >> 1);
  if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
  if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
  // minCapacity is usually close to size, so this is a win:
  elementData = Arrays.copyOf(elementData, newCapacity);
}
// 形参 minCapacity = 10
```

这里就很明显了，oldCapacity接收了elementData数组的大小，初始化很明显是0

newCapacity 这里的公式其实很简单，就是 oldCapacity + (oldCapacity / 2)

我们看看第一个if (newCapacity - minCapacity < 0)

此时的newCapacity  = 0 ， minCapacity = 10 ， (0 - 10 < 0) 是成立的，也就是会执行

newCapacity = minCapacity;

此时newCapacity = 10

就直接走到最后面一条语句

elementData = Arrays.copyOf(elementData, newCapacity);

```java
 public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
   @SuppressWarnings("unchecked")
   T[] copy = ((Object)newType == (Object)Object[].class)
     ? (T[]) new Object[newLength]
     : (T[]) Array.newInstance(newType.getComponentType(), newLength);
   System.arraycopy(original, 0, copy, 0,
                    Math.min(original.length, newLength));
   return copy;
 }
// elementData是一个Object[] 数组那么其实是走这里new Object[newLength]
// 最后进行一个数组的复制，把原数组的数据复制到copy里
```

最后一路结束栈回到原来的add

```java
 public boolean add(E e) {
   ensureCapacityInternal(size + 1);  // Increments modCount!!
   elementData[size++] = e;
   return true;
 }
```

 elementData[size++] = e; 将进行赋值



总结：如果是无参构造器，将会把容量扩容到10，然后每次扩容（旧容量 + (旧容量 / 2)）



# 有参构造器下扩容

```java
List list = new ArrayList(6);
for (int i = 1; i <= 10; i++) {
  list.add(i);
}
```

根据上面代码，我们继续debug一下

```java
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
// initialCapacity = 6
```

毫无疑问直接进到this.elementData = new Object[initialCapacity];

initialCapacity > 0：根据传入的initialCapacity大小进行一个Object[]数组的赋值

initialCapacity = 0：将elementData赋值空数组

else：抛出一个参数错误的异常

```java
private void ensureExplicitCapacity(int minCapacity) {
  modCount++;

  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
// minCapacity = 1
```

接下来我们看看if (minCapacity - elementData.length > 0)

因为现在 minCapacity - elementData.length > 0 不成立 所以不进行grow扩容

然后下次扩容将会按照grow里面的

newCapacity = oldCapacity + (oldCapacity >> 1);



总结：有参构造器，会根据传入的数字进行初始化，然后每次扩容（旧容量 + (旧容量 / 2)）