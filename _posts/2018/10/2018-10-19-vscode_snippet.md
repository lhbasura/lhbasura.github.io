---
layout:     post
title:      vscode上snippet使用总结
subtitle:   vscode_snippet
date:       2018-10-19
author:     lhbasura
header-img: img/banner_vscode.jpg
keywords_post:  "vscode,snippet"
catalog: true
tags:
    - vscode
    - snippet  
---  


## 前言  
vscode上面自带了snippet插件，这里分享一些我平时常用的一些代码段
## snippet自定义php打印代码段 
用PHP编写代码的时候，我们可能需要用`print_r`函数来打印变量或数组，但是如果数组里面元素比较多我们需要在前后加上`pre`，
这时候我们可以用snippet自定义一下方便我们打印,就像下面这样
```
"Print value message out": {
		"prefix": "printo",
		"body": [
			"<pre><?php print_r($1);?></pre>"
		],
		"description": "print value message"
	},
	"Print value message in": {
		"prefix": "printi",
		"body": [
			"echo '<pre>';print_r($1);echo '</pre>';",
		],
		"description": "print value message"
	}
```
我用printo在php标签外部打印，printi在php标签内部打印，这样就很方便。
## snippet生成注释
很多时候我们代码的注释里面都需要写上日期、作者、联系方式、代码段的描述等，就像下面这样 
```
/*
* @Date:2018-10-20 04:20:42
* @Author: LiuHongbo
* @Email: hongbo.liu@gmail.com
* @Description:
*/
```
如果我们每次都写不免繁琐，所以这里我们可以通过snippet快捷生成
```  
"code notes":{
		"prefix": "/**",
		"body": [
			"/*",
			"* @Date:${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE} ${CURRENT_HOUR}:${CURRENT_MINUTE}:${CURRENT_SECOND}",
			"* @Author: LiuHongbo",
			"* @Email: lhbasura@gmail.com",
			"* @Description:$1",
			"*/"
		],
		"description": "code notes"
	},
```  
这里值得一提的事vscode里面自带的一些变量  
* `CURRENT_YEAR`: 当前年份；  
* `CURRENT_YEAR_SHORT`: 当前年份的后两位；  
* `CURRENT_MONTH`: 格式化为两位数字的当前月份，如 02；  
* `CURRENT_MONTH_NAME`: 当前月份的全称，如 July；  
* `CURRENT_MONTH_NAME_SHORT`: 当前月份的简称，如 Jul；  
* `CURRENT_DATE`: 当天月份第几天；  
* `CURRENT_DAY_NAME`: 当天周几，如 Monday；  
* `CURRENT_DAY_NAME_SHORT`: 当天周几的简称，如 Mon；  
* `CURRENT_HOUR`: 当前小时（24 小时制）；  
* `CURRENT_MINUTE`: 当前分钟；  
* `CURRENT_SECOND`: 当前秒数。  

通过这些变量我们就可以在注释中自动生成时间了  

##  markdown文件中的头部（jekyll） 
在markdown文件中，我发现vscode的snippet好像不能生效，必须得是以`'/'`为开头
具体原因不明确，网上也找不到解释，所以这里我用`'/h'`作为prefix来生成markdown文件的头部注释  
>有大佬如果知道原因希望可以在评论区内指点一二  

```
"markdown file header":{
		"scope": "markdown",
		"prefix": "/h",
		"body": [
			"---  ",
			"layout:     post",
			"title:      $1",
			"subtitle:   $2",
			"date:       ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}",
			"author:     lhbasura",
			"header-img: $3",
			"keywords_post:  \"$4\"",
			"catalog: true",
			"tags:",
			"    - $5  ",
			"---  "
		],
		"description": "print markdown header"

```
以上就是我对snippet目前的一些使用总结