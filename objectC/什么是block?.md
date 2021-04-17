# 什么是block?
block是函数其实上下文封装的对象.因为含有isa指针所以可以理解为对象.block的调用就是函数的调用.

#### block截获变量的特性是怎样的.
block对于不同类型的变量截获的方式是不同的,
局部变量 基本数据类型,直接截获其值.对于对象类型的局部变量连同所有权修饰符一起被截获.关于局部静态变量的截获是以指针的形式进行截获的,关于全局静态变量和静态全局变量是不进行截获的.

#### 什么时候会使用__block修饰符?
一般情况下对被截获变量进行赋值操作时需要添加__block,局部变量,不论是基本数据类型还是对象类型.对于其他类型不需要添加.
__block修饰的变量最终变成了对象,基本数据类型也会变成对象.
数据结构:isa,__forwarding等


#### 在何时需要对block需要进行copy操作,就是block用什么关键字修饰,为什么要用这个关键字修饰?
首先block分为全局类型的block,栈block,堆block.如果我们使用copy操作,栈block会被拷贝到堆上,堆block增加引用计数.全局block什么都不做.

#### __forwarding指针是什么,存在的意义?
进行__block截获操作时,局部变量中会变成对象内含有__forwarding,__forwarding指针指向自己,不论block在任何内存位置我们都可以访问同一个__block变量


#### 为什么block会出现循环引用?
block截获变量会对其所有权修饰符一起截获.产生了强引用关系.__block可能会在arc下产生循环引用,通过断环的方式解除循环引用


# 多线程:
#### iOS中为我们提供了哪些多线程解决方案呢?,他们的特点是什么?
1. GCD,简单多线程利用,c语言层级
2. NSOperation,OC层级,可以控制任务状态,最大并发数以及任务依赖.
3. NSThread:常驻线程的实现.

#### 同步,异步?串行,并发?
主队列,串行队列,全局队列.
GCD中子线程中默认不创建runloop.

** 同步方式:不具备开启线程的能力,只能在当前线程中执行其他任务.**
** 异步方式:具备开启线程的能力.可以在其他线程执行任务. **
** 串行执行:一个任务执行完毕才能执行执行下一个任务. **
** 并发执行:可以同时执行多个任务. ** 


#### dispatch _barrier_async()异步栅栏调用?多读单写.
1. dispatch_sync()并发读取数据,
2. dispatch _barrier_async()异步到并发队列中写数据添加围栏.
3. dispatch_group_async()?abc先执行完毕再执行d.

#### GCD怎么控制任务依赖?
通过组队列的方式控制(通过添加到不同的组队列中).

#### GCD怎么控制线程最大并发数?
通过信号量进行线程阻塞.


#### 多个任务执行完毕之后才开启下一个任务.
1. 创建组group,创建并发队列queue.
2. 异步并发执行任务在组中,dispatch_group_async()
3. 监听group通知.执行完成后在主队列执行.


##### NSOperation有哪些优势特点:
1. 可以添加任务依赖
2. 任务执行状态的控制
3. 最大并发量.


#### NSThread:常驻线程(结合Runloop)
NSThread启动流程中会调用[taget performSelector:selector];
常驻线程的移除:
1. 不能使用线程退出
2. 不能使用port,timer,abserver移除
需要使用CFRunLoopGetCurrent()函数退出内层循环.




#### 多线程和锁:使用过哪些琐
1. @synchronize
	`创建单例对象,保证多线程情况下唯一`
2. atmic
	`对被修饰对象保证赋值的线程安全,不保证使用时安全.`
3. OSSpinLock
	`循环等待询问,不释放当前资源,通过runtime分析系统源码,引用计数操作有使用到.`
4. NSLock
	`NSLock加锁后,再次获取锁导致死锁.我们可以通过递归锁解决问题.`
5. NSRecursiveLock
	`递归锁.特点是可以重入.`
6. dispatch_semaphore_t
	`信号量.`
7. NSConditionLock
    `条件锁`
8. NSCondition 
	`线程锁`


#### 扩展:互斥锁,自旋锁,悲观锁,乐观锁,读写锁:
1. 互斥锁:独占的锁,没有释放锁
2. 自旋锁:忙等的锁,直到锁释放才会继续往下执行.OSSpinkLock
3. 悲观锁:大多数使用到的是悲观锁.
4. 乐观锁:在线的文档编辑,多线程访问数据冲突小,或者可能不会发生多线程的访问.使用乐观锁.然后通过服务器对文档加版本号.判断是否是多线程操作再来进行事件处理.
5. 读写锁:队列访问.

#### dispatch_once实现原理:
dispatch_once用原子性操作block执行完成标记位，同时用信号量确保只有一个线程执行block，等block执行完再唤醒所有等待中的线程。

#### amtoic不可靠的原因:
只是对属性的setter和getter方法进行加锁处理,只能保证属性的赋值和写入的安全,不能保证操作的安全.


#### 面试注意:
1. 串行队列,直接执行同步任务不会造成线程死锁.
2. 串行队列,先异步执行任务,再同步执行其他任务.此情况会造成死锁.
3. 在主队列上执行同步任务会造成线程死锁,原因是队列引起的循环等待,虽然主队列是特殊的串行队列但是是在主线程上执行.比如说一个viewDidLoad任务,再次同步提交串行队列任务到主线程.任务一个执行完毕以后才会执行下一个.从而造成循环等待
4. 异步任务访问数据时要注意一个线程的安全访问数据问题.


# RunLoop
### 什么是Runloop?以及实现机制是怎样的?
RunLoop是通过内部维护事件循环来对事件/消息进行管理的一个对象.
没有消息休眠,有消息唤醒.
系统默认在主线程开启RunLoop.Runloop非死循环发生了状态切换.

#### RunLoop的数据结构:
```
CFRunLoop源码分析:数据结构
CFRunLoop,
CFRunLoopMode,
Source/Timer/Observe
```

#### CFRunLoop

```
CFRunLoop:
pthread(一一对应),
currentMode(CFRunLoopMode)当前模式,
modes(NSMutableSet <CFRunLoopMode*>),
commonModes(NSMutableSet <NSString*>),
commonModeItems(集合,多个observer,timer,source).
```

#### CFRunLoopMode
```
CFRunLoopMode:
name(NSDefaultRunLoopMode),
sources0(NSMutableSet *),
sources1(NSMutableSet *),
observers(NSMutableArray *),
timers(NSMutableArray *)
```
#### CFRunLoopSource
```
CFRunLoopSource:
source0,source1
CFRunLoopTimer:
CFRunLoopObserve:entry(入口),beforeTimer(将要处理定时器),beforeSource(通知处理source),进入活跃状态,进入休眠状态.

```

#### CommonMode有什么作用,是否用过?
common不是实际存在的mode,它是同步source/timer/observer到多个mode中的一种技术解决方案.

#### RunLoop为什么会有多个mode?滑动taleView时banner不会滚动?怎么解决?
RunLoop的事件循环机制?
	1. 发送通知即将开始runloop
	2. 将要处理timer/source0事件
	3. 处理source0事件,如果有source1事件要处理.处理消息.如果没有事件需要处理进行休眠同时发送通知等待再次被唤醒.
	唤醒的方式有三种:1. source1事件,2.timer事件,3.外部手动唤醒

#### 我们程序从开始点击到杀死是怎样的?
调用main函数后会调用UIApplicationMain,开启RunLoop.

#### 一般我们常用的mode有哪些,有什么作用和特点.
UITrackingRunLoopMode(触摸点击模式),kcpRunLoopDefaultMode(默认模式).通过CFRunloopAddTimer到commonModes上面函数同步添加到多个mode上.


#### Runloop与多线程:
一一对应,通过对数据结构的了解.默认情况下没有创建.
#### 怎样实现常驻线程?有什么作用?
	1. 为当前线程开启一个Runloop(通过runloopGetCurrent没有就会创建)
	2. 向该Runloop中添加Port/source等维护事件循环
	3. 启动该Runloop.



