
##主流网络框架Retrofit2
	Retrofit是对OkHttp的封装。

- [1]创建网络请求的Service接口//API接口
>
	public interface TakeOutService {
	   	@GET("home")
    	Call<ResponseInfo> getHomeData();
 		等等接口，以上是一个接口 //GET方式请求，home是接口具体的url
	}

- [2]创建retrofit实例
>
	Retrofit retrofit = new Retrofit.Builder().baseUrl(URL根地址).build();
	//在这里建议在创建baseUrl中以”/”结尾，在服务接口中不以”/”开头和结尾

	//如果需要添加转换器（转Gson数据）就这样写
	Retrofit retrofit = new Retrofit.Builder().baseUrl(URL根地址).addConverterFactory(GsonConverterFactory.create()).build();

- [3]调用Service接口
	TakeOutService takeOutService = retrofit.create(TakeOutService.class);
			//retrofit.contributorsBySimpleGetCall()
    Call<ResponseInfo> homeData = takeOutService.getHomeData();
    //因为没有线程池，异步加载
    homeData.enqueue(new Callback<ResponseInfo>() {｝);
		//如果需要同步请求：homeData.execute()
	
- [4]取消请求
	终止操作是对底层的httpclient执行cancel操作。即使是正在执行的请求，也能够立即终止。

- [5]添加请求头
	在[1]中的Service接口中
	@Headers({
	        "Accept: xxx",
	        "User-Agent: xxx",
	        "name:xxx"
	})

- [6]Service接口中的请求具体参数
	如：
		@GET("search/repositories")
		Call<RetrofitBean> queryRetrofitByGetCall(@Query("q")String owner）
		URL指定查询参数@Query。("q")q字段。queryRetrofitByGetCall(owner)带参数的方法  
	如：@QueryMap注解和map对象参数来指定每个表单项的Key，value的值

- [7]Retrofit设置缓存