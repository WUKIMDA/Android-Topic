#ORMLite框架的简单使用


##1.使用ORMLite框架进行Bean搭建数据库表
	DatabaseTable操作类
		@DatabaseTable(tableName = "表名")
	
	DatabaseField一般操作成员变量
		canBeNull 		表示不能为null；
		foreign=true	表示是一个外键;
		columnName 		列名
		generatedId = true自增
		id = true	手动

	如:
		@DatabaseTable(tableName = "t_address")
		public class RecepitAddress implements Serializable {
		
		    @DatabaseField(generatedId = true)  //自动增长
		    private int id;
		
		    @DatabaseField(columnName = "name")
		    private String name;
		    @DatabaseField(columnName = "sex")
		    private String sex;
		}


##2.搭建数据库和表
	[1]继承OrmLiteSqliteOpenHelper
	[2]建表TableUtils.createTable(connectionSource参数传递的, Bean类.class);
	
	如:
		public class TakeoutOpenHelper extends OrmLiteSqliteOpenHelper {
		
		    public TakeoutOpenHelper(Context context) {
		        super(context, "数据库名.db", null,版本);
		    }
		
		    @Override
		    public void onCreate(SQLiteDatabase database, ConnectionSource connectionSource) {
		        //创建数据库执行
		        try {
		            TableUtils.createTable(connectionSource, User.class);
		            //版本2时的新用户
		            TableUtils.createTable(connectionSource, RecepitAddress.class);
		        } catch (SQLException e) {
		            e.printStackTrace();
		        }
		    }
		
		    @Override
		    public void onUpgrade(SQLiteDatabase database, ConnectionSource connectionSource, int oldVersion, int newVersion) {
		        //更新数据库执行
		        try {
		            //版本2时的老用户
		            TableUtils.createTable(connectionSource, RecepitAddress.class);
		        } catch (SQLException e) {
		            e.printStackTrace();
		        }
		    }
		}

##3.操作数据库
	不封装DAO:
		TakeoutOpenHelper takeoutOpenHelper = new TakeoutOpenHelper(UiUtils.getContext());
	        try {
	            Dao<RecepitAddress, Integer> dao = takeoutOpenHelper.getDao(RecepitAddress.class);
	        } catch (SQLException e) {
	            e.printStackTrace();
	        }
		通过操作dao即可
			插入数据dao.create(Bean对象)
			删除数据dao.delete(Bean对象)
			更新数据dao.update(Bean对象)
			查询指定数据dao.queryForEq("查找条件",查找值)
			查询全部数据dao.queryForAll()
			等等
	
	封装(同操作Dao)
		插入数据dao.create(Bean对象)
		删除数据dao.delete(Bean对象)
		更新数据dao.update(Bean对象)
		查询指定数据dao.queryForEq("查找条件",查找值)
		查询全部数据dao.queryForAll()
		等等
		

####