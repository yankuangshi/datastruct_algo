# 链表（Linked List）

链表也是一种*线性表*数据结构；从内存上看，链表不需要一块连续的内存空间，它是通过"指针"将一组*零散的内存块*串联起来，从而进行数据存储的数据结构

## 链表的形式

链表中的每一个内存块称为链表的*节点*（Node），每个节点除了存储数据外，还需记录链上下一个节点的地址，即*后继指针*（next）

## 为什么使用链表

1. 插入、删除数据的效率高，时间复杂度是O(1)；随机访问效率低，时间复杂度O(n)，需要从头遍历至尾
2. 和数组相比，链表消耗更多的内存空间，因为链表的每个内存块需要额外的空间存储后继指针

## 常用链表

### 单链表（Singly Linked List）

```
  -----------  -----------  -----------  
->|data|next|->|data|next|->|data|next|->NULL
  -----------  -----------  -----------  
                                 ^
                                tail
```

1. 每个节点只包含一个指针，即后继指针
2. 单链表有2个特殊节点，头节点和尾节点，头节点用来记录链表的基地址（用于遍历整条链表），尾节点的后继指针指向空地址NULL
3. 性能：插入和删除的时间复杂度都是O(1)，因为只需要改变指针的指向；随机访问的时间复杂度O(n)，因为需要根据指针一个节点一个节点依次遍历

### 循环链表（Circular Linked List）

```
       -------------------------------- |
       v
  -----------  -----------  ----------- |
  |data|next|->|data|next|->|data|next|->
  -----------  -----------  -----------  
       ^                         ^
     head                       tail 
```

1. 除了尾节点的后继指针指向头节点的地址外，均与单链表一致
2. 适用于存储有循环特定的数据，比如[约瑟夫问题](https://zh.wikipedia.org/wiki/%E7%BA%A6%E7%91%9F%E5%A4%AB%E6%96%AF%E9%97%AE%E9%A2%98)

### 双向链表（Doubly Linked List）

```
   ----------------   ----------------  
<->|prev|data|next|<->|prev|data|next|->NULL
   ----------------   ----------------
```

1. 每个节点需要额外的2个空间来存储后继指针（指向后继节点的地址）和前驱指针（指向前驱节点的地址），所以如果存储同样多的数据，双向链表要比单链表占用更多内存空间
2. 由于多了一个前驱指针，所以找到前驱节点的时间复杂度是O(1)
3. 比单链表的性能优势：

    删除分2种情况：
    1. 删除节点中"值等于某个给定值"的节点
    2. 删除给定指针指向的节点
    
    第一种情况下，虽然单纯的删除操作时间复杂度是O(1)，但是单链表和双链表都需要遍历查找"节点的值等于给定值"，所以总的时间复杂度是O(n)
    
    第二种情况下，要进行删除操作必须找到被删除节点`q`的前驱节点，单链表需要从头开始遍历直到`p->next=q`，时间复杂度是O(n)；双链表只需要`p=q->prev`就能找到前驱节点,
    所以针对第二种情况，双链表只需要O(1)的时间复杂度就可以搞定，比单链表更高效

    插入操作：如果要在某个指定的节点前插入一个新节点，双链表可以在O(1)时间复杂度内搞定，而单链表需要O(n)，分析如以上的第二种删除情况
    
    有序链表下按值查找：双链表比单链表更高效，因为我们可以记录上次查找的位置p，每一次查询根据要查找的值与p的大小关系，决定是向前还是向后查找，所以平均只需要查找一半的数据

### 双向循环链表

头节点的前驱指针指向尾节点，尾节点的后继指针指向头节点    

## 选择数组还是链表

1. 时间复杂度对比：

|时间复杂度|数组|链表|
|:-------:|:----:| :---:|
| 插入删除 | O(n) | O(1) |
| 随机访问 | O(1) | O(n) |

> 数组和链表的对比，不能仅局限于时间复杂度

2. 数组缺点
    1. 如果申请的内存空间太大，比如100M，但是此时内存中没有100M的连续空间，则会申请失败
    2. 数组大小固定，如果超过上限，需要扩容，扩容会导致数据复制（耗时），也可能会导致第一种情况出现（比如1GB大小的数据扩容，申请更大的1.5GB连续内存空间）
3. 链表缺点
    1. 内存空间消耗更大，因为需要额外空间存储指针信息
    2. 对链表进行频繁插入和删除，会导致频繁申请内存和释放，容易造成内存碎片，比如在JVM中会造成频繁GC（耗时）
4. 相对于CPU缓存

数组是连续的内存空间，可以借助CPU缓存机制，预读数组中的数据，访问效率可以更高效；链表的内存不是连续的，所以对CPU缓存不友好，没办法预读

> 实际开发中，针对不同类型项目，需要根据具体情况，权衡选择数组还是链表

## 思考：如何基于链表实现LRU缓存淘汰算法

<details>
<summary>点击展开</summary>

维护一个单链表，越靠近链表尾部的节点是越早之前访问的，当一个新数据被访问，从头开始遍历链表：

1. 如果此数据已在链表中，遍历得到这个数据对应的节点，将其从原来位置删除，然后再插入到头部
2. 如果此数据不在链表中，又分为2种情况：
    
    1. 缓存链表未满，将此节点直接插入到头部
    2. 缓存链表已满，删除尾节点，将新数据节点插入头部

这种基于链表的实现思路，不管缓存链表有没有满，都需要遍历一遍链表，所以时间复杂度是O(n)
</details>

## 链表相关代码练习

* 实现单链表、循环链表、双向链表，支持增删操作
* 实现单链表反转 [LeetCode 206](https://leetcode-cn.com/problems/reverse-linked-list/)
* 链表中环的检测 [LeetCode 141](https://leetcode-cn.com/problems/linked-list-cycle/)
* 实现两个有序的链表合并为一个有序链表 [LeetCode 21](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
* 合并K个排序链表 [LeetCode 23](https://leetcode-cn.com/problems/merge-k-sorted-lists/)
* 删除链表倒数第 n 个结点 [LeetCode 19](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
* 实现求链表的中间节点 [LeetCode 876](https://leetcode-cn.com/problems/middle-of-the-linked-list/)
* 如何利用数组实现LRU缓存淘汰算法
* 如果字符串是用单链表存储的，如何判断是一个回文串
