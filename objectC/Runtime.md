# Runtime
数据结构:
关键词:
`
objc_object,objc_class,isa,cache_t,class_data_bits_t,IMP,class_rw_t,class_ro_t,protocols,properties,methods,
methods_t.SEL name,const char* types,IMP imp;
`

- 数据结构:
`
objc_object结构体
isa_t共用体
isa操作相关
弱引用相关
关联对象相关
内存管理相关
`

- Class类是否是对象呢?
继承自objc_object,所以是对象.


- isa指针是什么含义:
isa指针分为指针类型的isa和非指针类型的isa
类对象和元类对象.


methods_t:


- 什么是类对象?什么是元类对象?有什么区别?
类对象储存实例方法列表信息,元类对象储存类方法等信息.
任何一个元类对象的isa指针指向根元类对象,根元类对象的superClass指针指向根类对象,根类对象的superClass指针指向nil.
对象,类对象,元类对象,根类对象,根元类对象.

- 缓存查找:哈希查找,通过给定的key经过hash函数算法算出的值找到位置.

### 消息传递及消息转发.
1.  消息传递?
	1. 查找缓存是否命中,哈希查找
	2. 未命中当前类方法列表是否命中.排序好的二分,未排序的一般遍历.
	3. 逐级父类方法列表是否命中?
	4. 未命中走转发流程.
2. 消息转发流程?
	1. resolveInstanceMethod:返回YES,
	2. forwardingTargetSelector:返回转发目标
	3. methodSignatureForSelector:返回方法签名
	4. forwardInvocation:

- 方法交换,如何为一个类运行时动态添加方法,动态方法解析.
@dynamic.动态运行时语言将函数决议推迟到运行时.运行时生成getter和setter方法,非编译时.

###  内存布局
`stack(栈:方法调用),heap(堆:象),.bss(未初始化全局变量),.dada(已初始化全局变量),.text(代码块)`

### 内存管理?iOS系统是怎样对内存进行管理的.
1. TaggedPointer: NSNumber
2. NONPOINTER_ISA: 非指针类型isa
2. 散列表:其中包含弱引用表和引用计数表

#### 散列表
```
sideTables():哈希表
sideTable:spinlock_t(自旋锁忙等的锁),weak_table_t(弱引用表),RefcountMap(引用计数表).
为什么哈希查找效率高?本质原因因为插入和获取都是直接通过同一个哈希算法得到的,不用进行遍历.
RefcountMap(引用计数表,哈希表),哈希查找,找到对象的引用计数值.
weak_table_t(弱引用表,哈希表),找到弱引用对象的储存位置.

```

#### alloc怎么实现的?
#### retain怎么实现的?
#### release怎么实现的?
#### retainCount怎么实现的?
#### dealloc怎么实现的?


#### 弱引用管理?一个弱引用变量是怎么被添加到弱引用表当中的?

一个呗声明为__weak的弱引用指针经过编译会调用objc_initWeak()函数,经过一系列调用,最终在weak_register_no_lock()函数当中进行弱引用变量的添加,具体添加的位置是通过哈希算法来进行位置查找,如果我们查找的对象已经有了对应的的弱引用数组,我们就把新的添加上去,如果没有就重新创建一个将第零个位置添加上最新的weak指针.


#### 为什么weak指针修饰的变量会自动置为nil?
在一个对象被销毁时,系统经过一系列内部调用会调用消除弱引用表的函数,内部的代码实现原理是通过哈希查找找到当前对象在sidetables中的位置.获取弱引用数组,循环遍历全部置为nil.

