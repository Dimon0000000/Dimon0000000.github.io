---
title: LC.460 LFU-Cache
date: 2022-06-29 06:57:46
tags: 
- Daily algorithm problem solving
- 链表
- 红黑树
- 哈希表
categories: LeetCode
index_img: https://smms.app/image/4SYiG3dFeHmAtIX
---
### 前言
#### 缓存算法

-----

>缓存算法是指令的一个明细表，用于提示计算设备的缓存信息中哪些条目应该被删去。常见类型包括LFU、LRU、ARC、FIFO、MRU。

本文中将介绍[LRU缓存](https://baike.baidu.com/item/LRU)和[LFU缓存](https://baike.baidu.com/item/LFU)。

### 最近最少使用算法(LRU)

#### 介绍

-----

> LRU是Least Recently Used的缩写，即最近最少使用，是一种常用的[页面置换算法](https://baike.baidu.com/item/页面置换算法/7626091)，选择最近最久未使用的页面予以淘汰。该算法赋予每个[页面](https://baike.baidu.com/item/页面/5544813)一个访问字段，用来记录一个页面自上次被访问以来所经历的时间 t，当须淘汰一个页面时，选择现有页面中其 t 值最大的，即最近最少使用的页面予以淘汰。

例如音乐播放软件的**最近播放**页面采取的就是LRU缓存来存储一定数量的音乐缓存。

#### 思路分析

-----

1. 对于该缓存，当我们调用某一某一值时，需要同时将调用的该值移动至序列头部（因为我们更新了**自上次被访问以来所经历的时间 t**）；由于我们需要频繁地调整某一节点地位置，若使用线性表，每一次调整位置的开销为*O(n)*；使用链表可以将每一次移动的开销减小到*O(1)*。
2. 我们已经确定了使用链表，但对于链表来说，每一次的移动开销很小，但每一次的查询开销却依然为*O(n)*，这样和线性表基本没有区别了(线性表查询效率为*O(1)*，移动效率为*O(n)*)；对于这个问题，哈希表无疑是最佳选择(查询效率*O(1)*)；因此，在建表的过程中，同时把节点存储到一个哈希表中，以便于我们快速地访问需要进行修改地节点
3. 对于本题我们使用一个双向链表，这样在使用哈希查询时可以直接查询到该节点，且对节点进行移动时也可以快速地访问到该节点的**前驱节点**和**后继节点**

#### 参考代码

```cpp
class LRUCache {
private:
    struct Node {
        int key, value;
        Node *pre = nullptr;
        Node *next = nullptr;

        Node(int key, int value) : key(key), value(value) {}
    };

public:
    int limit;
    Node *head, *tail;
    unordered_map<int, Node*> map;

    LRUCache(int capacity) {
        limit = capacity;
        head = new Node(-1, -1), tail = new Node(-1, -1);
        head->next = tail;
        tail->pre = head;
    }

    void insertHead(Node *node) {
        node->next = head->next;
        node->pre = head;

        head->next->pre = node;
        head->next = node;
    }

    void deleteNode(Node *node) {
        node->pre->next = node->next;
        node->next->pre = node->pre;
    }

    void moveHead(Node *node, int val) {
        deleteNode(node);
        insertHead(node);
        node->value = val;
    }

    int get(int key) {
        auto it = map.find(key);
        if (it == map.end()) return -1;

        Node *node = it->second;
        moveHead(node, node->value);
        return node->value;
    }

    void put(int key, int value) {
        auto it = map.find(key);

        if (it != map.end()) {
            Node *node = it->second;
            moveHead(node, value);
        } else {
            if (map.size() == limit) {
                Node *node = tail->pre;
                deleteNode(node);
                map.erase(node->key);
            }
            Node *newNode = new Node(key, value);
            insertHead(newNode);
            map.insert(make_pair(key, newNode));
        }
    }
};
```

### 最不经常使用算法(LFU)

#### 介绍

-----

> LFU（least frequently used (LFU) page-replacement algorithm）。即最不经常使用页置换算法，要求在页置换时置换引用计数最小的页，因为经常使用的页应该有一个较大的引用次数。但是有些页在开始时使用次数很多，但以后就不再使用，这类页将会长时间留在内存中，因此可以将引用[计数寄存器](https://baike.baidu.com/item/计数寄存器/2033289)定时右移一位，形成指数衰减的平均使用次数。

#### 思路分析

-----

在LFU中，我们需要根据使用频率和使用时间来对每一个节点的位置进行确定，C++中的set底层为红黑树，可以满足我们的要求

1. 结构体定义为

   ```cpp
   struct Node {
       int freq, time;
       int key, value;
   
       Node(int _freq, int _time, int _key, int _value) : freq(_freq), time(_time), key(_key), value(_value) {}
   
       bool operator<(const Node &rhs) const {
           return freq == rhs.freq ? time < rhs.time : freq < rhs.freq;
       }
   };
   ```

   `key`, `value`为键值以及对应的数据

   `time`, `freq`为对应键值的使用次数以及最后一次使用该键值的时间

   下方还定义了一个比较方法：

   + 当缓存达到其容量 capacity 时，则应该在插入新项之前，移除最不经常使用的项。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最近最久未使用 的键。

2. 变量：

   + `time`代表“当前时间”，每进行任意方法的调用，就对其进行+1更新，便于后续调用节点时更新**使用时间**
   + `set`存储了当前缓存中的所有节点，并按照LFU进行排序
   + `limit`为容量上限(这题居然有0空间的缓存)
   + `map`快速定位到`key`对应的`node`

3. 具体的方法：

   + `get`方法返回对应`key`值的`value`，并更新节点位置；当未找到节点时直接返回-1

     ```cpp
     int get(int key) {
             time++;
     
             if (limit == 0) return -1;
         
             auto it = map.find(key);
             if (it == map.end()) return -1;
             Node node = it->second;
     
             set.erase(node);
     
             node.freq++;
             node.time = time;
     
             set.insert(node);
             it->second = node;
             return node.value;
         }
     ```

   + `put`方法用于新放置一个键：

     + 若`key`存在于缓存中，直接更新对应节点
     + 若不存在于缓存中；如果缓存满了，则先移除掉`set`的头节点(即最不经常使用的节点)；上述判断完成后加入新节点即可

     ```cpp
     void put(int key, int value) {
             time++;
     
             if (limit == 0) return;
             auto it = map.find(key);
             if (it == map.end()) {
                 if (map.size() == limit) {
                     auto node = set.begin();
                     map.erase(set.begin()->key);
                     set.erase(node);
                 }
                 Node node = Node(1, time, key, value);
                 set.insert(node);
                 map.insert(make_pair(key, node));
             } else {
                 auto it = map.find(key);
                 Node node = it->second;
     
                 set.erase(node);
     
                 node.freq++;
                 node.time = time;
                 node.value = value;
     
                 set.insert(node);
                 it->second = node;
             }
         }
     ```

     

#### 参考代码

-----

```cpp
struct Node {
    int freq, time;
    int key, value;

    Node(int _freq, int _time, int _key, int _value) : freq(_freq), time(_time), key(_key), value(_value) {}

    bool operator<(const Node &rhs) const {
        return freq == rhs.freq ? time < rhs.time : freq < rhs.freq;
    }
};

class LFUCache {
private:
    int limit;
    int time = 0;
    set<Node> set;
    unordered_map<int, Node> map;
public:
    LFUCache(int capacity) {
        limit = capacity;
    }

    int get(int key) {
        time++;

        if (limit == 0) return -1;
        auto it = map.find(key);
        if (it == map.end()) return -1;
        Node node = it->second;

        set.erase(node);

        node.freq++;
        node.time = time;

        set.insert(node);
        it->second = node;
        return node.value;
    }

    void put(int key, int value) {
        time++;

        if (limit == 0) return;
        auto it = map.find(key);
        if (it == map.end()) {
            if (map.size() == limit) {
                auto node = set.begin();
                map.erase(set.begin()->key);
                set.erase(node);
            }
            Node node = Node(1, time, key, value);
            set.insert(node);
            map.insert(make_pair(key, node));
        } else {
            auto it = map.find(key);
            Node node = it->second;

            set.erase(node);

            node.freq++;
            node.time = time;
            node.value = value;

            set.insert(node);
            it->second = node;
        }
    }
};
```

