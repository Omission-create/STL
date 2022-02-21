#### 迭代器

不同容器的迭代器也不同，其功能强弱也有所不同,

`容器的迭代器的功能强弱，决定了该容器是否支持 STL中的某种算法。`

常用的迭代器按功能强弱分为输入迭代器、输出迭代器、前向迭代器、双向迭代器、随机访问迭代器 5 种。

| 容器                          | 对应迭代器类型 |
| ----------------------------- | -------------- |
| `array`（数组容器）           | 随机访问迭代器 |
| `vector`（向量容器）          | 随机           |
| `deque`（双端队列）           | 随机           |
| `list`（双向链表）            | 双向           |
| `set/multiset`                | 双向           |
| `map/multimap`                | 前向           |
| `unordered_map/unordered_set` | 前向           |
| `forward_list`（正向链表）    | 前向           |
| `stack`                       | 不支持迭代器   |
| `queue`                       | 不支持迭代器   |

#### 序列式容器

- array<T,N>（数组容器）：表示可以存储 N 个 T 类型的元素，是 C++本身提供的一种容器。此类容器一旦建立，其==长度就是固定不变的==，这意味着不能增加或删除元素，只能改变某个元素的值；
- vector<T>（向量容器）：用来存放 T 类型的元素，是一个长度可变的序列容器，即在存储空间不足时，会自动申请更多的内存。使用此容器，在==尾部==增加或删除元素的效率最高（时间复杂度为 O(1) 常数阶），在其它位置插入或删除元素效率较差（时间复杂度为 O(n) 线性阶，其中 n 为容器中元素的个数）；
- deque<T>（双端队列容器）：和 vector 非常相似，区别在于使用该容器不仅==尾部==插入和删除元素高效，在==头部==插入或删除元素也同样高效，时间复杂度都是 O(1) 常数阶，但是在容器中某一位置处插入或删除元素，时间复杂度为 O(n) 线性阶；
- list<T>（链表容器）：是一个长度可变的、由 T 类型元素组成的序列，它以双向链表的形式组织元素，在这个序列的任何地方都可以高效地增加或删除元素（时间复杂度都为常数O(1)），但==访问容器中任意元素的速度要比前三种容器慢==，这是因为 list<T> 必须从第一个元素或最后一个元素开始访问，需要沿着链表移动，直到到达想要的元素。
- forward_list<T>（正向链表容器）：和 list 容器非常类似，只不过它以==单链表==的形式组织元素，它内部的元素只能从第一个元素开始访问，是一类比链表容器快、更节省内存的容器。

`注意，其实除此之外，stack<T> 和 queue<T> 本质上也属于序列容器，只不过它们都是在 deque 容器的基础上改头换面而成，通常更习惯称它们为容器适配器，`

[相应的容器函数]([C++序列式容器（STL序列式容器）是什么 (biancheng.net)](http://c.biancheng.net/view/409.html))

`rbegin()` 返回指向容器最后一个元素的迭代器

`rend()`  	返回指向第一个元素所在位置前一个位置的迭代器

`emplace()`指定位置直接生成一个元素

`data()`		返回指向容器中首个元素的指针（指针和迭代器不可以混用）

`merge()`	  合并两个事先排好序的容器，并且合并后的容器是有序的

```mark
#include <iostream>
//需要引入 array 头文件
#include <array>
using namespace std;
int main()
{
    array<int, 5>values; //初始化
    int h = 1;
    auto first = values.rbegin();
    auto last = values.rend(); 
    //初始化 values 容器为 {5,4,3,2,1}
    while (first != last)
    {
        *first = h;
        ++first; //++迭代器向左移动
        h++;
    }
    //重新遍历容器，并输入各个元素
    cout << "values1[0] is : " << values1[0] << endl;
    //at()索引越界会抛出异常out_of_range;
    cout << "values1[1] is : " << values1.at(1) << endl;
    //get<n>参数必须是一个确定的常量，不能是循环变量，只能访问特定元素
    cout << "values1[2] is : " << get<2>(values1) << endl;
    return 0;
}
```

#### `vector`容器

- 初始化空的`vector`容器时，不能使用迭代器

  `对于空的 vector 容器来说，可以通过调用 push_back() 或者借助 resize() 成员函数实现初始化容器的目的。`

- `vector `容器在申请更多内存的同时，会导致之前创建的迭代器失效。

- `emplace_back()`和`push_back()`

  `push_back()` 向容器尾部添加元素时，首先会创建这个元素，然后再将这个元素拷贝或者移动（==优先==）到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）；`emplace_back() `则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程。==优先选用emplace_back()==。

- `remove()` 删除容器中所有和指定元素值相等的元素，并返回指向最后一个元素下一个位置的迭代器

  ```mark
  #include <vector>
  #include <iostream>
  #include <algorithm>
  using namespace std;
  
  int main()
  {
      vector<int>demo{ 1,3,3,4,3,5 };
      //交换要删除元素和最后一个元素的位置
      auto iter = std::remove(demo.begin(), demo.end(), 3);
  	
  	demo.erase(iter,demo.end()); //删除无用元素
  	
      cout << "size is :" << demo.size() << endl;
      cout << "capacity is :" << demo.capacity() << endl;
      //输出剩余的元素
      for (auto first = demo.begin(); first < iter;++first) {
          cout << *first << " ";
      }
      return 0;
  }
  
  //运行结果:
  size is :6
  capacity is :6
  1 4 5
  ```

  注意，在对容器执行完 remove() 函数之后，由于==该函数并没有改变容器原来的大小和容量==，因此无法使用之前的方法遍历容器，而是需要向程序中那样，借助 remove() 返回的迭代器完成正确的遍历。==搭配erase()删除无用元素==。

  `remove() 的实现原理是，在遍历容器中的元素时，一旦遇到目标元素，就做上标记，然后继续遍历，直到找到一个非目标元素，即用此元素将最先做标记的位置覆盖掉，同时将此非目标元素所在的位置也做上标记，等待找到新的非目标元素将其覆盖。因此，如果将上面程序中 demo 容器的元素全部输出，得到的结果为 1 4 5 4 3 5。`

- `shrink_to_fit()`或者`vector<T>(x).swap(x)`

- `vector<bool>`不是存储`bool`类型的容器，尽量避免使用！

  可以选择`deque<bool>`或者`bitset`（不支持插入删除和迭代器操作）来代替

#### `deque`容器

- ==`deque`容器中存储元素并不能保证所有元素都存储到连续的内存空间==。

  `和 vector 相比，额外增加了实现在容器头部添加和删除元素的成员函数，同时删除了 capacity()、reserve() 和 data() 成员函数。`

- [`deque`底层实现](file:///F:/BaiduNetdiskDownload/c%E8%AF%AD%E8%A8%80%E4%B8%AD%E6%96%87%E7%BD%91/STL/2.21%E6%B7%B1%E5%BA%A6%E5%89%96%E6%9E%90deque%E5%AE%B9%E5%99%A8%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86.html)

  ```mark
  //迭代器
  template<class T,...>
  struct __deque_iterator{
      ...
      T* cur;   //当前正在遍历元素指针
      T* first; //指向当前连续空间的首地址
      T* last;  //指向当前连续空间的末尾地址
      map_pointer node;//map_pointer 等价于 T**
      //指向Map数组中存储的指向当前连续空间的指针
  }
  
  //deque容器
  //_Alloc为内存分配器
  template<class _Ty,
      class _Alloc = allocator<_Ty>>
  class deque{
      ...
  protected:
      iterator start; //map数组中首个连续空间信息，cur指向首个元素
      iterator finish; //map数组最后一个连续空间信息，cur指向最后一个元素下一个
      map_pointer map;
  ...
  }
  ```

- 对`deque`进行的排序操作，为了最高效率，可将`deque`先完整的复制到一个`vector`中，对`vector`容器进行排序，再复制回`deque`

#### `list`容器

- `unique()`

  删除`list`容器相邻重复元素，仅保留一份

  `remove_if()`

  ```martk
  void unique()
  void unique(BinaryPredicate) //传入二元谓词函数
  
  #include <iostream>
  #include <list>
  using namespace std;
  //二元谓词函数
  bool demo(double first, double second)
  {
      return (int(first) == int(second));
  }
  
  int main()
  {
      list<double> mylist{ 1,1.2,1.2,3,4,4.5,4.6 };
      //删除相邻重复的元素，仅保留一份
      mylist.unique();//{1, 1.2, 3, 4, 4.5, 4.6}
  
      for (auto it = mylist.begin(); it != mylist.end(); ++it)
          cout << *it << ' ';
      cout << endl;
      //demo 为二元谓词函数，是我们自定义的去重规则
      mylist.unique(demo);
  
      for (auto it = mylist.begin(); it != mylist.end(); ++it)
          std::cout << *it << ' ';
      //删除 mylist 容器中能够使 lamba 表达式成立的所有元素。    
      mylist.remove_if([](int value) {return (value < 10); });
      
      return 0;
  }
  ```

  >  注意，除了以上一定谓词函数的方式，还可以使用`lamba`表达式以及函数对象的方式定义。

- `forward_list`

  > 效率高是选用 forward_list 而弃用 list 容器最主要的原因，换句话说，只要是 list 容器和 forward_list 容器都能实现的操作，应优先选择 forward_list 容器。

  ```mark
  //forward_list不提供size()函数，可以通过<literator>的distance()函数
  	std::forward_list<int> my_words{1,2,3,4};
      int count = std::distance(std::begin(my_words),std::end(my_words));
      
  //forward_list 容器迭代器的移动除了使用 ++ 运算符单步移动，还能使用 advance() 函数
  	std::forward_list<int> values{1,2,3,4};
      auto it = values.begin();
      advance(it, 2);
  ```



### 关联式容器

> 弃用序列式容器，转而选用关联式容器存储元素，往往就是看中了关联式容器可以快速查找、读取或者删除所存储的元素，同时该类型容器插入元素的效率也比序列式容器高。

[红黑树（更高级的二叉查找树）](file:///F:/BaiduNetdiskDownload/c语言中文网/数据结构/9.8红黑树（更高级的二叉查找树）算法详解.html)

#### `pair`

模板定义在<utility>头文件下；

<utility>还为pair对象重载了<、>=、==等6类运算符

```mark
    // 调用构造函数 1，也就是默认构造函数
    pair <string, double> pair1;
    // 调用第 2 种构造函数 pair(const first_type& a, const second_type& b)
    pair <string, string> pair2("STL教程","http://c.biancheng.net/stl/");  
    // 调用拷贝构造函数
    pair <string, string> pair3(pair2);
    //调用移动构造函数
    pair <string, string> pair4(make_pair("C++教程", "http://c.biancheng.net/cplus/"));
    // 调用第 5 种构造函数
    pair <string, string> pair5(string("Python教程"), string("http://c.biancheng.net/python/")); 
```

#### `map`容器

- `map`容器的模板定义如下：

  ```amrk
  template < class Key,                                     // 指定键（key）的类型
             class T,                                       // 指定值（value）的类型
             class Compare = less<Key>,                     // 指定排序规则
             class Alloc = allocator<pair<const Key,T> >    // 指定分配器对象的类型
             > class map;
  ```

- `find()` 找不到返回`end()`

  `lower_bound(key)` 返回指向容器中第一个>=key的键值对的双向迭代器

  `upper_bound(key)` 返回指向容器中第一个>key键值对的迭代器

  `equal_range()`		返回一个pair对象

  > lower_bound(key) 和 upper_bound(key) 更多用于 `multimap` 容器，在 map 容器中很少用到。

- `[]`访问map

  > 注意，只有当 map 容器中确实存有包含该指定键的键值对，借助重载的 [ ] 运算符才能成功获取该键对应的值；反之，若当前 map 容器中没有包含该指定键的键值对

```mark
int main() {
    //创建空 map 容器
    std::map<std::string, int>myMap;
    int cValue = myMap["C语言教程"]; //添加<"C语言教程"，0>
    for (auto i = myMap.begin(); i != myMap.end(); ++i) {
        cout << i->first << " "<< i->second << endl;
    }
    return 0;
}
```

- `insert()`

  ```mark
  //1、引用传递一个键值对
  pair<iterator,bool> insert (const value_type& val);
  //2、以右值引用的方式传递键值对
  template <class P> pair<iterator,bool> insert (P&& val);
  //3、以普通引用的方式传递 val 参数
  iterator insert (const_iterator position, const value_type& val);
  //4、以右值引用的方式传递 val 键值对参数
  template <class P> iterator insert (const_iterator position, P&& val);
  //5、插入其他容器指定区域内的所有键值
  template <class InputIterator>
    void insert (InputIterator first, InputIterator last);
    
   //insert()更新键值对元素 插入失败返回相同建的键值
   mymap.insert(STL).first->second="http://c.biancheng.net/java/";
  
      //创建一个接收 insert() 方法返回值的 pair 对象
      std::pair<std::map<string, string>::iterator, bool> ret;
      //以右值引用的方式传递临时的键值对变量
      ret = mymap.insert({ "C语言教程","http://c.biancheng.net/c/" });
  ```

  > 当实现“向 map 容器中添加新键值对元素”的操作时，insert() 成员方法的执行效率更高；而在实现“更新 map 容器指定键值对的值”的操作时，operator[ ] 的效率更高。

- `emplace()`

  > 实现相同的插入操作，无论是用 emplace() 还是 emplace_hint()，都比 insert() 方法的效率高

  ```mark
  //pair<iterator,bool> emplace()
      pair<map<string, string>::iterator, bool> ret = mymap.emplace("STL教程", "http://c.biancheng.net/stl/");
      
  //iterator emplace_hint(const_iterator position,...)
  
  ```



#### `set`容器

- 从语法上讲 set 容器并没有强制对存储元素的类型做 `const` 修饰，即 set 容器中存储的元素的值是可以修改的。但是，C++ 标准为了防止用户修改容器中元素的值，对所有可能会实现此操作的行为做了限制，使得在正常情况下，用户是无法做到修改 set 容器中元素的值的。

#####  [如何自定义C++ `STL`关联式容器的排序规则？](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/3.17如何自定义C++ STL关联式容器的排序规则？.html)

1. 使用函数对象自定义排序规则（lambda表达式）
2. 重载关系运算符自定义排序

##### [如何修改关联式容器中键值对的键？](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/3.18如何修改关联式容器中键值对的键？.html)

`map`和`mutimap`容器中元素的键是无法修改的，只能先删除后插入，但借助`const_cast`，我们可以直接修改`set`和`multiset`容器中的非键部分。

### 无序关联容器

#### `unordered_map`容器

- 容器模板定义如下：

  ```mark
  template<class Key,
  		 class T,
  		 class Hash = hash(Key); //哈希函数
  		 class Pred = equal_to(Key); //判断键值是否相等的规则
  		 class Alloc = allocator< pair<const Key,T> >
  		> class unordered_map;
  ```

  > 当无序容器中存储键值对的键为自定义类型时，默认的哈希函数 hash 以及比较函数 equal_to 将不再适用，只能自己设计适用该类型的哈希函数和比较函数，并显式传递给 `Hash` 参数和 `Pred` 参数。

- 存储结构是哈希表分离链接法；

  各个链表称为桶（bucket）；

  如果操作无序容器，使得最大负载因子超过默认值，则容器会自动增加桶数，并重新哈希，表示容器特定范围的迭代器失效，但指向单个键值对的引用或指针仍然有效。

  > 这也就解释了，为什么我们在操作无序容器过程中，键值对的存储顺序有时会“莫名”的发生变动。
  >
  > 用于遍历整个容器的 begin()/end() 迭代器对，重哈希只会影响遍历容器内键值对的顺序，整个遍历的操作仍然可以顺利完成。

- 无序容器管理哈希表的成员方法

  `bucket_count()`	=    tableSize

  `load_factor()`	  =	装载因子

  `bucket(key)`		  =	 `hash_function(key)%bucket_count()`

  `bucket_size(n)`    =	 第n个桶存储键值对数

  `reverse(n)`			=	  将容器桶数设置最适合存储n个元素的桶数

- ```mark
  int main()
  {
      //创建空 umap 容器
      unordered_map<string, string> umap;
      //[] 运算符在 = 右侧  [ ] 运算符内的元素为键值,要求的值类型的默认值
      string str = umap["STL教程"];
      //[] 运算符在 = 左侧
      umap["C教程"] = "http://c.biancheng.net/c/";
     
      for (auto iter = umap.begin(); iter != umap.end(); ++iter) {
          cout << iter->first << " " << iter->second << endl;
      }
      return 0;
  }
  ```

- `insert(const_iterator hint,...)`和`emplace_hint(const_iterator position,...)`

  新建值对添加到容器中的位置并不是迭代器说了算，最终取决于该键值对的键的值。

  > 可以这样理解，emplace_hint() 方法中传入的迭代器，仅是给 unordered_map 容器提供一个建议，并不一定会被容器采纳。

[如何自定义C++ `STL`无序容器的哈希函数和比较规则？](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/4.12如何自定义C++ STL无序容器的哈希函数和比较规则？（超级详细）.html)

 当无序容器中存储结构体或类自定义数据类型的数据时，自定义哈希函数用函数对象实现；自定义比较规则既可以通过函数对象方式，也可以使用默认的equal_to<key>规则，==但需要重载 == 运算符==。

### 容器适配器

容器适配器是一个封装了序列容器的类模板，它在一般序列容器的基础上提供了一些不同的功能。之所以称作适配器类，是因为它可以通过适配容器现有的接口来提供不同的功能。

#### `stack`容器适配器

stack<T>：是一个封装了 `deque<T>` 容器的适配器类模板，默认实现的是一个后入先出（Last-In-First-Out，LIFO）的压入栈，没有迭代器。

- ```mark
  template<class T
  		 class Container = deque<T>
  		  > stack
  ```

  > 通过指定第二个模板类型参数，我们可以使用出 `deque` 容器外的其它序列式容器，只要该容器支持 empty()、size()、back()、push_back()、pop_back() 这 5 个成员函数即可。
  >
  > ```ma
  > std::stack<std::string,std::list<string>> valuse;
  > ```

  [stack容器适配器实现计算器（含实现代码）](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/5.3stack容器适配器实现计算器（含实现代码）.html)

- `top()` 返回==栈顶元素==的引用；

  `pop()` `push()`

#### `queue`容器适配器

queue<T>：是一个封装了 `deque<T>` 容器的适配器类模板，默认实现的是一个先入先出（First-In-First-Out，LIFO）的队列，没有迭代器。

- ```mark
  template<class T
  		 class Container = deque<T>
  		  > queue
  ```

  > 作为 queue 容器适配器的基础容器，其必须提供 front()、back()、push_back()、pop_front()、empty() 和 size() 这几个成员函数，符合条件的序列式容器仅有 `deque` 和` list`。

- `front()` `back()`

  `pop()` `push()`

​		[C++ queue容器适配器模拟超市结账环节](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/5.5C++ queue容器适配器模拟超市结账环节.html)

​		![img](file:///C:/Users/QISHEN~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

#### `priority_queue`容器适配器

priority_queue<T>：是一个封装了 vector<T> 容器的适配器类模板，默认实现的是一个会对元素排序，从而保证最大元素总在队列最前面的队列。

- ```mark
  template <typename T,
          typename Container=std::vector<T>,  //只有vector和deque容器符合
          typename Compare=std::less<T> >
  class priority_queue{
      //......
  }
  ```

- [priority_queue容器适配器实现自定义排序](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/5.7priority_queue容器适配器实现自定义排序.html)

  - 以函数对象的方式自定义排序规则
  - 以全局函数/成员函数/友元函数重载 < 或 > 运算符

- 存储结构：最小堆

  `make_heap(first,last,comp)`	first和last可以是指针或迭代器，默认建立大顶堆

  `push_heap(first,last,comp)`	向数组或容器中添加数据，并重建堆

  `pop_heap(first,last,comp)`	  将序列头的元素移动到序列尾，并使[first,last-1]区域元素重建堆

  `sort_heap(first,last,comp)`	对[first,last]区域元素进行堆排序，将其变成有序序列

  `is_heap(first,last,comp)`		检查[first,last]区域元素是否为堆结构

### 迭代器适配器

> 本质上讲，迭代器适配器仍属于迭代器，可以理解为是基础迭代器的“翻新版”或者“升级版”。同时，“xxx 迭代器适配器”通常直接称为“xxx 迭代器”。

#### `reverse_iterator`

反向迭代器是通过操纵内部的基础迭代器实现逆向遍历的

- ```mark
  //创建
  reverse_iterator<vector<int>::iterator> my_reiter(myvector.rbegin());
  
  //base()成员方法 返回当前反向迭代器底层所使用的基础迭代器
  auto iter = my_reiter.base();
  ```

#### `insert_iterator`

- `back_insert_iterator` 底层实现需要调用指定容器的push_back()成员方法（`vector`、`deque`、`list`）；

  传入参数为具体容器；返回参数back_iterator迭代器

  ```mark
  int main() {
      //创建一个 vector 容器
      std::vector<int> foo;
      //创建一个可向 foo 容器尾部添加新元素的迭代器
      std::back_insert_iterator< std::vector<int> > back_it(foo);
      //将 5 插入到 foo 的末尾
      back_it = 5;
      //将 4 插入到当前 foo 的末尾
      back_it = 4;
      //将 3 插入到当前 foo 的末尾
      back_it = 3;
       //输出 foo 容器中的元素
      for (std::vector<int>::iterator it = foo.begin(); it != foo.end(); ++it)
          std::cout << *it << ' ';
      return 0;
  }
  ```

- `front_insert_iterator` 需要push_front方法(`deque`、`list`、`forward_list`)

- `insert_iterator` 需要insert()方法==唯一可用于关联式容器的插入迭代器==

  ```mark
  insert_iterator<Contianer> insert_it(constainer,it);
  //contianer表示目标容器，it是一个基础迭代器，表示新插入元素的位置
  
  //定义一个基础迭代器，用于指定要插入新元素的位置
  std::list<int>::iterator it = ++foo.begin();
      //创建一个 insert_iterator 迭代器
      //std::insert_iterator< std::list<int> > insert_it(foo, it);
      std::insert_iterator< std::list<int> > insert_it = inserter(foo, it);
      //向 foo 容器中插入元素
      insert_it = 1;
      insert_it = 2;
      insert_it = 3;
      insert_it = 4;
      
  //执行结果：5 1 2 3 4 5
  ```

#### 流迭代器

- `istream_iterator`

  重载 ++ 实现，该运算符内部调用`operator>>`读取数据

  ```mark
  #include <iostream>
  #include <iterator>
  using namespace std;
  int main() {
      //用于接收输入流中的数据
      double value1, value2;
      cout << "请输入 2 个小数: ";
      //创建表示结束的输入流迭代器(创建方式1)
      istream_iterator<double> eos;
      //创建一个可逐个读取输入流中数据的迭代器，同时这里会让用户输入数据(创建方式2)
      istream_iterator<double> iit(cin);
      //判断输入流中是否有数据
      if (iit != eos) {
          //读取一个元素，并赋值给 value1
          value1 = *iit;
      }
      //如果输入流中此时没有数据，则用户要输入一个；反之，如果流中有数据，iit 迭代器后移一位，做读取下一个元素做准备
      iit++;
      if (iit != eos) {
          //读取第二个元素，赋值给 value2
          value2 = *iit;
      }
      //输出读取到的 2 个元素
      cout << "value1 = " << value1 << endl;
      cout << "value2 = " << value2 << endl;
      return 0;
  }
  ```

  > 只有读取到 `EOF` 流结束符时，程序中的 `iit` 才会和 `eos` 相等。另外，Windows 平台上使用 `Ctrl+Z` 组合键输入 `^Z` 表示 `EOF` 流结束符，此结束符需要单独输入，或者输入换行符之后再输入才有效。

- `ostream_iterator`

  重载 = 运算实现

  ```mark
  int main() {
      //创建一个输出流迭代器(创建方式1)
      ostream_iterator<string> out_it(cout);
      //向 cout 输出流写入 string 字符串
      *out_it = "http://c.biancheng.net/stl/";
      cout << endl;
      //创建一个输出流迭代器，设置分隔符 ,  (创建方式2)
      ostream_iterator<int> out_it1(cout, ",");
      //向 cout 输出流依次写入 1、2、3
      *out_it1 = 1;
      *out_it1 = 2;
      *out_it1 = 3;
      return 0;
  }
  
      //经常与copy()连用
      //创建输出流迭代器
      std::ostream_iterator<int> out_it(std::cout, ", ");
      //将 myvector 容器中存储的元素写入到 cout 输出流中
      std::copy(myvector.begin(), myvector.end(), out_it);
  ```

#### 流缓冲区迭代器

- `istreambuf_iterator`

  从指定的流缓冲区读取字符元素，本质上是一个输入迭代器

  ```mark
  int main() {
      //创建结束流缓冲区迭代器(创建方式1)
      istreambuf_iterator<char> eos;
      //创建一个从输入缓冲区读取字符元素的迭代器(创建方式2)
      istreambuf_iterator<char> iit(cin);
      //istreambuf_iterator<char> iit(cin.rdbuf());
      string mystring;
      cout << "向缓冲区输入元素：\n";
      //不断从缓冲区读取数据，直到读取到 EOF 流结束符
      while (iit != eos) {
          mystring += *iit++;
      }
      cout << "string：" << mystring;
      return 0;
  }
  ```

- `ostreambuf_iterator`

  ```MARK
  int main() {
      //创建一个和输出流缓冲区相关联的迭代器
      std::ostreambuf_iterator<char> out_it(std::cout); // stdout iterator
      //向输出流缓冲区中写入字符元素
      *out_it = 'S';
      *out_it = 'T';
      *out_it = 'L';
      //和 copy() 函数连用
      std::string mystring("\nhttp://c.biancheng.net/stl/");
      //将 mystring 中的字符串全部写入到输出流缓冲区中
      std::copy(mystring.begin(), mystring.end(), out_it);
      return 0;
  }
  ```

#### 移动迭代器缓冲器

- `move_iterator`

  ```makr
  //将 vector 容器的随机访问迭代器作为新建移动迭代器底层使用的基础迭代器
  typedef std::vector<std::string>::iterator Iter;
  //调用默认构造函数，创建移动迭代器(创建方式1)
  std::move_iterator<Iter>mIter;
  //创建方式2
  std::move_iterator<Iter>mIter(myvec.begin());
  //创建方式3
  std::move_iterator<Iter>mIter = make_move_iterator(myvec.begin());
  
  	//创建并初始化一个 vector 容器
      vector<string> myvec{ "STL","Python","Java" };
      //再次创建一个 vector 容器，利用 myvec 为其初始化		    	
       vector<string>othvec(make_move_iterator(myvec.begin()), make_move_iterator(myvec.end()));
       
       //提供base()方法 获取到当前移动迭代器底层所使用的基础迭代器
       
  ```

  > 移动迭代器将某区域元素移动到其他容器中，虽然原容器中残留之前存储的元素，但已经不能再使用。

#### 迭代器辅助函数

| 迭代器辅助函数        | 功能                                                         |
| --------------------- | ------------------------------------------------------------ |
| advance(it, n)        | it 表示某个迭代器，n 为整数。该函数的功能是将 it 迭代器前进或后退 n 个位置。 |
| distance(first, last) | first 和 last 都是迭代器，该函数的功能是计算 first 和 last 之间的距离。 |
| begin(cont)           | cont 表示某个容器，该函数可以返回一个指向 cont 容器中第一个元素的迭代器。 |
| end(cont)             | cont 表示某个容器，该函数可以返回一个指向 cont 容器中最后一个元素之后位置的迭代器。 |
| prev(it)              | it 为指定的迭代器，该函数默认可以返回一个指向上一个位置处的迭代器。注意，it 至少为双向迭代器。 |
| next(it)              | it 为指定的迭代器，该函数默认可以返回一个指向下一个位置处的迭代器。注意，it 最少为前向迭代器。 |

- `advance(it,n)`底层采取不同的机制。如果 it 是随机访问迭代器，则支持 it+n；否则采用 n 个 ++ 实现。

- [如何将`const_iterator`转换为`iterator`类型迭代器？](file:///F:/BaiduNetdiskDownload/c语言中文网/STL/6.9如何将const_iterator转换为iterator类型迭代器？.html)

  - `iterator`到`const_iterator`编译器会自动进行隐式类型转换；

  - `const_iterator`到`iterator`不能简单使用`const_cats`，因为不是`const T`和`T`的关系

  - ```mark
    //将 const_iterator 转换为 iterator
    advance(iter, distance<cont<T>::const_iterator>(iter,citer));
    //将 const_reverse_iterator 转换为 reverse_iterator
    advance(iter, distance<cont<T>::const_reverse_iterator>(iter,citer));
    ```

- `begin()`作用在容器上返回迭代器，作用在普通数组上返回指针；
- `prev(it,n)`返回一个距离指定迭代器 n 个元素的迭代器；
- `next(it,n)`



### `STL`常用算法（排序、合并、搜索和分区）

#### `sort()`排序

| 函数名                                                     | 用法                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| sort (first, last)                                         | 对容器或普通数组中 [first, last) 范围内的元素进行排序，默认进行升序排序。 |
| stable_sort (first, last)                                  | 和 sort() 函数功能相似，不同之处在于，对于 [first, last) 范围内值相同的元素，该函数不会改变它们的相对位置。 |
| partial_sort (first, middle, last)                         | 从 [first,last) 范围内，筛选出 muddle-first 个最小的元素并排序存放在 [first，middle) 区间中。 |
| partial_sort_copy (first, last, result_first, result_last) | 从 [first, last) 范围内筛选出 result_last-result_first 个元素排序并存储到 [result_first, result_last) 指定的范围中。 |
| is_sorted (first, last)                                    | 检测 [first, last) 范围内是否已经排好序，默认检测是否按升序排序。 |
| is_sorted_until (first, last)                              | 和 is_sorted() 函数功能类似，唯一的区别在于，如果 [first, last) 范围的元素没有排好序，则该函数会返回一个指向首个不遵循排序规则的元素的迭代器。 |
| void nth_element (first, nth, last)                        | 找到 [first, last) 范围内按照排序规则（默认按照升序排序）应该位于第 nth 个位置处的元素，并将其放置到此位置。同时使该位置左侧的所有元素都比其存放的元素小，该位置右侧的所有元素都比其存放的元素大。 |

- `sort()`基于快速排序实现；`stable_sort()`基于归并排序实现

- `partial_sort`以交换元素存储位置的方式实现部分排序，平均时间复杂度`NlogM`

  `partial_sort_copy()`先拷贝新容器中然后再排序，还可以对 list 和 forward_list 排序

- ==自定义算法规则，应优先使用函数对象==。因为内联函数执行效率比普通函数执行效率高

  > C++ 并不能真正地将一个函数作为参数传递给另一个函数，换句话说，如果我们试图将一个函数作为参数进行传递，编译器会隐式地将它转换成一个指向该函数的指针，并将该指针传递过去。

#### `merge()`合并

- `merge(first1,last1,first2,last2,result)` result输出迭代器，用于为最终生成的新有序序列指定存储位置，该函数返回输出迭代器指向新有序序列的最后一个元素之后的位置
- `inplace_merge(first,middle,last)`将两个序列合并成一个升序序列且新序列让存储再first数组中

#### `find()`查找

- `ietrator find ( iterator first , iterator last , const T& val );`

- `iterator find_if ( iterator first , iterator last , mycomp() );`

  返回满足`mycomp()`的第一个对象迭代器

- `iterator find_if_not ( iterator first , iterator last , mycomp() );`

- ```mark
  //查找序列 [first1, last1) 中最后一个子序列 [first2, last2)
  ForwardIterator find_end (ForwardIterator first1, ForwardIterator last1,
                            ForwardIterator first2, ForwardIterator last2);
  //查找序列 [first2, last2) 中，和 [first2, last2) 序列满足 pred 规则的最后一个子序列  返回一个指向查找到的子序列的第一个元素正向迭代器
  ForwardIterator find_end (ForwardIterator first1, ForwardIterator last1,
                            ForwardIterator first2, ForwardIterator last2,
                            BinaryPredicate pred);
  ```

  > 第一种语法格式也可以看做是包含一个默认的 `pred` 参数，该参数指定的是一种相等规则，即在 `[first1, last1)` 范围内查找和 `[first2, last2)` 中各个元素对应相等的子序列

- ``find_first_of ()` 同上，在 `[first1, last1)` 范围内查找和 `[first2, last2)` 中任何元素相匹配的第一个元素的迭代器

- `adjacent_find ( first , last , compare() )` 返回指向连续相等元素的第一个元素的正向迭代器

- `search()`与`find_end()`相对应，用于在序列 A 中查找序列 B 第一次出现的位置

- ```mark
  //search_n ()实现
  template<class ForwardIterator, class Size, class T>
  ForwardIterator search_n (ForwardIterator first, ForwardIterator last,
                              Size count, const T& val)
  {
      ForwardIterator it, limit;
      Size i;
      //Limit表示最后最后能找到的位置
      limit=first; std::advance(limit,std::distance(first,last)-count);
      while (first!=limit)
      {
          it = first; i=0;
          while (*it==val)       // 或者 while (pred(*it,val))，对应第二种格式
          { ++it; if (++i==count) return first; }
          ++first;
      }
      return last;
  }
  ```

#### `partition()` 分组

- ```
  ForwardIterator partition (ForwardIterator first,
                             ForwardIterator last,
                             UnaryPredicate pred);
  //返回一个指向分组分界位置的迭代器
  ```

  > `pred`指定筛选规则，所谓筛选规则，其本质就是一个可接收 1 个参数且返回值类型为 `bool` 的函数，可以是普通函数，也可以是一个函数对象。

- ```
  //不改变原迭代器的元素位置
  pair<OutputIterator1,OutputIterator2> partition_copy (
                      InputIterator first, InputIterator last,
                      OutputIterator1 result_true, OutputIterator2 result_false,
                      UnaryPredicate pred);
   //返回分别指向两个不同容器的迭代器对
  ```

- ```
  //返回迭代器指向的是 [first, last] 范围内第一个不符合 pred 筛选规则的元素
  //即返回指向分界的元素
  ForwardIterator partition_point (ForwardIterator first, ForwardIterator last,
                                   UnaryPredicate pred);
  ```

​		[partition_point - C++ Reference ](http://www32.cplusplus.com/reference/algorithm/partition_point/?kw=partition_point)底层实现二分查找，时间复杂度`O(logN)`

#### 其他函数

- ```
  //在 [first, last) 区域内查找不小于 val 的元素
  ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last,
                               const T& val);
  //在 [first, last) 区域内查找第一个不符合 comp 规则的元素
  ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last,
                               const T& val, Compare comp);
  ```

  -  lower_bound()、upper_bound()、equal_range() 以及 binary_search() 这 4 个查找函数，它们的底层实现采用的都是二分查找的方式

  - 第一种语法格式也设定有比较规则，只不过此规则无法改变，即使用 < 小于号比较 [first, last) 区域内某些元素和 val 的大小，直至找到一个不小于 val 的元素。这也意味着，如果使用第一种语法格式，则 [first,last) 范围的元素类型必须支持 < 运算符。

  - ```mark
    //查找一个不大于3的元素
    class mycomp {
    public:
        bool operator()(const int& i, const int& j) {
            return i>j;
        }
    };
     
     vector<int> myvector{ 4,5,3,1,2 }; //看似乱序，但对于3来说是有序的
        //根据 mycomp2 规则，从 myvector 容器中找到第一个违背 mycomp2 规则的元素
        vector<int>::iterator iter = lower_bound(myvector.begin(), myvector.end(),3,mycomp());
    ```

  - 底层实现代码基本与`parttition_point( )` 相同，二分法仅适用于已排好序的序列

- `bool binary_search( first , last , const T& val , mycomp() );`

  ```mark
  //第一种语法格式的实现
  template <class ForwardIterator, class T>
  bool binary_search (ForwardIterator first, ForwardIterator last, const T& val)
  {
      first = std::lower_bound(first,last,val); //指向第一个>=val的指针
      return (first!=last && !(val<*first)); //如果*first>val意味着没有找到
  }
  //第二种语法格式的底层实现
  template<class ForwardIt, class T, class Compare>
  bool binary_search(ForwardIt first, ForwardIt last, const T& val, Compare comp)
  {
      first = std::lower_bound(first, last, val, comp);
      return (!(first == last) && !(comp(val, *first)));
  }
  ```

- `all_of `、`any_of`、`none_of`

  - all_of() 算法会返回 true，前提是序列中的所有元素都可以使谓词返回 true。
  - any_of() 算法会返回 true，前提是序列中的任意一个元素都可以使谓词返回 true。
  - none_of() 算法会返回 true，前提是序列中没有元素可以使谓词返回 true。

  ```mark
  //检查是否含有<min_age的元素
  std::vector<int> ages {22, 19, 46, 75, 54, 19, 27, 66, 61, 33, 22, 19};
  int min_age{18};
  std::cout << "There are "<< (std::none_of(std::begin(ages), std::end(ages),[min_age](int age) { return age < min_age; }) ? "no": "some") << " people under " << min_age << std::endl; //将传入的ages容器中的元素和min_age值做比较，用none_of返回的布尔值选择输出信息no或some
  
  //count 和 count_if
  //确定ages中等于the_age的元素个数
  std::cout << "There are "<< std::count(std::begin(ages),std::end(ages),the_age)<< " people aged "<< the_age << std::endl;
  //查找大于Max_age的元素个数
  std::cout << "There are "<< std::count_if(std::begin(ages), std::end(ages),[max_age](int age) { return age > max_age; }) << " people aged over " << max_age << std::endl;
  ```

- ```mark
  //bool equal ( first1 , last1 , first2);
  //bool equal ( first1 , last1 , first2 , last2 ,[](const string& s1, const string& s2) { return s1[0] == s2[0]; })
  ```

  > 尽管可以用 equal() 来比较两个同种类型的容器的全部内容，但最好还是使用容器的成员函数 operator==() 来做这些事

- ```mark
  //pair<iterator1,iterator2> mismatch( first1 , last1 , first2 );
  序列匹配失败，iter1和iter2分别指向不匹配的元素；匹配成功，都向后移动相同距离到末尾
  //pair<iterator1,iterator2> mismatch( first1 , last1 , first2 , last2 ); 
  ```

- `bool lexicographical_compare( first1 , last1 , first2 , last2 )` 字典序

- ```makr
  //copy_n(first,n,iterator) 返回指向最后一个被复制元素的后一个位置的迭代器
  std::vector<string> names {"A1","Beth", "Carol", "Dan", "Eve","Fred","George" ,"Harry", "Iain", "Joe"};
  std::unordered_set<string> more_names {"Janet", "John"};
  std::copy_n(std:rbegin(names)+1, 3, std::inserter(more_names, std::begin(more_names)));//inserter()生成insert_iterator对象调用容器成员函数Insert()来向容器添加元素
  
  //目的地址可以是流迭代器 返回第三个参数
  std::copy_n(std::begin(more_names), more_names.size()-1,std::ostream_iterator<string> {std::cout, " "});
  
  //iterator copy_if(first1,last1,first2,last2,mycomp())
  std::copy_if(std::begin(names), std::end(names), std::inserter(more_names, std::begin(more_names)), [max_length](const string& s) { return s.length() <= max_length;});
  
  //目的地址可以是流迭代器
  ```

- `copy(begin(from),end(from),begin(from))`与`copy_backward(begin(from),end(from),end(to))` 

  	`copy_backward()`优势：==序列重叠时==

- ```mark
  // Testing for palindromes using reverse_copy()
  #include <iostream>                                      // For standard streams
  #include <iterator>                                      // For stream iterators and begin() and end()
  #include <algorithm>                                     // For reverse_copy() and copy_if()
  #include <cctype>                                        // For toupper() and isalpha()
  #include <string>
  using std::string;
  
  int main()
  {
      while(true)
      {
      	//数据输入
          string sentence;
          std::cout << "Enter a sentence or Ctrl+Z to end: ";
          std::getline(std::cin, sentence);
          if(std::cin.eof()) break;
  
          // Copy as long as the characters are alphabetic & convert to upper case
          string only_letters;
          std::copy_if(std::begin(sentence), std::end(sentence), std::back_inserter(only_letters),[](char ch) { return std::isalpha(ch); });  //仅读取字母
          std::for_each(std::begin(only_letters), std::end(only_letters), [](char& ch) { ch = toupper(ch); }); //转化成大写
  
          // Make a reversed copy
          string reversed;
          std::reverse_copy(std::begin(only_letters), std::end(only_letters), std::back_inserter(reversed));
          std::cout << '"' << sentence << '"'<< (only_letters == reversed ? " is" : " is not") << " a palindrome." << std::endl;
      }
  }
  ```

- ```mark
  //输出
  for_each()
  copy(begin,end,ostream_iterator<>(cout," "));
  cout<<endl;
  ```

- `unique(begin,end)`移除重复元素，并返回一个正向迭代器作为新序列的结束迭代器

  > 并没有真正移除重复元素，而是可能置于序列结尾，最好通过erase()截断
  >
  > ```mark
  > auto end_iter = std::unique(std::begin(words), std::end(words));
  > words.erase(end_iter, std::end(words));
  > std::copy (std::begin (words) , std::end (words) , std::ostream iterator<string> {std::cout, " "});
  > std::cout << std::endl;
  > ```

  ```mark
  //unique作用在字符串上 删除text中连续空格
  string text {"There's no air in spaaaaaace!"};
  text.erase(std::unique(std::begin(text), std::end(text),[](char ch1, char ch2) { return ch1 = ' '&& ch1 = ch2; }), std::end(text));
  std::cout << text << std::endl; // Outputs: There's no air in spaaaaaace!
  ```

- `rotate(begin,newbegin,end)`  返回一个迭代器指向第一个元素

  ![img](http://c.biancheng.net/uploads/allimg/180920/2-1P92010401J32.jpg)

- ```mark
  //返回一个目的序列的输出迭代器指向最后一个被复制元素的下一个位置
  std::rotate_copy(start, std::find(std::begin(words), std::end(words),"five") 			, end_iter, std::back_inserter (words_copy));
  
  //生成的 words_copy 容器默认有 20 个元素
  std::vector<string> words_copy {20}; // vector with 20 default elements
  auto end_copy_iter = std::rotate_copy(start,std::find(std::begin(words), std::end(words), "five"), end_iter, std::begin(words_copy));
  //返回的迭代器可以用来确定 words_copy 的尾部边界
  std::copy (std::begin (words_copy),end_copy_iter, std::ostream_iterator<string>{std::cout," "});
  ```

- `move(begin,end,back_inserter())`

- `swap_ranges(first1,last1,first2)` 交换范围内元素

- 移除算法无法从序列中移除元素，它们只会被重写或者忽略。==移除操作不会改变移除元素序列的元素个数==

  - remove() 可以从它的前两个正向迭代器参数指定的序列中移除和第三个参数相等的对象。基本上每个元素都是通过用它后面的元素覆盖它来实现移除的。它会返回一个指向新的最后一个元素之后的位置的迭代器。

    `samples.erase(std::remove(std::begin(samples), std::end(samples), 0.0), std::end(samples));`

  - remove_copy() 可以将前两个正向迭代器参数指定的序列中的元素复制到第三个参数指定的目的序列中，并忽略和第 4 个参数相等的元素。它返回一个指向最后一个被复制到目的序列的元素的后一个位置的迭代器。序列不能是重叠的。

  - remove_if() 可以从前两个正向迭代器指定的序列中移除能够使作为第三个参数的谓词返回 true 的元素。

  - remove_copy_if() 可以将前两个正向迭代器参数指定的序列中，能够使作为第 4 个参数的谓词返回 true 的元素，复制到第三个参数指定的目的序列中。它返回一个指向最后一个被复制到目的序列的元素的后一个位置的迭代器。序列不能是重叠的。

- `fill(first,last,value);`

- 函数对象的参数是`for_each()`的前两个参数所指定序列中元素的引用，因此它可以直接修改被保存的值

  `generate()`第三个参数是用来定义以下这种形式的函数对象，无法访问序列元素的值

  `T function();// T is a type that can be assigned to an element in the range `

  ```mark
  string chars (30, ' ');// 30 space characters
  char ch {'a'};
  int incr {};
  //捕获ch值传递的方式修改 以引用的方式捕获&incr
  std::generate (std::begin (chars) , std::end (chars), [ch, &incr]
  {
      incr += 3;
      return ch + (incr % 26);})；
  std::cout << chars << std: :endl;
  // chars is: dgjmpsvybehknqtwzcfiloruxadgjm
  ```

- `transform()` 可以将函数应用到序列的元素上，并将这个函数返回的值保存到另一个序列中，它返回的迭代器指向输出序列所保存的最后一个元素的下一个位置。

  第一版本`transform()`函数：一元函数应用到元素序列改变值

  ```mark
  std::vector<double> deg_F; // Empty container
  std::transform(std::begin(deg_C), std::end(deg_C),std::back_inserter(deg_F),[](double temp){ return 32.0 + 9.0* temp/5.0; });
  // Result 69.8 86.9 32 37.76 212
  ```

  第二版本：二元函数

  ```mark
  //计算空间两个点之间的距离
  using Point = std::pair<double, double>; // pair<x,y> defines a point
  
  std::vector<Point> hexagon {{1,2}, {2,1}, {3,1}, {4,2}, {3,3}, {2,3}, {1,2}};
  std::vector<double> segments; // Stores lengths of segments
  std::transform (std::begin (hexagon),std::end(hexagon) — 1, std::begin (hexagon) + 1, std::back_inserter(segments),[](const Points p1, const Points p2){return st d::sqrt((p1.first-p2.first)*(p1.first-p2.first) +(p1.second - p2.second)*(p1.second - p2.second)); });
  ```

- ```mark
  //replace()
  std::deque<int> data {10, -5, 12, -6, 10, 8, -7, 10, 11};
  std::replace(std::begin(data), std::end(data), 10, 99);
  // Result: 99 -5 12 -6 99 8 -7 99 11
  
  //replace_if()
  string password { "This is a good choice !"};
  std::replace_if(std::begin(password), std::end(password),[](char ch){return std::isspace(ch);}, '_');
  //Result:This_is_a_good_choice!
  ```

- 



