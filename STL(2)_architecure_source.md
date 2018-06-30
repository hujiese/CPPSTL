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