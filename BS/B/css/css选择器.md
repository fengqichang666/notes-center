## :where

```css
.dark-theme button,
.dark-theme a,
.light-theme button,
.light-theme a{
 color: pink;
}

:where(.dark-theme, light-theme) :where(button, a) {
    color: pink;
}
```

## :is

`:is()`跟`:where()`可以说一模一样，区别就是 `:is()`的优先级不是0，而是由传入的选择器来决定的

```css
字体颜色会是 pink，因为 类选择器 优先级比 标签选择器 优先级高~
:is(.test) {
    color: pink
}
div {
    color: yellow;
}
```

## :has

```css
图片显示时，字体大小为 12px 图片隐藏时，字体大小为 20px
.container {
    font-size: 20px;
}
.container:has(img) {
    font-size: 12px;
}

或者
.container:has(.test-img) {
    font-size: 12px;
}
-------------------------
判断容器有没有子img，有的话字体设置为 12px（上面的例子是后代选择器，不是子选择器）
.container:has(>img) {
    font-size: 12px;
}
判断容器有没有一个小相邻的img，有的话设置字体颜色为 red
.container:has(+img) {
    color: red;
}
------------------------
 hover 到 子img 上时，让 container 的字体变粗
.container:has(>img:hover) {
    color: red;
}
```

## 属性选择器

```css
1) [att=val] 选择器
//选择具有特定属性的元素
[data-text){ 
	background-color: yellow; 
}

//判断属性值
[id=sectionl){ 
	background-color: yellow; 
}
2) [att*=val] 选择器
[id*=sectionl){ 
	background-color: yellow; 
}
3) [att^=val] 选择器   属性的开头字符都为val的元素
[id^=sectionl){ 
	background-color: yellow; 
}
4) [att$=val] 选择器  属性值的结尾字符为val的元素   注意该属性选择器中必须在指定匹配字符前加上 “\” 这个转义字符。
[id$=\-1]{
	background-color: yellow; 
}
5) 使用 [attribute~=value] 来选择具有指定属性，并且该属性的多个值中包含给定值的元素：
举例
a(hrefS=\/J:after, a[href＄一htm]:after, a[href沪html]:after{
	content: "Web网页” ;
	colo:r:: red;
｝
a[href$=jpg):after{
	content:"JPEG图像文件” ;
	color: green;
｝
```



## 结构性伪类选择器

```css
1)	root选择器   将样式绑定到页面的根元素中
	:root{ 
		background-color: yellow; 
	}
2)	not选择器
	body *:not(h1){
		background-color: yellow; 
	}
3)	empty选择器   当元素内容为空白时
	:empty{
		background-color: yellow; 
	}
4)	target选择器 对页面中某个target元素（该元素的id被当做页面中的超链接来使用）指定样式，该样式只在用户点击了页面中的超链接，	并且跳转到target元素后起作用．
    target{ 
    	background-color: yellow; 
    ｝ 
5)	first-child选择器 last-child选择器
	li:first-child{
		background-color: yellow;
	}
	li:last-child{
		background-color: yellow;
	}
6)	nth-child选择器	nth-last-child选择器
在计算子元素是第奇数个元素还是第偶数个元素的时候，是连同父元素中的所有子元素一起计算的．
"h2:nth-child(odd)" 这句话的含义， 并 不是指 “针对div元素中第奇数个h2子元素来使用“，而是指 “当div元素中的第奇数个子元素如果是h2子元素的时候使用“ 。
	li:nth-child (2) { 
   	 	background-color: yellow; 
    ｝ 
    li:nth-last-child (2){ 
    	background-color: skyblue; 
    ｝ 
    对所有第奇数个子元素或第偶数个子元素使用样式
    nth-child (odd) { 
		//指定样式
	}
	所有正数下来的第偶数个子元素
	＜子元素＞:nth-child(even){
		//指定样式
	}
	／／所有倒数上去的第奇数个子元素
    ＜子元素＞:nth-last－child(odd){ 
    	//指定样式
    }
	所有倒数上去的第偶数个子元素
	＜子元素＞:nth-last-child(even){ 
		//指定样式
	}
7)	nth-of-type选择器	nth-last-of-type选择  针对同类型的子元素进行计算
    h2:nth-of-type(even){
        background-color: skyblue; 
    }
循环使用样式 使用nth-child举例
	nth-child(n)  可以把参数n改成an+b  a 和 b 都必须为整数，并且元素的第一个子元素的下标为 1。换言之就是，该伪类匹配所有下标在集合 { an + b; n = 0, 1, 2, ...} 中的子元素
8)	only-child选择器   只有一个子元素时
    li:only-child{ 
        background-color: skyblue;
    }
补：
:valid 伪类用于选择具有与其属性（如 pattern、type 等）所指定要求相匹配的内容的输入元素。
:invalid 伪类用于选择具有内容不符合要求的输入元素。
:required 伪类用于选择具有 required 属性的输入元素，该属性表示在提交表单之前必须填写它们。
:optional 伪类用于选择没有 required 属性的输入元素，这意味着它们不是必填项。  
:default：匹配设置为默认选择状态的用户界面元素（如单选按钮或复选框）
:fullscreen：选择当前以全屏模式显示的元素。
:in-range：匹配值在指定范围内的表单元素（使用 min 和 max 属性指定范围）。
:out-of-range：匹配值在指定范围之外的表单元素。
:indeterminate：选择状态不确定的表单元素，例如既未选中也未取消选中的复选框（在树状结构中经常见到）。  
:read-only：匹配由于 readonly 属性而不可编辑的表单元素。
:read-write：选择可由用户编辑的表单元素，意味着它们不是只读的。
:lang()：根据元素的语言属性进行匹配。例如，:lang(en) 选择使用英语定义的元素。        
```

## `UI`元素状态伪类选择器

```css
常用举例：
a:link {color:#FFOOOO;text-decoration:none} 
a:visited {color:#OOFFOO;text-decoration:none} 
a:hover {color:#FFOOFF;text-decoration:underline} 
a:active {color:#OOOOFF;text-decoration:underline} 
1)	E:hover选择器  当鼠标指针移动到元素上面时使用的样式
2)	E:active选择器	元素被激活（鼠标在元素上按下还没有松开）时使用的样式
3)	E:focus选择器	获得光标焦点时使用的样式，主要是在文本框控件获得焦点并进行文字输入的时候使用。
4)	E:enabled选择器  处于可用状态时的样式。
5)	E:disabled选择器  处于不可用状态时的样式。
6)	E:read-only选择器	 处于只读时的样式。
7)	E:read-write选择器	处于非只读时的样式。
8)	E:checked选择器  复选框处于选取状态时的样式
9)	E:default选择器	当页面打开时默认处于选取状态的单选框或复选框控件的样式。用户将默认设定为选取状态的单选框或复选框修改为非选取状态， 使用default选择器设定的样式依然有效
10)	E:indeterminate选择器  一组单选框中任何一个单选框都没被选中时的样式，用户选择之后样式取消
11)	E::selection选择器 	用来指定当元素处千于选中状态时的样式。

```

## 兄弟元素选择器

```css
former_element ~ target_element { style properties }
p ~ span {
  color: red;
}
```



## 伪元素选择器

```css
1、first-line伪元素选择器    为某个元素中的第一行文字使用样式
	p:first-line{color:#OOOOFF} 
2、first-letter伪元素选择器   为某个元素中的文字的首字母（欧美文字）或第一个字（中文或日文等汉字）使用样式。
	p:firet-letter{color:#OOOOFF} 
3、before伪元素选择器  在某个元素之前插入一些内容(图片 属性值  计数器)
    li:before{
        content: '插入文字';
        font-size:12px;
        color:red; 
    } 
    其他内容
    li:before{content: url(test.wav)} 
4、after伪元素选择器  在某个元素之后插入一些内容

::placeholder 伪元素用于选择并修改表单字段的占位符文本，从而应用特定的样式。占位符文本是在用户未输入任何内容时显示的默认文本。
::selection 伪元素用于选择并修改用户所选文本的样式。可以应用于包含文本内容的任何元素，如段落、标题、按钮等。
::marker 伪元素用于为列表项中的标记符设置样式，这些标记符通常包含无序列表的项目符号或有序列表的数字/字母。
::file-selector-button：用于选择文件的按钮元素的伪元素。它可以用来样式化文件上传控件中的选择按钮。
::cue：用于样式化媒体元素（如音频或视频）中的字幕或注释的伪元素。可以用来设置字幕的样式，比如字体、颜色等。
::part()：用于自定义 Web 组件的内部部件的伪元素。它可以针对组件的具体部分应用样式，并通过组件的 shadow DOM 提供的 Custom Elements API 进行访问。
::slotted()：用于样式化插槽内容的伪元素。插槽是一种在 Web 组件中用于插入内容的机制，::slotted() 可以应用样式到被插入的内容，以实现更精确的样式控制。

```





