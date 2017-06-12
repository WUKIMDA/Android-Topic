##国际化i18n
	internationalization  因为在i和n之间还有18个字符
	在res/目录下在新建文件夹为：values-语言代号-地区代号。如values-zh-rCN表示简体中文，values-zh-rTW表示繁体等。
	注：配置选项包括语言代号、地区代号。表示中文和中国的配置选项是 zh-rCN（zh表示中文，CN表示中国）。 
	表示英文和美国的配置选项是en-rUS（en表示英文，US表示美国）。前面的r是必须的。
	
	图片资源的国际化
		中文简体的图片资源：drawable-zh-rCN
		日本的图片资源：drawable-jp
		保证图片的文件名是一样
	
	一键实现语言国际化
		AndroidLocalizationer
			找到string.xml——>右键——>Convert to other languages 
	