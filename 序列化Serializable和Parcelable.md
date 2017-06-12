##Activity之间传递对象/对象集合==>Serializable接口/Parcelable接口

- 为什么要了解序列化？
>
	—— 进行Android开发的时候，无法将对象的引用传给Activities或者Fragments，我们需要将这些对象放到一个Intent或者Bundle里面，然后再传递。

- 什么是序列化
>
	—— 序列化，表示将一个对象转换成可存储或可传输的状态。序列化后的对象可以在网络上进行传输，也可以存储到本地。

- 怎么通过序列化传输对象？
>
	方式1:需要传递的对象实现Serializable接口(Java自带)
	方式2:需要传递的对象实现Parcelable接口(android 专用)
>	
	注意事项:需要传递的对象的成员变量都要序列化


####关于Parcelable接口
	Parcelable接口的缺点是使用了反射，序列化的过程较慢。这种机制会在序列化的时候创建许多的临时对象，容易触发垃圾回收。

	Parcelable方式的实现原理是将一个完整的对象进行分解，而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了


####关于Serializable接口
	序列化是将对象分割成一个个属性小块储存到文件中, 然后再读取. 比如游戏的进度储存就是用序列化.

####Serializable接口和Parcelable接口的区别和使用
	传递:( 对象序列化到本地建议用Serializable接口,  如果是应用内传递用Parcelable接口 )
		传递的对象要实现Serializable接口或者Parcelable接口
		=========>然后intent.putExtra(String key,Serializable value)
		或=======>然后intent.putExtra(String key,Parcelable value)
		//Serializable永久序列化----->效率低====>写法简单
		//Parcelable	内存序列化---->内存---->不稳定--->效率高====>写法复制  写法看源码提示复制粘贴
	接收:
		第二个页面getIntent()的时候强转回原来的对象即可.

	如果传递多个对象===>用集合
		Serializable接口===>一样的在接收数据的时候强转回原来的集合对象即可.再做其他操作
		Parcelable接口=====>getParcelableArrayListExtra(name)和.getParcelableArrayExtra(name)方法,  第一个可以直接放回ArrayList集合(不需要强转)		第二个返回数组(不需要强转)

	

