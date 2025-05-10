# ticketmap

## 1. 简介

**一个具有强大功能的容器**

它是作者开发出来的一个扩展库

[^陈佳宇]: 拿到这个项目刚开始以为是邮票地图？再参阅了文档、调试环境去运行后，发现它其实是一个非常酷的容器，类似map, unordered_map，一个全新的map



## 2. 功能特点

1. 自动键功能管理：自动为每个新插入的元素分配唯一的键（票号）。
2. 键溢出处理：当键类型达到最大值时，能够检测溢出并抛出异常。
3. 迭代器支持：提供迭代器以遍历容器中的所有元素。
4. 容量管理：可以预留足够的空间以存储指定数量的元素。
5. 异常处理：在遇到错误情况（如键溢出）时抛出异常。



## 3. ticketmap的定义及方法

**ticketmap的定义同map, unordered_map一样**

~~~cpp
// 声明
ticket_map<int, string> 变量名;
~~~

**方法：**
~~~cpp
// insert("值") -- 给键插入值
auto 键 = 变量名.insert(值);
// 例
auto ticket = map.insert("Hello World!");
cout << ticket << endl;
// 这里输出0 因为ticket从0自增
cout << map[ticket] ;
// 这里会输出答案值:Hello World!
~~~

~~~cpp
// map.begin() - 返回迭代器的第一个参数

auto one = map.insert("one");
auto two = map.insert("two");
auto three = map.insert("three");

cout << map.begin().ticket << ' ' << map.begin().value << endl;
// 答案输出为 0 one. 因为是添加的第一个键和它的值
~~~



## 4. 使用示例



```markdown
#include "ticket_map.hpp"
#include <iostream>

// 个人习惯：使用命名空间简写代码
using namespace jss; // jss::ticket_map 变为 ticket_map
using namespace std; // std::cout 变为 cout

int main() {
    // 创建一个 ticket_map 实例，键类型为 int，值类型为 string
    ticket_map<int, string> map;

    // 插入元素并获取分配的票号
    auto ticket = map.insert("Hello, World!");
    cout << "Inserted with ticket: " << ticket << endl;

    // 通过票号访问元素
    cout << "Value: " << map[ticket] << endl;

    // 遍历容器中的所有元素
    for (const auto& entry : map) {
        cout << "Ticket: " << entry.ticket << ", Value: " << entry.value << endl;
    }

    return 0;
}
```

**运行结果：**

![1745249897527](/imges/run_problem1.png)



## 5. 运行时候出现的小问题

**第一次在运行的时候出现一个小问题（图1）**

![1745248916445](/imges/run_problem1.png)

**利用Kimi找到了解决这个问题的办法**

![1745249350575](/imges/run_solution.png)



## 6. 环境配置

- 该项目作为c++的一个拓展项目并不需要配置什么环境，只需要提供能编译G++17的编译器

- 只要把项目拉取下来，然后在编译的c++文件中去导入这个库，即可使用

- 如果直接拉取不可以的话或许是没有配置MinGW，这边我使用的是Clion，下载Clion会自带默认MinGW，如果还出现报错的话，那就把进入到MinGW的bin目录当中把地址复制到环境变量->系统变量->path中去配置 

  <!--陈佳宇-->

## 7.翻译
### 80~125行（类型定义注释）
- `Required iterator typedefs`  
  `所需的迭代器类型定义`
- `Compare iterators for inequality.`  
  `比较迭代器是否不相等`
- `Equality in terms of iterator_impls: if it's not not-equal then it must be equal`  
  `就迭代器实现而言的相等性：如果不是不相等，那么它必须相等`
- `Dereference the iterator`  
  `取消引用迭代器`
- `Dereference for iter->m`  
  `对 iter->m 进行解引用（即通过指针/迭代器访问成员 m）`
- `Pre-increment`  
  `前置递增`
- `Post-increment`  
  `后增量`

---

### 127~138行（构造方法注释）
- `Allow constructing a const_iterator from a non-const iterator, but not vice-versa`  
  `允许从非const迭代器构造const迭代器，但反之不可。`
- `A default-constructed iterator is a sentinel value`  
  `默认构造的迭代器是一个哨兵值`

---

### 140~160行（实现细节注释）
- `Construct from an iterator into a map`  
  `通过迭代器构造映射`
- `The stored iterator`  
  `存储的迭代器`
- `The map`  
  `映射`

---

### 80~160行中代码特殊实现说明
- `arrow_proxy operator->()`  
  `通过指针代理访问元素成员（实现 iter->m 语法）`
- `underlying_iterator` 类型说明  
  `底层容器迭代器（根据 is_const 选择 const/non-const 版本）`
- `map_ptr` 类型说明  
  `指向所属容器的指针`
  <!--by 陈蓉蓉-->

<hr>

### 320-400行翻译

```cpp
// Remove the element referenced by the provided iterator.
/// Returns an iterator to the next element if there is one, or end()
/// otherwise.
/// Invalidates any existing iterators into the map.
/// Compacts the data if there are too many empty slots.

// 移除所提供的迭代器引用的元素。
// 如果有下一个元素，则返回一个迭代器，否则返回end（）。
// 使映射中所有现有的迭代器失效。
// 如果存在过多的空闲槽位，则会压缩数据。
constexpr iterator erase(const_iterator pos) noexcept {
            return {erase_entry(data.begin() + (pos.iter - data.begin())),
                    this};
        }


```

```cpp
/// Swap the contents with other. Afterwards, other has the contents and
/// next ticket value of *this prior to the call, and *this has the
/// contents and next ticket value of other prior to the call.

/// 与另一个 `ticket_map` 对象交换内容。交换后，`other` 将包含调用前 `*this` 的内容和下一个票据值，
/// 而 `*this` 将包含调用前 `other` 的内容和下一个票据值。
constexpr void swap(ticket_map &other) noexcept {
            data.swap(other.data);
            std::swap(filledItems, other.filledItems);
            std::swap(nextId, other.nextId);
        }


```

```cpp
/// Remove all elements from *this. Invalidates all iterators into the
/// map.

/// 从 `*this` 中移除所有元素。此操作会使映射中所有迭代器失效。

constexpr void clear() noexcept {
            data.clear();
            filledItems= 0;
        }
```

```cpp
/// Ensure the map has room for at least count items

/// 确保映射至少可以容纳 `count` 个元素。

constexpr void reserve(std::size_t count) {
    if (count > size()) {
        collection_type new_data;
        new_data.reserve(count);
        for (auto &[ticket, value] : data) {
            if (value) {
                new_data.emplace_back(
                    std::move(ticket), std::move(value));
            }
        }
        data.swap(new_data);
    } else {
        compact();
    }
}
```

```cpp
/// Return the maximum number of items that can be inserted without
/// reallocating

/// 返回在不需要重新分配内存的情况下可以插入的最大元素数量。
constexpr std::size_t insert_capacity() const noexcept {
    return data.capacity() - data.size();
}
```

```cpp
/// Return the number of entries for a ticket in the container. The
/// return value is 1 if the ticket is in the container, 0 otherwise.

/// 返回映射中指定票据的条目数量。如果票据存在于映射中，则返回值为 1，否则为 0。

constexpr std::size_t count(Ticket const &ticket) const noexcept {
            return (lookup(data, ticket) == data.end()) ? 0 : 1;
        }
```

```cpp
/// Find the next valid iterator into the map

/// 查找映射中的下一个有效迭代器。
    template <typename Iter>
    constexpr Iter next_valid(Iter iter) const noexcept {
        for (; iter != data.end() && !iter->second; ++iter)
            ;
        return iter;
    }

```

```cpp
/// Erase an entry referenced by an iterator into the internal vector

 /// 删除内部向量中由迭代器引用的条目。
    constexpr typename collection_type::iterator
    erase_entry(typename collection_type::iterator iter) {
        if (iter != data.end()) {
            iter->second.reset();
            iter = next_valid(iter);
            --filledItems;
            if (needs_compaction()) {
                auto ticket = iter != data.end() ? iter->first :
                                                        std::optional<Ticket>();
                compact();
                iter = ticket ? lookup(data, *ticket) : data.end();
            }
        }
        return iter;
    }
```

<!-- by 陈明旭 -->

<hr>

### 400-480行翻译
1. Return the maximum number of items that can be inserted without
 reallocating
**返回在不需要重新分配内存的情况下可以插入的最大项目数。**

2. Return the number of entries for a ticket in the container. Thereturn value is 1 if the ticket is in the container, 0 otherwise.
**返回容器中票证的条目数量。如果票证在容器中，则返回值为1，否则为0。**

3. Erase an entry referenced by an iterator into the internal vector
**删除内部向量中由迭代器引用的条目**

4. Returns true if the container has too many empty slot, falseotherwise
**如果容器中有太多空槽，则返回true，否则返回false。**

5. Compact the container to remove all empty slots.
**压缩容器以移除所有空槽**

6. Increment a ticket and check for overflow (generic)
**增加票证并检查溢出（通用）**
<br>
<!-- by 陈桂生 -->
