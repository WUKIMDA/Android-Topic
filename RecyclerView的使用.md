##RecyclerView的使用
- 在support-v7 widget包下
- 通过布局管理器LayoutManager	控制其显示的方式	
- 通过ItemDecoration	控制Item间的间隔（可绘制）
- 通过ItemAnimator		控制Item增删的动画

>
>RecyclerView的代码
>
	//布局管理器LayoutManager//有3种
	LayoutManager layout = new LinearLayoutManager(上下文);
	//设置布局管理器
	mRecyclerView.setLayoutManager(layout);
	//设置adapter//extends RecyclerView.Adapter
	mRecyclerView.setAdapter(adapter)

- 其他

	//设置Item增加、移除动画
	mRecyclerView.setItemAnimator(new DefaultItemAnimator());
	//添加分割线
	mRecyclerView.addItemDecoration(new DividerItemDecoration(
    getActivity(), DividerItemDecoration.HORIZONTAL_LIST));


>
>RecyclerView的适配器和方法//extends RecyclerView.Adapter
>
	onCreateViewHolder()创建Holder
	onBindViewHolder()绑定数据//一般调用参数的holder让holder绑定数据,本方法传递数据
	getItemCount()//数据数量
	//适配器的getItemViewType(),废弃了getItemCount()

####LayoutManager
- RecyclerView.LayoutManager吧，这是一个抽象类，好在系统提供了3个实现类：

	LinearLayoutManager 现行管理器，支持横向、纵向。

	GridLayoutManager 网格布局管理器

	StaggeredGridLayoutManager 瀑布就式布局管理器
