# 基础
## 问题
1. [java hashmap，hashtable 区别，list初始容量.hashmap初始容量。扩容机制 int几个字节](#1)
2. session和cookies区别，varchar和char区别  datatime和时间戳区别
3. ConcurrentHashMap  有了解吗 jdk1.8 有什么不同
4. 你开发中碰到最多异常是啥。你项目印象最深的问题是哪个，怎么解决的
5. jsp九大内置对象
6. 接口和抽象类区别

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
