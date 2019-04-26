# 基础
## 问题
1. [java hashmap，hashtable 区别，list初始容量.hashmap初始容量。扩容机制 int几个字节](#1)
2. [session和cookies区别](#2)
3. ConcurrentHashMap  有了解吗 jdk1.8 有什么不同
4. 你开发中碰到最多异常是啥。你项目印象最深的问题是哪个，怎么解决的
5. jsp九大内置对象
6. 接口和抽象类区别
7. varchar和char区别
8. datatime和时间戳区别

## 解答:
1. <span id="1">java hashmap，hashtable 区别，list初始容量.hashmap初始容量。扩容机制 int几个字节</span>    
- 继承和实现  
<table>
   <tr>
        <th width=20%>集合</th>
        <th width=45% style="text-align:center">实现</th>
        <th widht=25% style="text-align:center">继承</th>
   </tr>
    <tr>
        <td>HashMap</td>
        <td rowspan = 2>Map <br>Cloneable<br>Serializable</td>
        <td>AbstractMap</td>
    </tr>
    <tr>
        <td>HashTable</td>
        <td>Dictionary</td>
     </tr>
   <!--colspan-->
</table>

（1）它们都实现了Map接口，但决定到底要用哪一个，还得从性能、线程安全等方面进行考虑

（2）实现了Cloneable接口，代表它们能够被克隆

（3）实现了Serializable接口，代表它们能够被序列化


- 线程安全

集合 | 线程安全
---|---
HashMap | 不安全
HashTable | 安全

（1）HashMap是非synchronized，HashTable是synchronized的

（2）单线程情况下，不需要进行同步，此时HashMap的性能要高于HashTable

（3）HashMap是不同步的，但是我们可以使用Collections这个集合操作类来使HashMap保持同步，具体方法是

```
Map map=Collections.synchronizedMap(new HashMap<>());
```
（4）在JDK1.5中，新增了ConcurrentHashMap类，是线程安全的HashMap，可以用来替代HashTable，而且扩展性也比HashTable好

- 迭代器机制

集合 | 迭代器 | 机制|
---|---|---
HashMap| Iterator | fail-fast
HashTable | Enumerator | 非fail-fast

1）fail-fast ，也称快速失败机制。“快速失败”也就是fail-fast，它是Java集合的一种错误检测机制。当多个线程对集合进行结构上的改变的操作时，有可能会产生fail-fast机制。记住是有可能，而不是一定。例如：假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出 ConcurrentModificationException 异常，从而产生fail-fast机制。

- 新增元素的要求

集合 | 新增元素的要求
---|---
HashMap | key,value都可以为null
HashTable| key,value都不可以为null

（1）HashTable中key和value中任意一个为null，主线程都会抛出NullPointerException  

---

2. <span id ="2">session和cookies区别</span>   
(1)、数据存放位置不同：   
Cookie数据存放在客户浏览器上，session数据存放在服务器上。   
(2)、安全程度不同：
  Cookie不是很安全，别人可以分析存放在本地的Cookie并进行Cookie欺骗，考虑到安全原因，应该使用Session；  
(3)、性能使用等都不同：   
Session会在一定时间内保存在服务器上，当访问增多，会比较占用你服务器的性能、在考虑减轻服务器压力的情况下，设当使用Cookie；     
(4)、数据存储大小不同：   
单个Cookie保存的数据不能超过4k，很多浏览器都限制一个站点最多保存20哥cookie，而session则存储与服务端，浏览器没有限制；  
(5)、会话机制不同：   
Session会话机制：session会话机制是一种服务器端机制，类似哈希结构来保存信息。
Cookie会话机制:cookies是服务器存储本地计算机的小块文本，并随每个请求发送到同一服务器，Web服务器使用HTTP标头将Cookie发送到客户端。在客户端，浏览器解析Cooki并将其保存为本地文件，该文件自动将来同一服务器的任何请求绑定到这些cookie。
---
3. <span id ="3">ConcurrentHashMap  有了解吗 jdk1.8 有什么不同</span>   
**在1.7中**，ConcurrentHashMap的底层数据结构其实是由Segment数组和多个HashEntry组成  
![avatar](/images/20190426161552.png)
Segment数组的意义就是将一个大的table分割成多个小的table来进行加锁，也就是上面的提到的锁分段技术，而每一个Segment元素存储的是HashEntry数组+链表，这个和HashMap的散列表+链表的数据存储结构是一样的。这里的每个table就像我们之前所说的HashTable一样。    
不同于HashTable为了并发情况下的安全性锁整个table那么暴力不同，ConcurrentHashMap的处理方式则是对其进行了优化，是把一个大table分割成小table来分别锁住。也就是我们常说的锁分段。    
同时从源码中我们可以看出，Segment继承了ReentrantLock(独占锁)类。使大table中的每个小table都有了一个锁。
```
static final class Segment<K,V> extends ReentrantLock implements Serializable
```
**在1.8中**，ConCurrentHashMap的数据结构底层是：散列表+链表+红黑树，其变化与HashMap在1.8的变化是一样的
![avatar](/images/20190426161849.png)   
从头注释信息中可以得知  
· ConcurrentHashMap支持高并发情况下对哈希表的访问和更新。   
· ConcurrentHashMap与HashTable相似，与HashMap不同。   
· ConcurrentHashMap的所有操作都是线程安全的。   
· 它不允许null用作键或值   
· get操作没有上锁。是非阻塞的。所以在并发情况下可以与阻塞的put或remove函数交迭。但在聚合操作下比如putAll和clean，并发情况下由于线程调度的原因get函数可能只能检索到插入和删除的一些Entries(函数还未执行完)。  
**ConcurrentHashMap为何存在**   
我们知道由于HashMap在多线程情况下是线程不安全的。而和其对应的HashTable虽然是线程安全的，但是却是十分极端的在所有涉及多线程的操作上都加上了synchronized关键字来锁住整个table。这就意味着在多线程情况下，所有的线程都在竞争同一把锁。虽然是线程安全的，但是却无疑是效率低下的。  
其实HashTable有很多的优化空间，锁住整个table这么粗暴的方法可以变相的柔和点，比如在多线程的环境下，对不同的数据集进行操作时其实根本就不需要去竞争一个锁，因为他们不同hash值，不会因为rehash造成线程不安全，所以互不影响，这就是锁分离技术，将锁的粒度降低，利用多个锁来控制多个小的table，这就是ConcurrentHashMap的核心思想   
总而言之：   
Hashtable是在每个方法上都对hashtable的对象加上了对象锁完成同步，效率低下。
ConcurrentHashMap为每个数组元素加上了桶锁并结合CAS算法来实现同步。
