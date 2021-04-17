### UI

1. UITableView优化相关
	1. 重用机制.
		重用池池中取出.直接复用.
	2. 数据源同步.
		并发同步,串行访问.
2. UIView和CALayer之间的关系:
	1. UIView负责内容以及事件相关
	2. CALayer负责内容显示
	单一职责原则.
3. 事件传递机制
	1. hitTest_WithEvent,point_WithEvent
	传递事件机制:
	点击屏幕->UIApplication->UIWindow->hit方法判断是否响应->pointInSide方法->是否在父控件内->父视图倒序遍历子视图.递归查找直到找到相应的子视图相应hit方法.
	hit方法内部实现:
		1. 判断hidden,userinterface,alpha是否小于0.01.
		重写hit方法可以自定义点击区域.
4. 视图事件响应.
	1. 逐级像父视图传递.到UIViewController到window再到appDelegate.



- 图像的显示原理.
CPU+GPU
CPU
layout(布局和计算),display(drerect绘制方法),prepare,commit.
GPU
顶点着色,光栅化等.
控件流程:
UIView->CALayer->contents->CoreAnimation->openGL->screen.


- 卡顿掉帧的原因:
60fps,流畅效果,16.7ms产生一张图.
在规定的16.7ms之内,下一帧vsync信号到来之前,没有完整一帧画面的合成.所以卡顿和掉帧.

- 滑动优化方案:基于CPU,
基于CPU,
1. 对于对象的创建销毁放在子线程
2. 预排版在子线程中进行.
3. 预渲染(文本异步绘制,图片编解码等)
基于GPU避免离屏渲染,图层的圆角,阴影等设置就会触发或者进行预渲染.

- UIView的绘制原理:
当我们调用UIView的setNeedDisplay方法,系统会调用他的layer的同名方法,在当前runloop将要结束的时候调用当前视图的CALayer display方法.
[CALayer display]方法内部实现中首先会判断layer的delegate是否响应displayLayer:方法,如果响应系统给异步绘制留了入口,不响应走系统的绘制流程

- 系统的绘制流程:
CALyer创建位图上下文,判断是否有代理,没有代理调用layer的drawInContext方,有代理调用layer的代理的drawLayerInContext方法,然后回调给[UIView drawRect]方法.

- 如何实现异步绘制:
layer的delegate如果实现了displayLayer:方法就可以进行后续的异步绘制的流程,在子线程中创建位图上下文CreatBitMap,绘制控件,CreatImage生成CGImage图片.然后回到主队列中设置layer的contents属性.

- 离屏渲染?
GPU在当前屏幕缓冲区意外新开辟了一个缓冲区进行渲染操作.
当我们指定视图的某些属性标记的在未合成之前不能用于显示的时候,就会触发离屏渲染.

- 什么场景下触发离屏渲染?为什么要避免?
圆角,图层蒙版,阴影设置,光栅化.增加了GPU的工作量使总耗时超出了16.7ms.就产生卡顿的感觉.


- NSArray,NSDictory,NSSet的数据结构是怎么样的?有什么区别?
普通的c语言数组,本质是对象,采用了环形缓冲区(当我们插入元素时,会根据最少移动内存指针的方式插入)的结构.
NSDictionary其实就是一个散列表,通过哈希算法能查找到具体的index.
NSSet本质也是散列表,是无序的.


- UItableViewCell怎么实现自己的重用机制?
	1. 创建成员变量,一个等待使用的队列,一个使用中的队列,以集合的方式实现.
	2. dequeueResuerView
		从等待中的队列中取出view,同时移除等待使用队列中的view,添加到已使用中的队列.
	3. addView:向重用池中添加视图
		将view添加到使用中的队列
	4. 重置方法
		遍历正在使用中的队列中的view移除,并且添加等待使用的队列中.


- 面试:load和initialize?
load当类被引用进项目时会调用
initialize当此类第一个方法被调用时调用.

