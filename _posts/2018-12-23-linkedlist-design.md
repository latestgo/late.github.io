---
layout: post
title: "链表的实现"
categories: Java
tag: LinkedList
author: "latestgo"
---

# 链表的基本介绍

要想保存多个对象，首先的方法是对象数组，同时如果该数组可以保存任意对象，那应该是Object型的数组。`Object[] data = new Object[3];`。但是数组是**定长**的线性结构。如果数据的数量不确定，那就很难用数组来表示了。

类似火车的车厢的设计模式，动态的进行车厢的挂载，那么假设每一节车厢只保留一个数据，并不受内存的限制，就可以证明解决了数据的长度问题。火车车厢不能只保留一个数据，因为还需要另外一个指向，指向下一个节点。

**范例：** 链表
```java
class Node {
    private Object data;
    private Node next; //定义下一个节点
    public Node(Object data) {
        this.data = data;
    }
    public void setData(Object data) {
        this.data = data;
    }
    public Object getData() {
        return this.data;
    }
    public void setNext(Node next) {
        this.next = next;
    }
    public Node getNext() {
        return this.next;
    }
}
public class TestLinkDemo {
	public static void main(String[] args) throws Exception {
        //1.封装几个节点
        Node root = new Node("head");
        Node n1 = new Node("NA");
        Node n2 = new Node("NB");
        Node n3 = new Node("NC");
        //2. 设置节点的关系
        root.setNext(n1);
        n1.setNext(n2);
        n2.setNext(n3);
        n3.setNext(null);
        print(root);//head NA NB NC
    }
    public static void print(Node node) {
        if(node != null) {
            System.out.println(node.getData());
            print(node.getNext());
        }
    }
}
```

在整个链表的实现过程之中Node类的核心作用在于：保存数据和连接节点关系，但是主方法需要进行节点的创建和关系的配置。所以所谓的链表就是需要有一个单独的类来进行节点的保存以及关系处理。

# 链表简单设计
![PNG image 8.png](https://img-blog.csdnimg.cn/20181222001749137.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

使用私有的内部类可以保证只有Link类才有权操作Node类，而且使用了内部类之后，可以方便进行私有属性的访问，也就是避免了setter和getter。

**范例：** 基本结构
```java
class Link {
    class Node {
        private Object data;
        private Node next; //定义下一个节点
        public Node(Object data) {
            this.data = data;
        }
    }
}
```

链表的核心功能：增加、修改、删除、取出数据、判断某一数据时候存在。

# 数据增加：public void add(Object data)

数据的增加操作，首先应该在链表里面提供有一个追加方法，这个方法的参数应是Object类型。
![PNG image 9.png](https://img-blog.csdnimg.cn/20181222004726253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

**范例：** 添加数据方法
```java
class Link {
    class Node {
        private Object data;
        private Node next; //定义下一个节点
        public Node(Object data) {
            this.data = data;
        }
        public void addNode(Node newNode) {
            if(this.next == null) {
                this.next = newNode;
            } else {
                this.next.addNode(newNode);
            }
        }
    }
    private Node root; //定义头结点
    public void add(Object data) { //末尾添加节点
        if(data == null) {
            return;
        }
        //新建节点，把数据封装到节点中
        Node newNode = new Node(data);
        if(this.root == null) {
            this.root = newNode;
        } else {
            this.root.addNode(newNode);
        }
    }
}
```

# 取得元素个数：public int size()

在一个链表之中可以保存多个元素的数据，那么为了操作方便就需要知道其一共保存的数据个数，所以需要统计节点数的方法。

1. 定义一个属性保存节点个数；
	`private int count = 0;`
2. 添加一个元素count加1；
3. 添加取得元素个数的方法。
	```java
	 public int size() {
        return this.count;
    }
    ```

# 判断是否为空链表:public boolean isEmpty()

如果根元素为null或元素个数为0，则认为是一个空链表，则isEmpty()返回true；
```java
public boolean isEmpty() {
	return this.root == null && this.count == 0;
}
```

# 取得全部数据：public Object[] toArray()

链表是一个动态对象数组，那返回的内容应该是一个对象数组。实现对象数组的返回，首先必须要开辟一个数组（数组长度是count属性），同时这个数组内容的填充应是依次将节点中的数据取出才能完成。
![PNG image 10.png](https://img-blog.csdnimg.cn/20181222223242378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

Link类中的方法：
```java
public Object[] toArray() {
    if (this.count == 0) {
        return null;
    }
    this.retData = new Object[this.count];
    this.foot = 0;
    this.root.toArrayNode();
    return this.retData;
}
```

Node类中的方法：
```java
public void toArrayNode() {
    Link.this.retData[Link.this.foot++] = this.data;
    if (this.next != null) {
        this.next.toArrayNode();
    }
}
```

# 判断数据是否存在：public boolean contains(Object obj)

判断一个数据是否存在于链表之中，有则返回true。

Node类中的containsNode方法：
```java
public boolean containsNode(Object search) {
    if(search.equals(this.data)) {
        return true;
    } else {
        if(this.next != null) {
            return this.next.containsNode(search);
        } else {
            return false;
        }
    }
}
```

Link类中的contains方法：
```java
public boolean contains(Object search) {
    if(search == null || this.root == null)
        return false;
    return this.root.containsNode(search);
}
```

# 寻找指定索引的数据：public Object get(int index)

对于链表中所有的数据都是有顺序的，增加顺序就是保存数据，所以链表属于动态数组，就可以根据索引取得对应数据。

Node类中有一个方法用于索引查找：
```java
public Object getNode(int index) {
    if(Link.this.foot++ == index)
        return this.data;
    else 
        return this.next.getNode(index);
}
```

在Link类中方法：
```java
public Object get(int index) {
    if(index >= this.count)
        return null;
    this.foot = 0;
    return this.root.getNode(index);
}
```

# 修改索引数据：public void set(int index, Object obj)

根据索引修改对应数据，操作跟get类似。

Node类中的setNode()方法：
```java
public void setNode(int index, Object newData) {
    if(Link.this.foot++ == index)
        this.data = newData;
    else
        if(this.next  != null)
            this.next.setNode(index, newData);
}
```

Link类中的set方法：
```java
public void set(int index, Object newData) {
    if(index >= this.count)
        return;
    
    this.foot = 0;
    this.root.setNode(index, newData);
}
```

# 数据删除：public  void remove(Object data)

数据删除处理需要考虑两种情况：

- 要删除的是根节点
	处理的是root节点，只需要在Link类中完成即可 
- 删除的不是根节点
所有的操作应交给Node类来处理

![PNG image 11.png](https://img-blog.csdnimg.cn/20181223213733486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

Node类中的删除：
```java
public void removeNode(Node previous, Object data) {
    if (this.data.equals(data))
        previous.next = this.next;
    else
        this.next.removeNode(this, data);
}
```

Link类中的删除：
```java
public void remove(Object data) {
    if (this.contains(data)) {
        if (this.root.data.equals(data))
            this.root = this.root.next;
        else
            this.root.next.removeNode(this.root, data);
    }
    this.count--;
}
```

# 总结

整个设计只是完成了一个最简单的单向链表，也没有考虑到过多的性能。