##Activity之间传递对象/对象集合==>Serializable接口/Parcelable接口

- 为什么要了解序列化？(需要传递)
>
	—— 进行Android开发的时候，无法将对象的引用传给Activities或者Fragments，我们需要将这些对象放到一个Intent或者Bundle里面，然后再传递。

- 什么是序列化(把对象变成可以存储和可以传输的状态)
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
	[1]Serializable是给对象打了一个标记，系统会自动将其序列化。//简单
	[2]Parcelable需要在类中添加一个静态成员变量CREATOR，这个变量需要实现 Parcelable.Creator 接口   //复杂

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

	

####Parcelable内存序列化的方法分析
	public interface Parcelable{
		//内容描述接口，基本不用管
		public int describeContents();

		//写入接口函数，打包
		public void writeToParcel(Parcel dest, int flags);

		//读取接口，目的是要从Parcel中构造一个实现了 Parcelable的类的实例处理。因为实现类在这
		里还是不可知的，所以需要用到模板的方式，继承类名通过模板参数传入

		//为了能够实现模板参数的传入，这里定义Creator嵌入接口 , 内含两个接口函数分别返回单个和
		多个继承类实例
		public interface Creator<T>{
			public T createFromParcel(Parcel source);
			public T[] newArray(int size);
		}
	}


####实现Parcelable步骤
	1）implements Parcelable

	2）重写writeToParcel方法，将你的对象序列化为一个Parcel对象，即：将类的数据写入外部提供的Parcel中，打包需要传递的数据到Parcel容器保存，以便从 Parcel容器获取数据

	3）重写describeContents方法，内容接口描述，默认返回0就可以

	4）实例化静态内部对象CREATOR实现接口 Parcelable.Creator
	public static final Parcelable.Creator<T> CREATOR

	具体实现:
		public class MyParcelable implements Parcelable {
			private int mData;
		
			public int describeContents() {
				return 0;
			}
		
			public void writeToParcel(Parcel out, int flags) {
				out.writeInt(mData);
			}
		
			public static final Parcelable.Creator<MyParcelable> CREATOR = new Parcelable.Creator<MyParcelable>() {
				public MyParcelable createFromParcel(Parcel in) {
					return new MyParcelable(in);
				}
		
				public MyParcelable[] newArray(int size) {
					return new MyParcelable[size];
				}
			};
		
			private MyParcelable(Parcel in) {
				mData = in.readInt();
			}
		}