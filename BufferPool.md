<img src="C:\Users\GUMP\Downloads\bufferpool.svg" alt="bufferpool" style="zoom: 67%;" />

1. Free链表：对应buffer pool的空白页

2. Flush链表：对应buffer pool的脏页（有修改或更新数据）

3. LRU链表：

   冷数据区域对应页的数据第二次访问与第一次访问的时间间隔大于1秒时，将之加入热数据区域。为了处理全表扫描的特殊情况会破坏LRU原则的问题。

