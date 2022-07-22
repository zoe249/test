# uni-app引入uView组件库

*作者：李莉*

*描述：uni-app开发微信小程序引入uView组件库*

*日期：2020-05-12*

## 一、安装

1、确保Hbuilder X版本在 2.5.5及以上

2、安装scss插件，在HX菜单的 工具->插件安装中找到"scss/sass编译"插件进行安装， 如不生效，重启HX即可

## 二、下载uview-ui文件

打开官网http://www.uviewui.com/components/install.html在下载安装分示例工程中分别下载zip

![https://imgchr.com/i/YtB7jS](https://s1.ax1x.com/2020/05/12/YtB7jS.png)

![https://imgchr.com/i/YYO2ad](https://s1.ax1x.com/2020/05/12/YYO2ad.png)

将两个zip中的uview-ui文件夹拷贝到项目根目录中

![https://imgchr.com/i/YYXFo9](https://s1.ax1x.com/2020/05/12/YYXFo9.png)

![https://imgchr.com/i/YYXidJ](https://s1.ax1x.com/2020/05/12/YYXidJ.png)

## 三、配置

1、引入uView主js库

在项目根目录中的`main.js`中，引入并使用uView的JS库，注意这两行要放在`import Vue`之后。

```
// main.js
import uView from "uview-ui";
Vue.use(uView);
```

2、引入全局scss文件

在项目根目录的`uni.scss`中引入此文件。

```
/* uni.scss */
@import 'uview-ui/theme.scss';
```

3、引入uView基础样式

在`App.vue`中**首行**的位置引入，注意给style标签加入lang="scss"属性

```
<style lang="scss">
	@import "uview-ui/index.scss";
</style>
```

4、配置easycom组件模式

此配置需要在项目根目录的`pages.json`中进行。

**easycom组件路径一定要加@，否则会报错**

```
// pages.json
{
	"easycom": {
		"^u-(.*)": "@/uview-ui/components/u-$1/u-$1.vue"
	},
	
	// 此为本身已有的内容
	"pages": [
		// ......
	]
}
```

## 四、在index.vue中使用

```
<u-button type="primary">主要按钮</u-button>
```

注：

1、组件中自带事件@change写成bind:change

2、每次引入新组件，需要重启项目

3、全屏选项卡，圆形进度条暂时不能用