 散列表
----

:earth_asia: **散列表**（**Hash table**，也叫**哈希表**），是根据[键](https://zh.wikipedia.org/wiki/鍵)（Key）而直接访问在内存存储位置的[数据结构](https://zh.wikipedia.org/wiki/数据结构)。也就是说，它通过计算一个关于键值的函数，将所需查询的数据[映射](https://zh.wikipedia.org/wiki/映射)到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做[散列函数](https://zh.wikipedia.org/wiki/散列函数)，存放记录的数组称做**散列表**。
> 使用散列的查找算法分两步。第一步是用散列函数将查找的键转化为数组索引。如果有冲突，散列查找的第二步就是处理碰撞冲突



散列函数
------

:earth_asia:**散列函数**（英语：Hash function）又称**散列算法**、**哈希函数**，是一种从任何一种数据中创建小的数字“指纹”的方法。散列函数把消息或数据压缩成摘要，使得数据量变小，将数据的格式固定下来。该[函数](https://zh.wikipedia.org/wiki/函数)将数据打乱混合，重新创建一个叫做**散列值**（hash values，hash codes，hash sums，或hashes）的指纹。:

除留余法  
------
将整数散列最常用的方法是除留余法。选择大小为**素数**M的数组，对于任意正整数k,计算`k%M`
键为0~1之间的实数，将键表示为二进制数后在使用除留余法(java就是这么做的)。
字符串也可以使用除留余法。一种叫Horner方法的经典算法用N次乘法、加法和取余来计算一个字符串的散列。

```java
final int M = 31;
final int R = 100;
int hash = 0;
String s = "nanbnddgadgnnnadsgaaaagqqqqqqqnqqqn";
for (int i = 0; i < s.length(); i++) {
    hash = (R*hash +s.charAt(i)) % M;
}
```



> Horner 算法是以英国数学家 William George Horner 命名的一种多项式求值的快速算法，实际上，这种快速算法在他之前就已经被Paolo Ruffini使用过了。而中国数学家秦九韶提出这种算法要比William George Horner 早600多年。
>
> ![1567099032709](https://raw.githubusercontent.com/MXDC/images_bed/master/img/1567099032709.jpg)



基于拉链法的散列表
------

一种碰撞处理的方法就是将大小为M的数组中的没过元素指向一个链表，链表中的每个节点都储存该元素的**键值对**。

```java
	
```

