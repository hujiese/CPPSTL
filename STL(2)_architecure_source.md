## 1. OOP (面向对象编程) vs. GP (泛型编程）##
![](https://i.imgur.com/FbTMn26.png)
![](https://i.imgur.com/Ii6mQh5.png)
![](https://i.imgur.com/LVmL0Ae.png)
![](https://i.imgur.com/v5dcEWX.png)

## 2. 技术基础：操作符重载and模板(泛化, 全特化, 偏特化) ##
![](https://i.imgur.com/betE3Ng.png)
![](https://i.imgur.com/ct0wTEj.png)
![](https://i.imgur.com/RrFu2Vf.png)
![](https://i.imgur.com/Ja0tzLp.png)
![](https://i.imgur.com/8Ft3TJh.png)
![](https://i.imgur.com/iHXX17H.png)
![](https://i.imgur.com/X4yLQxb.png)
![](https://i.imgur.com/WVMHDfH.png)
![](https://i.imgur.com/u2iJDBa.png)
![](https://i.imgur.com/OzPkEed.png)

## 3. 探索容器底层的allocator ##
### （1）new函数的底层实现 ##

![](https://i.imgur.com/U157wo4.png)

从上图可以看到，vc98编译器和CB5编译器在底层对new的实现是通过运算符重载来完成的，底层是使用malloc函数进行内存分配。

### （2）VC6.0 STL的allocator的使用 ###

![](https://i.imgur.com/2aFYo54.png)

上图可以看出，VC6.0 STL容器模板参数中都默认使用了allocator，下面可以看看这个allocator的内部细节：
![](https://i.imgur.com/5h0Qima.png)
从上面可以看到，allocator是一个模板类，其中有两个很核心的函数allocate和deallocate，allocate函数调用_Allocate函数完成内存的申请，其底层使用了重载后的new操作符，所以底层还是使用了malloc函数。deallocate则释放内存。上图还演示了如何自己使用allocator申请内存，但不建议这样使用。所以，结论是：VC6.0对alloator本质是对malloc函数的封装。

### （2）BC5 STL的allocator的使用 ###
![](https://i.imgur.com/3866E2F.png)
![](https://i.imgur.com/2mi61Fl.png)
结论：和VC6.0类似，BC5的allocator也只是是对malloc函数的封装。

### （3）GCC2.9 STL的allocator的使用 ###
![](https://i.imgur.com/JrFi82d.png)
上图的allocator虽然在G2.9标准库中，但实际并没有使用该库的allocator，下面才是G2.9真正的allocator实现：
![](https://i.imgur.com/EvA9KLv.png)
下面看看STL的模板默认参数Alloc类的内部原理：
![](https://i.imgur.com/O9MuS6P.png)

为了减小开销，采用链表的方式来实现allocator，至于为什么这样设计很好，参见侯捷老师的内存分配专题，这里只是了解而已。

### （4）GCC4.9 STL的allocator的使用 ###
![](https://i.imgur.com/upOdeLg.png)
![](https://i.imgur.com/tMPYo8D.png)
结论：G4.9的allocator也只是是对malloc函数的封装，并没有延续G2.9版本的所谓先进的设计。
但是，G2.9版本的allocator还是被收录在B4.9的ext扩展文件中：
![](https://i.imgur.com/6O2PfS6.png)
![](https://i.imgur.com/Ai0VFvK.png)

## 4. 容器结构与分类 ##
![](https://i.imgur.com/Vn82gwp.png)
![](https://i.imgur.com/5x248sO.png)
![](https://i.imgur.com/5kX4gOT.jpg)

## 5. 探索list ##
![](https://i.imgur.com/us3FJ1u.png)
从上图可以看到，STL的list结构是一个循环双向链表，list类模板里有两个较为重要的成员，一个是link\_type类型的node，一个是\_\_list\_iterator<T, T&, T\*\>类型的iterator，其中link\_type的本质是\_\_list\_node\*结构，此结构体详见上图。下面将分析\_\_list\_iterator结构：
![](https://i.imgur.com/YibdMtH.png)

\_\_list\_iterator是一个重载了多种操作符的智能指针：
![](https://i.imgur.com/Hk0w7PY.png)

值得注意的是iterator的前置和后置++操作。C++11标准为了标识后置++采取了operator++\(int\)方式。后置++操作后先获取node节点\(\_\_list\_node\*指针类型\)的下一个节点地址，然后将该地址转化为\_\_list\_node\*指针类型，最后返回*this的引用。总体上看就是该迭代器指针指向了传入链表节点的下一个节点。后置++操作先保存该迭代器到tmp中，然后调用前置++，并返回临时变量tmp，返回时由于不是返回引用故调用了迭代器的拷贝构造函数。这里不能返回引用，主要是为了避免后置连续++，例如i++++，这在C++中是不允许的。这个迭代器的设计真的十分精妙。
![](https://i.imgur.com/T4AxY04.png)
关于重载\*和->运算符，重载\*运算符相当于返回节点的data域,此时返回的是引用，然后假设data中有method\()何field，此时就可以利用data.method()和data.field来调用属性和方法，也就是说(\*it)最后返回的是node的data成员，(\*it) == data。

重载->运算符将调用operator*()函数，先返回data对象的引用，然后取data的地址并返回一个指向data地址的指针，所以it->method()等价于\(&(\*it))->method()，即通过data的地址（this指针）来访问data的方法和属性。

下面是GCC2.9和GCC4.9 STL中list的一些区别：
![](https://i.imgur.com/sffV4LF.png)
![](https://i.imgur.com/NAFeW1g.png)

从上面可见，G4.9较G2.9\_\_list\_iterator<>泛型模板列表只有一个元素_Tp，更加简洁，但从整个list容器设计来看，结构其实更加复杂。

## 6. Iterator需要遵循的原则 ##
![](https://i.imgur.com/HMDznBe.png)
迭代器是沟通容器和算法的桥梁，但是一个算法需要迭代器提供iterator_category、difference_type和value_type（reference和pointer没用到），所以一个迭代器中需要提供这五种类型：
![](https://i.imgur.com/z7fvMk3.png)
但是iterator有可能不是一个类，只是普通的指针，所以需要判断迭代器的类型，于是：
![](https://i.imgur.com/axh2xTb.png)
STL使用Traits来分离class iterators和no-class iterators：
![](https://i.imgur.com/jPghisf.png)
![](https://i.imgur.com/OhiBvZk.png)
![](https://i.imgur.com/abSASK2.png)


## 7. 探究vector ##
vector的内部实现很像数组：
![](https://i.imgur.com/n596H8P.png)

当vector插入元素发现空间不够用时，将采取两倍扩容的方法：
![](https://i.imgur.com/xFyetDb.png)
![](https://i.imgur.com/jifv9Cv.png)

GCC2.9版本的vector内部迭代器是普通指针，而非智能指针（类）：
![](https://i.imgur.com/fFNCcRb.png)

GCC4.9版本的vector：
![](https://i.imgur.com/d9nOW6b.png)
![](https://i.imgur.com/ve4C72a.png)
![](https://i.imgur.com/PiEir8Q.png)

4.9版本的vector变动很大，更加复杂和冗余。

## 8. 探究array ##
array是固定长度的数组，其内部的iteraor是普通指针，非智能指针:
![](https://i.imgur.com/9uvOfAy.png)

GCC4.9版本的array：
![](https://i.imgur.com/RuVxSN3.png)
更加复杂和冗余。

## 8. 探究forward_list ##
forward_list为单向链表，分析方法和list类似：
![](https://i.imgur.com/Jp9064h.png)

## 9. 探究deque ##
![](https://i.imgur.com/k4BgJeK.png)
![](https://i.imgur.com/MmHLOKG.png)
![](https://i.imgur.com/tI3JcaI.png)
![](https://i.imgur.com/jkLPV6L.png)
![](https://i.imgur.com/FIKwnsn.png)
![](https://i.imgur.com/3sop6iB.png)
![](https://i.imgur.com/HyyNxPW.png)
![](https://i.imgur.com/ZZgE9oO.png)
![](https://i.imgur.com/ydk7XYy.png)

GCC4.9版本的deque：

![](https://i.imgur.com/4JnzTL8.png)
![](https://i.imgur.com/O96xrPx.png)

## 10. 探究queue和stack ##
![](https://i.imgur.com/BfBIZR6.png)
![](https://i.imgur.com/LaiHWR1.png)
![](https://i.imgur.com/R8ti6RE.png)
![](https://i.imgur.com/JoSpGIt.png)
![](https://i.imgur.com/6iiNFGQ.png)

## 11. 红黑树 ##
![](https://i.imgur.com/0ijtX4w.png)
![](https://i.imgur.com/R56kXul.png)
![](https://i.imgur.com/fr3dvVQ.png)
![](https://i.imgur.com/UDDtryy.png)
![](https://i.imgur.com/u6GlPVX.png)
![](https://i.imgur.com/co1hdP3.png)
![](https://i.imgur.com/WqIWkNs.png)

## 12. 探究set/multiset
![](https://i.imgur.com/jOQEkid.png)
![](https://i.imgur.com/QwqR0De.png)
![](https://i.imgur.com/R0xmirY.png)
![](https://i.imgur.com/TRqBJM8.png)

## 13. 探究map/multimap ##
![](https://i.imgur.com/vmF5b8k.png)
![](https://i.imgur.com/BZHf7Oh.png)
![](https://i.imgur.com/qLocCxm.png)
![](https://i.imgur.com/TwJr0HI.png)
![](https://i.imgur.com/lFjQNnd.png)
![](https://i.imgur.com/KO5UKd3.png)

## 14. hashtable ##
![](https://i.imgur.com/X3QLlAR.png)
![](https://i.imgur.com/i587xKS.png)
![](https://i.imgur.com/1AjaVrI.png)
![](https://i.imgur.com/1LqNlU3.png)
![](https://i.imgur.com/cf7U2bb.png)
![](https://i.imgur.com/zlHN9bh.png)
![](https://i.imgur.com/8hBXqpA.png)
![](https://i.imgur.com/ZtRKcf7.png)
![](https://i.imgur.com/k9cmyOg.png)
![](https://i.imgur.com/szQKlkQ.png)

## 15. unordered容器 ##
![](https://i.imgur.com/unC0HMv.png)
![](https://i.imgur.com/cIYMJM0.png)
![](https://i.imgur.com/FxrxaXW.png)