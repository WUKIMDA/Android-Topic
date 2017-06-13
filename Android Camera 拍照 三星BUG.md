##Android Camera 三星BUG
	(1) 摄像头拍照后图片数据不一定能返回 ;  onActivityResult的data为空  
	(2) 三星的camera强制切换到横屏  导致Activity重启生命周期 
	(但是部分机型  配置  android:configChanges  也不能阻止横竖屏切换); 

###问题所在
	生命周期被销毁重建


###解决方法  
	[1]如果 activity 的销毁如果无法避免在activity销毁之前调用 onSaveInstanceState  保存图片的路径   
		[1.1]当activity重新创建的时候 会将 onSaveInstanceState  保存的文件传递给onCreate(Bundle savedInstanceState)当中进行恢复状态
	[2]在onCreate当中  检查照片的地址是否存在文件  以此来判定拍照是否成功

	