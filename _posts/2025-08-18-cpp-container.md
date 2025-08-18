---
title: VLM-Resolution
date: 2025-08-18 12:00:00 +/-TTTT
categories: [cpp]
tags: [cpp ]
---

# C++ 容器 & DFS/BFS & 常用数据结构总结

## 1️⃣ vector

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `vector<int> v;` 或 `vector<int> v(n, 0);` |
| 增 | `v.push_back(5);` <br> `v.insert(v.begin()+2, 10);` |
| 删 | `v.pop_back();` <br> `v.erase(v.begin()+1);` <br> `v.clear();` |
| 改 | `v[0] = 100;` |
| 查 | `v[0]` 或 `v.at(0)` <br> `v.size()` <br> `for(auto x:v) cout<<x;` |
| 空检查 | `v.empty();` |

---

## 2️⃣ unordered_map

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `unordered_map<string,int> mp;` |
| 增 | `mp["apple"] = 5;` <br> `mp.insert({"banana",10});` |
| 删 | `mp.erase("apple");` <br> `mp.clear();` |
| 改 | `mp["banana"] = 20;` |
| 查 | `mp["banana"];` 或 `mp.at("banana");` <br> `mp.count("banana");` <br> `for(auto &p:mp) cout<<p.first<<" "<<p.second;` |
| 空检查 | `mp.empty();` |

---

## 3️⃣ set

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `set<int> s;` |
| 增 | `s.insert(5);` |
| 删 | `s.erase(5);` <br> `s.erase(s.begin());` <br> `s.clear();` |
| 改 | ⚠️ 不支持直接修改元素，需先删后增 |
| 查 | `s.count(5);` <br> `s.find(5);` <br> `*s.begin();` 最小值 <br> `*s.rbegin();` 最大值 |
| 空检查 | `s.empty();` |

---

## 4️⃣ multiset

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `multiset<int> ms;` |
| 增 | `ms.insert(5);`（允许重复元素） |
| 删 | `ms.erase(ms.find(5)); // 删除一个` <br> `ms.erase(5); // 删除所有` |
| 改 | ⚠️ 不支持直接修改 |
| 查 | `ms.count(5);` <br> `ms.find(5);` |
| 空检查 | `ms.empty();` |

---

## 5️⃣ map

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `map<string,int> mp;` |
| 增 | `mp["apple"] = 5;` <br> `mp.insert({"banana",10});` |
| 删 | `mp.erase("apple");` <br> `mp.clear();` |
| 改 | `mp["banana"] = 20;` |
| 查 | `mp["banana"];` 或 `mp.at("banana");` <br> `mp.count("banana");` <br> 遍历：`for(auto &p:mp) cout<<p.first<<" "<<p.second;` |
| 空检查 | `mp.empty();` |

---

## 6️⃣ multimap

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `multimap<string,int> mm;` |
| 增 | `mm.insert({"apple",5});` <br> `mm.insert({"apple",10});` |
| 删 | `mm.erase("apple");`（删除所有该键） |
| 改 | ⚠️ 不支持直接修改 |
| 查 | `mm.count("apple");` <br> 遍历同 map |
| 空检查 | `mm.empty();` |

---

## 7️⃣ stack

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `stack<int> st;` |
| 增 | `st.push(5);` <br> `st.emplace(10);` |
| 删 | `st.pop();` |
| 改 | `st.top() = 99;` |
| 查 | `st.top();` <br> `st.size();` |
| 空检查 | `st.empty();` |

---

## 8️⃣ queue

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `queue<int> q;` |
| 增 | `q.push(5);` <br> `q.emplace(10);` |
| 删 | `q.pop();` |
| 改 | ⚠️ 只能修改队首元素：`q.front() = 99;` |
| 查 | `q.front(); // 队首` <br> `q.back(); // 队尾` <br> `q.size();` |
| 空检查 | `q.empty();` |

---

## 9️⃣ deque

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `deque<int> dq;` |
| 增 | `dq.push_back(5);` <br> `dq.push_front(3);` <br> `dq.emplace_back(10);` <br> `dq.emplace_front(1);` |
| 删 | `dq.pop_back();` <br> `dq.pop_front();` <br> `dq.clear();` |
| 改 | `dq[0] = 100;` <br> `dq.at(0) = 200;` |
| 查 | `dq.front(); // 队首` <br> `dq.back(); // 队尾` <br> `dq.size();` <br> `for(auto x:dq) cout<<x;` |
| 空检查 | `dq.empty();` |

---

## 🔟 priority_queue (优先队列)

| 操作 | 方法 / 示例 |
|------|-------------|
| 创建 | `priority_queue<int> pq; // 大根堆` <br> `priority_queue<int, vector<int>, greater<int>> pq; // 小根堆` |
| 增 | `pq.push(5);` <br> `pq.emplace(10);` |
| 删 | `pq.pop();` |
| 改 | ⚠️ 不支持直接修改 |
| 查 | `pq.top(); // 堆顶元素` <br> `pq.size();` |
| 空检查 | `pq.empty();` |

---

## 1️⃣1️⃣ DFS / BFS 常用容器

| 类型 | 数据结构 | 基本操作 |
|------|---------|-----------|
| DFS | 栈 (`stack<pair<int,int>>`) 或递归 | `st.push({x,y}); st.pop(); st.top(); st.empty(); st.size();` |
| BFS | 队列 (`queue<pair<int,int>>`) | `q.push({x,y}); q.pop(); q.front(); q.back(); q.empty(); q.size();` |
| 最短路 (Dijkstra) | 优先队列 `priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>>` | `pq.push({dist,u}); pq.top(); pq.pop();` |





## 1. `vector<T>`（动态数组）

```cpp
vector<int> v;
v.push_back(10);      // 尾部插入
v.pop_back();         // 删除尾部
v.insert(v.begin()+1, 20);  // 中间插入
v.erase(v.begin()+2);       // 删除第3个元素
v.clear();            // 清空
int n = v.size();     
sort(v.begin(), v.end());
reverse(v.begin(), v.end());
for (int x : v) cout << x << " ";
```

---

## 2. `deque<T>`（双端队列）

```cpp
deque<int> dq;
dq.push_back(1);
dq.push_front(2);
dq.pop_back();
dq.pop_front();
int x = dq.front(), y = dq.back();
```

---

## 3. `list<T>`（双向链表）

```cpp
list<int> l;
l.push_back(1);
l.push_front(2);
l.insert(next(l.begin()), 3); // 在第二个位置插入 3
l.erase(l.begin());           // 删除第一个
for (int x : l) cout << x << " ";
```

---

## 4. `stack<T>`（栈）

```cpp
stack<int> st;
st.push(10);
st.pop();
int t = st.top();
bool empty = st.empty();
```

---

## 5. `queue<T>`（队列）

```cpp
queue<int> q;
q.push(1);
q.pop();
int f = q.front();
bool empty = q.empty();
```

---

## 6. `priority_queue<T>`（堆）

```cpp
priority_queue<int> maxq; // 大顶堆
priority_queue<int, vector<int>, greater<int>> minq; // 小顶堆
maxq.push(5);
maxq.push(2);
int t = maxq.top();
maxq.pop();
```

---

## 7. `set<T>`（有序集合）

```cpp
set<int> s;
s.insert(5);
s.erase(5);
auto it = s.find(5);
auto it1 = s.lower_bound(5); // >=5
auto it2 = s.upper_bound(5); // >5
for (int x : s) cout << x << " ";
```

---

## 8. `multiset<T>`（有序多重集合）

```cpp
multiset<int> ms;
ms.insert(5);
ms.insert(5);  // 可以重复
ms.erase(ms.find(5)); // 只删一个 5
```

---

## 9. `map<Key,Value>`（有序映射）

```cpp
map<string,int> mp;
mp["apple"] = 5;
mp.insert({"banana", 10});
mp.erase("apple");
auto it = mp.find("banana");
for (auto [k,v] : mp) cout << k << " -> " << v << "\n";
```

---

## 10. `unordered_map<Key,Value>`（哈希映射）

```cpp
unordered_map<string,int> ump;
ump["apple"] = 5;
ump.erase("apple");
if (ump.count("apple")) cout << ump["apple"];
for (auto [k,v] : ump) cout << k << " -> " << v << "\n";
```

---

# 📌 统一常用操作（适用大多数容器）

```cpp
c.size();    // 元素个数
c.empty();   // 是否为空
c.clear();   // 清空
c.begin();   // 迭代器起点
c.end();     // 迭代器终点
swap(a, b);  // 交换两个容器
```




## 📌 1. `vector<T>`

* `v.push_back(x)`
* `v.pop_back()`
* `v[i]` / `v.at(i)`
* `v.size()` / `v.empty()`
* 遍历：`for (auto x : v)`
* **查找**（需要排序后才有意义）

  * `auto it = lower_bound(v.begin(), v.end(), x);`
  * `auto it = upper_bound(v.begin(), v.end(), x);`
  * `binary_search(v.begin(), v.end(), x)`

---

## 📌 2. `deque<T>`（双端队列）

* `dq.push_back(x), dq.push_front(x)`
* `dq.pop_back(), dq.pop_front()`
* `dq.front(), dq.back()`
* `dq[i]`（支持随机访问）
* 查找同 `vector`（需要排序 + `lower_bound`）

---

## 📌 3. `list<T>`（双向链表）

* `lst.push_back(x), lst.push_front(x)`
* `lst.pop_back(), lst.pop_front()`
* `lst.insert(it, x), lst.erase(it)`
* 遍历：`for (auto it = lst.begin(); it != lst.end(); ++it)`
* **不支持随机访问**（没有 `operator[]`，没有 `lower_bound`）。

---

## 📌 4. `set<T>`（有序，不重复）

* `s.insert(x)`
* `s.erase(x)` / `s.erase(it)`
* `s.find(x)`（返回迭代器）
* `s.count(x)`（要么 0 要么 1）
* `s.lower_bound(x)` → 第一个 `>= x`
* `s.upper_bound(x)` → 第一个 `> x`
* 遍历有序：`for (auto x : s)`

---

## 📌 5. `multiset<T>`（有序，可重复）

* `ms.insert(x)`
* `ms.erase(ms.find(x))`（只删一个）
* `ms.count(x)`（返回个数）
* `ms.lower_bound(x), ms.upper_bound(x)`
* `ms.equal_range(x)` → 返回 `[l, r)` 区间迭代器

---

## 📌 6. `unordered_set<T>`（哈希表，不重复，无序）

* `us.insert(x), us.erase(x)`
* `us.find(x)`（O(1) 均摊）
* `us.count(x)`（0 或 1）
* **⚠️ 不支持 `lower_bound/upper_bound`（因为无序）**

---

## 📌 7. `map<K,V>`（有序映射）

* `mp[key] = val`（会插入新 key）
* `mp.at(key)`（访问已存在 key）
* `mp.find(key)`
* `mp.count(key)`
* `mp.erase(key)`
* 遍历（有序按 key）：`for (auto &[k,v] : mp)`
* `mp.lower_bound(k), mp.upper_bound(k)`（基于 key）

---

## 📌 8. `multimap<K,V>`（有序，可重复 key）

* `mmp.insert({k,v})`
* `mmp.find(k)`（返回第一个 k 的迭代器）
* `mmp.count(k)`
* `mmp.equal_range(k)` → 所有等于 k 的范围
* `mmp.lower_bound(k), mmp.upper_bound(k)`

---

## 📌 9. `unordered_map<K,V>`（哈希表）

* `ump[key] = val`
* `ump.find(key), ump.count(key)`
* `ump.erase(key)`
* 遍历无序：`for (auto &[k,v] : ump)`
* **⚠️ 不支持 `lower_bound/upper_bound`**

---

## 📌 10. `priority_queue<T>`

* 默认大根堆（最大值优先）
* `pq.push(x), pq.pop()`
* `pq.top()`（最大值）
* **小根堆**写法：

  ```cpp
  priority_queue<int, vector<int>, greater<int>> pq;
  ```
* 没有 `lower_bound`，只能取堆顶。

---

✅ 总结：

* **支持 `lower_bound/upper_bound`** 的容器：`vector`（排序后）、`deque`（排序后）、`set`、`multiset`、`map`、`multimap`
* **不支持的**：`list`、`unordered_set`、`unordered_map`、`priority_queue`


