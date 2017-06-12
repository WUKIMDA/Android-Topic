##内存溢出的原因:(OOM: Out Of Memory)
	内存占有量超过了VM所分配的最大.
	不管android设备总内存是多大, 都只给每个app分配一定内存大小, 16M, 一旦超出16M就内存溢出了//google原生OS的默认值是16M.

	出现原因一般有: 
		加载对象过大,相应资源过多，来不及释放.
		或者一直被某个或某些实例所持有却不再被使用导致GC不能回收。
		数据库Cursor游标没有关闭.
		ListView等列表没使用ViewHolder.
		未取消注册广播接收器.
		IO流未及时关闭.
		Bitmap使用后未调用recycle()释放资源.
		于线程的生命周期不可控导致内存泄露.
		内部类和外部模块的引用.
		监听器.		
		各种连接.
		单例模式的不正确使用(持有外部引用).(生命周期长的对象拥有生命周期短的对象时容易引发内存泄漏)
		集合类的缓存泄漏.
		传感器管理不使用时没有注销监听器.
		

	处理方式:
		尽早释放无用对象的引用---置null.
		字符串处理时，尽量避免使用String，而应使用StringBuffer.
		在内存引用上做些处理:软引用、强化引用、弱引用.
		内存中加载图片时直接在内存中作处理，如边界压缩.
		动态回收内存.
		优化Dalvik虚拟机的堆内存分配.
		突破堆内存大小.
		Context上下文使用Application的
		尽量避免使用 static 成员变量.
		将内部类改为静态内部类,静态内部类中使用弱引用来引用外部类的成员变量.
		尽量运用对象池技术以提高系统性能.


##内存泄露与内存溢出的区别
	内存溢出(满了)
		就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。
	内存泄漏(迟早用完)
		就是没有及时清理内存垃圾，导致系统无法再给你提供内存资源（大量的内存泄露会导致内存溢出）。
		最简单的例子是死循环


##关于Context传入的生命周期
	1、如果此时传入的是 Application 的 Context，因为 Application 的生命周期就是整个应用的生命周期，所以这将没有任何问题。

	2、如果此时传入的是 Activity 的 Context，当这个 Context 所对应的 Activity 退出时，由于该 Context 的引用被单例对象所持有，其生命周期等于整个应用程序的生命周期，所以当前 Activity 退出时它的内存并不会被回收，这就造成泄漏了。

​	
##引用和垃圾回收器(内存中对象的引用) //面试加分
	垃圾回收器只回收空引用,而且不及时.

	[1]强引用(默认), 垃圾回收器不会回收
	[2]软引用SoftReference, 垃圾回收器会考虑回收 //常用
	[3]弱引用WeakReference, 垃圾回收器更会考虑回收 
	[4]虚引用PhantomReference, 垃圾回收器最优先回收 
	缺点:
		Android2.3之后更倾向于回收[软引用和弱引用的对象]


###手机app分配内存上限值获取和突破
	获取Android系统默认给每个app分配的内存上限
	ActivityManager activityManager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
	int memorySize = activityManager.getMemoryClass();

	Android4.0以后，可以通过在application节点中设置属性Android:largeHeap=”true”来突破这个上限。



###图片的缓存和软/弱应用的结合
	由于图片占用内存空间比较大，缓存很多图片需要很多的内存，就可能比较容易发生OutOfMemory异常。这时，我们可以考虑使用软/弱引用技术来避免这个问题发生。
	缺点:
		Android2.3之后更倾向于回收[软引用和弱引用的对象]


##关于finalize()方法释放
	被执行的时间不确定，不能依赖与它来释放紧缺的资源。时间不确定的原因是：
        虚拟机调用GC的时间不确定
        Finalize daemon线程被调度到的时间不确定


#Handler内存泄漏分析及解决

###二、分析

1、 Android角度

	当Android应用程序启动时，framework会为该应用程序的主线程创建一个Looper对象。这个Looper对象包含一个简单的消息队列Message Queue，并且能够循环的处理队列中的消息。这些消息包括大多数应用程序framework事件，例如Activity生命周期方法调用、button点击等，这些消息都会被添加到消息队列中并被逐个处理。
	
	另外，主线程的Looper对象会伴随该应用程序的整个生命周期。
	
	然后，当主线程里，实例化一个Handler对象后，它就会自动与主线程Looper的消息队列关联起来。所有发送到消息队列的消息Message都会拥有一个对Handler的引用，所以当Looper来处理消息时，会据此回调[Handler#handleMessage(Message)]方法来处理消息。

2、 Java角度

	在java里，非静态内部类 和 匿名类 都会潜在的引用它们所属的外部类。但是，静态内部类却不会。





###Handler 造成的内存泄漏
原因:

	 如果Handler 发送的 Message 尚未被处理，则该 Message 及发送它的 Handler 对象将被线程 MessageQueue 一直持有。由于 Handler 属于 TLS(Thread Local Storage) 变量, 生命周期和 Activity 是不一致的。因此这种实现方式一般很难保证跟 View 或者 Activity 的生命周期保持一致，故很容易导致无法正确释放。

修复方式:
	避免使用非静态的内部类。
	最好的做法是，使用静态内部类，然后在该类里使用弱引用来指向所在的Activity。
	比如上面我们将 Handler 声明为静态的，则其存活期跟 Activity 的生命周期就无关了。同时通过弱引用的方式引入 Activity，避免直接将 Activity 作为 context 传进去.

代码:

	 private static class MyHandler extends Handler {
	    private final WeakReference<SampleActivity> mActivity;//WeakReference弱引用
	
	    public MyHandler(SampleActivity activity) {
	      mActivity = new WeakReference<SampleActivity>(activity);
	    }
	
	    @Override
	    public void handleMessage(Message msg) {
	      SampleActivity activity = mActivity.get();
	      if (activity != null) {
	        // ...
	      }
	    }
	  }