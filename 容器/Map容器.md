# Map容器

常用有3种Map：HashMap、TreeMap、LinkedMap

HashMap：最常用的Map结构，它根据键的HashCode来存储数值，具有很快的访问速度，遍历时取得的数据顺序是完全随机的

TreeMap：实现了SortMap接口，默认按键的升序来排序，当Iterator遍历时得到的顺序是排序过的

LinkedMap：是HashMap的子类，保存了插入数据的顺序，遍历时比HashMap慢