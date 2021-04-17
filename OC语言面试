# OC语言面试:
- 什么是分类?用分类做了什么事?分类的实现原理?
声明私有方法,分解类,framework私有方法公开化.
- 分类可以做哪些事情?
实例方法,类方法,协议,属性(只声明set和get方法,并没有添加实例变量)
运行时决议.一些特点.

- 分类的实现逻辑?两个分类有同名方法最终哪个会生效?分类和主类用同名方法,会实现哪一个?
最后编译的分类方法最终生效,分类会覆盖主类.(倒序遍历添加方法,根据源码分类方法会添加到方法列表前面,会优先实现返回)

- 关联对象:
objc_set.
objc_get.

- 关联对象的本质:哈希表map值,统一全局容器.


- 什么是扩展?用扩展做了什么事?
声明私有属性,声明私有成员变量.

** 总结:扩展简单,编译时决议,一般都用于声明属性和成员变量,可以为系统类添加分类但是不能添加扩展 **

- 代理:Delegate
软件设计模式代理模式,@protocal.传递1对1.
optional可选实现
- 通知:NSNotification,使用观察者模式,用于跨层传递消息.
通知的大致实现原理:
通知中心维护一个map表.通知名key包含,value是observeList

- KVO:观察者设计模式的实现,apple采用isa-swizzling混写技术实现
原理:
当我们addOvserverValueForkeypath观察一个类,系统会在运行时动态创建此类的子类,并将isa指针指向子类,重写setter方法.willChangeValueForkey,调用父类实现,didChangeValueForKey.
手动kvo是怎么样的?手动调用willChangeValueForkey和didChangeValueForKey.

- KVC:
键值编码技术,破坏面向编程思想.
setValue: forKey
valueForKey:
1. 通过key访问的实例变量,1. 是否有相应的get方法,2. 是否存在实例变量,3. 系统会调用NSUndefinedKey抛出异常.

- 属性关键字:
atomic赋值和获取保证安全,操作不保证安全.
assgin和weak区别:
assgin可以用来修饰基本数据类型,不改变被修饰对象的引用计数,会产生悬垂指针问题.
weak不改变被修饰对象的引用计数,所指对象被释放之后会自动置为nil.

- 深浅拷贝:
浅拷贝增加引用计数,指向同一个内存地址
深拷贝:内存地址发生改变.
mutableCopy就是内存地址增加.
NSMutableArray用copy修饰:要分析赋值的是可变还是不可变.都变成不可变对象,调用方法就会报错.

## 基本类的数据结构
- objc_object:
```
objc_object:
isa_t,
isa相关操作主要是isa指针指向,
实例对象的isa指针指向类对象,
类对象的isa指针指向元类对象,
引用计数相关方法,
弱引用相关方法,
内存管理的相关方法.
```

- objc_class
```
objc_class:继承自objc_object
superClass:指向其类对象的superClass指针指向其父类,元类对象的superClass指针指向跟元类对象.跟元类对象的superClass指针指向跟类,根类superClass指针指向nil
cache_t:bucket_t是可增量的哈希表 key->IMP
class_data_bits_t:class_rw_t的封装
```

- class_rw_t:
```
class_rw_t:
class_ro_t:
[protols],[propreties],[methods]二维数组,因为内部含有分类的相关方法实现
```


- class_ro_t:
```
class_ro_t:
name:类名
protols,propreties,methods.ivas组成,一位数组
```

- CFRunLoop:
```
CFRunLoop的数据结构:
	pthread,currentMode<CFRunLoopMode *>,modes(集合),commonModes<[nsstring *]>,commonModeItems(包含多个timer,source.observer)
CFRunLoopMode的数据结构:
	name,sources0,sources1,observers,timers
CFRunLoopSource:
	source0,source1具备唤醒线程的能力
CFRunLoopTimer:
	NSTimer进行转化
CFRunLoopObserver:
	观测时间点
	enter:入口时机准备启动
	beforeTimers:通知观察者当前runloop对timer一些事件进行处理
	beforeSources:通知观察者当前runloop对sources一些事件进行处理
	beforeWaiting:通知观察者当前runloop将要进行休眠状态
	afterWaiting:切换到用户态
	exit:通知观察者runloop退出

```
