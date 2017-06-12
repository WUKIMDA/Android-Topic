

##Builder建造者模式
	有2种模式:
		经典的Builder模式，变种Builder模式，而现在Android开发普遍使用的是第二种的变种Builder模式

	用于构建复杂对象的一种模式，解决构造方法传参过多的问题
	Builder模式就是使用一个代理完成对象的构建过程。
	好处是易于扩展和类的使用，但同时失去了一些效率

	独立，扩展性强
	
	缺点:
		产生多余的Builder对象等，消耗内存
		成员变量过多,对象复制代码臃肿
	
####区别
	[1]经典的Builder模式重点在于抽象出对象创建的步骤，并通过调用不同的具体实现类从而得到不同的结果
	[2]变种的Builder模式的目的在于减少对象创建过程中引入的多个重载构造函数，可选参数以及setters过度使用导致的不必要的复杂性

####变种Builder模式自动化生成
	下载插件 InnerBuilder

####Builder的经典模式代码示例
	//基类Modle
	public abstract class Person {
	    public String mName;
	    public int mAge;
	    public String mSex;
	
	    public abstract void setName(String name);
	    public abstract void setAge(int age);
	    public abstract void setSex(String sex);
	
	    @Override
	    public String toString() {//为了打印用的
	        return getClass().getSimpleName()+ "  : " +
	                "mName='" + mName + '\'' +
	                ", mAge=" + mAge +
	                ", mSex=" + mSex +
	                '}';
	    }
	}

	//Modle具体产品
	public class Teacher extends  Person {
	    @Override
	    public void setName(String name) {    this.mName=name;   }
	    @Override
	    public void setAge(int age) {    this.mAge=age;	    }
	    @Override
	    public void setSex(String sex) {     this.mSex=sex;    }
	}

	//Builder基类
	public abstract class PersonBuilder {
	    public Person mPerson;
	    public abstract PersonBuilder setName(String name);
	    public abstract PersonBuilder setAge(int age);
	    public abstract PersonBuilder setSex(String sex);
	
	    @Override
	    public String toString() {
	        if (mPerson != null){
	            mPerson.toString();
	        }
	        return super.toString();
	    }
	}

	//具体产品的Builder
	public class TeacherBuilder extends PersonBuilder {
	    public TeacherBuilder() {     this.mPerson = new Teacher();    }
	    @Override
	    public PersonBuilder setName(String name) {  mPerson.mName = name;    return this;	    }
	    @Override
	    public PersonBuilder setAge(int age) {  mPerson.mAge = age;  return this;  }
	    @Override
	    public PersonBuilder setSex(String sex) {    mPerson.mSex = sex; return this;  }
	    @Override
	    public String toString() {   return mPerson.toString();  }
	}

	//在MainActivity使用Builder模式
	public class MainActivity extends AppCompatActivity {
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	    }
	
	    public void toast1(View view) {
	        PersonBuilder personBuilder = new TeacherBuilder()
	                .setName("WUKIMDA, ")
	                .setAge(21)
	                .setSex(", 男 ");
	        Toast.makeText(this, personBuilder.toString(), Toast.LENGTH_SHORT).show();
	    }
	}


####Builder的变种模式代码示例(变种模式通常指Android中使用Builder模式)
	Android Studio中使用"InnerBuilder"插件可以一键生成

	//Modle基类
	public abstract class Person {
	
	    //只写这成员变量  在具体的产品类继承本基类  可以一键生成
	    public String mName;
	    public int mAge;
	    public String mSex;
	
	    @Override
	    public String toString() {//打印使用
	        return getClass().getSimpleName()+ "  : " +
	                "mName='" + mName + '\'' +
	                ", mAge=" + mAge +
	                ", mSex=" + mSex +
	                '}';
	    }
	}

	//Modle具体产品
	public class Teacher extends Person {
	    private Teacher(Builder builder) {
	        mName = builder.mName;
	        mAge = builder.mAge;
	        mSex = builder.mSex;
	    }
	    public static final class Builder {
	        private String mName;
	        private int mAge;
	        private String mSex;
	        public Builder() {}
	        public Builder mName(String val) {  mName = val;    return this;  }
	        public Builder mAge(int val) {     mAge = val; return this;    }
	        public Builder mSex(String val) {    mSex = val;   return this;    }
	        public Teacher build() {//注意,最后的是builder方法,返回的是具体的Model
	            return new Teacher(this);
	        }
	    }
	}

	//在MainActivity中使用
	public class MainActivity extends AppCompatActivity {
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	    }
	
	    public void toast1(View view) {
	        Teacher build = new Teacher.Builder()
	                .mAge(21)
	                .mName("KIMDA")
	                .mSex("Boy")
	                .build();
	        Toast.makeText(this, build.toString(), Toast.LENGTH_SHORT).show();
	    }
	}
