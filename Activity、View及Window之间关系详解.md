##Activity、View及Window之间关系详解
	
	View、Window以及Activity主要是用于显示并与用户交互的
	
	View:将View组成成树形结构
		View是显示器里面具体显示的内容.
		View也有生命周期//Activity/Fragment
	Window：Window表示一个窗口
		Window相当于一个容器，里面“盛放”着很多View，这些View是以树状结构组织起来的。------surfaceview
	Activity:（承担了生命周期,任务栈、保存状态等等）
		应用程序的载体,提供用户处理事件的API，维护应用程序的生命周期
	
####View和Window的关系
		(1)View是Window里面用于交互的UI元素。
		(2)Window只attach一个View Tree，当Window需要重绘（如，当View调用invalidate）时，最终转为Window的Surface，Surface被锁住（locked）并返回Canvas对象，此时View拿到Canvas对象来绘制自己。
		(3)当所有View绘制完成后，Surface解锁（unlock），并且post到绘制缓存用于绘制，通过Surface Flinger来组织各个Window，显示最终的整个屏幕。
	
####View和Activity的关系
		(1) 在Activity onCreate方法中初始化了View 的时候, 调用了View 的onFinishInflate()
		(2) 在执行完 Activity的 onResume 方法之后，才真正开始了View的绘制工作：onMeasure --> onSizeChanged --> onLayout --> onDraw
		(3) onMeasure,onSizeChanged,onLayout,onDraw可能由于setVisible或onresume调用多次，而onAttachedToWindow与onDetachedFromWindow在创建与销毁view的过程中只会调用一次

	
####Activity和View和Window的关系
		[1]Activity在onCreate时调用attach方法，在attach方法中会创建window对象。
		[2]window对象创建时并没有创建 DocerView 对象。// View树是已经创建好的实例对象,实际上是surfaceview来绘制
		[3]用户在Activity中调用setContentView,然后调用window的setContentView，这时会检查DecorView是否存在，
			如果不存在则创建DecorView对象，然后把用户自己的View添加到DecorView中。
	






#View的生命周期

>android view有以下14个周期：

	1、onFinishInflate() 当View中所有的子控件均被映射成xml后触发 。

	2、onMeasure( int ,  int ) 确定所有子元素的大小 。

	3、onLayout( boolean ,  int ,  int ,  int ,  int ) 当View分配所有的子元素的大小和位置时触发 。

	4、onSizeChanged( int ,  int ,  int ,  int ) 当view的大小发生变化时触发  。

	5、onDraw(Canvas) view渲染内容的细节。

	6、onKeyDown( int , KeyEvent) 有按键按下后触发  。

	7、onKeyUp( int , KeyEvent) 有按键按下后弹起时触发  。

	8、onTrackballEvent(MotionEvent)轨迹球事件 。

	9、onTouchEvent(MotionEvent) 触屏事件  。

	10、onFocusChanged( boolean ,  int , Rect) 当View获取或失去焦点时触发  。

	11、onWindowFocusChanged( boolean ) 当窗口包含的view获取或失去焦点时触发  。

	12、onAttachedToWindow() 当view被附着到一个窗口时触发  。

	13、onDetachedFromWindow() 当view离开附着的窗口时触发，该方法和  onAttachedToWindow() 是相反的。

	14、onWindowVisibilityChanged( int ) 当窗口中包含的可见的view发生变化时触发。

>和View的3个关联
>
	View的生命周期是与 Activity/Fragment 相关的
	View的生命周期跟它的可见性 Visibility 相关
	View的生命周期跟它是xml加载还是代码方式相关


##View和Activity的关系
>1.生命周期 [从左到右, 从上到下]
>​		
		  Activity					View
		onCreate()-------------onFinishInflate()
		onStart()	
		onResume()	
		onPostResume()--------onAttachedToWindow()
		onMeasure()
		onSizeChanged()
		onLayout()
		onDraw()

>2.事件相关 [从左到右]
>
	事件					 Activity			View
	按下锁屏键			onPause()	 	onSaveInstanceState()
	当重新回到页面的时候	onRestart()...	没有任何生命周期活动 // ?这个有待考证
	点击回退的时候	...onDestroy()		onDetachedFromWindow()

>3.原则
>
	(1) 在Activity onCreate方法中初始化了View 的时候, 调用了View 的onFinishInflate()
	(2) 在执行完 Activity的 onResume 方法之后，才真正开始了View的绘制工作：onMeasure --> onSizeChanged --> onLayout --> onDraw
	(3) onMeasure,onSizeChanged,onLayout,onDraw可能由于setVisible或onresume调用多次，而onAttachedToWindow与onDetachedFromWindow在创建与销毁view的过程中只会调用一次
