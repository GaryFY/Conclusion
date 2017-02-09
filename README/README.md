README.md练习
====

###标题

= 和 - 的数量是没有限制的。通常的做法是使其和标题文本的长度相同，这样看起来比较舒服。或者可以像我一样，用四个 - 或 =。
或者利用#

###引用
####多行引用

>如果仅在第一行使用 `>`，
后面相邻的行即使省略 `>`，也会变成引用内容

####嵌套引用

>也可以在引用中
>>使用嵌套的引用

###列表
####无序列表

* 可以使用 `*` 作为标记
+ 也可以使用 `+`
- 或者 `-`

####有序列表
1. 有序列表以数字和 `.` 开始；
3. 数字的序列并不会影响生成的列表序列；
4. 但仍然推荐按照自然顺序（1.2.3...）编写。

####嵌套的列表
1. 第一层
  + 1-1
  + 1-2
2. 无序列表和有序列表可以随意相互嵌套
  1. 2-1
  2. 2-2

###代码
####代码块
可以使用缩进来插入代码块：代码块前后需要有至少一个空行，且每行代码前需要有至少一个 Tab 或四个空格；

	<html> // Tab开头
		<title>Markdown</title>
	</html> // 四个空格开头

####行内代码
利用``

###分割线
1. 可以在一行中使用三个或更多的 `*`、`- `或` _ `来添加分隔线（`<hr>`）：
```
***  
------  
___  
```
	
2. 多个字符之间可以有空格（空白符），但不能有其他字符：
```
* * *  
- - -  
```
	
###超链接
####行内式

格式为` [link text](URL 'title text')`  
[Google](http://www.google.com/)

####参考式
参考式链接的写法相当于行内式拆分成两部分，并通过一个 识别符 来连接两部分。参考式能尽量保持文章结构的简单，也方便统一管理 URL。

####自动链接
使用`<>` 包括的 URL 或邮箱地址会被自动转换为超链接：  
<http://www.google.com/>

<123@email.com>

###图像

插入图片的语法和插入超链接的语法基本一致，只是在最前面多一个`!`。也分为行内式和参考式两种

####行内式
`![GitHub](https://avatars2.githubusercontent.com/u/3265208?v=3&s=100 "GitHub,Social Coding")`  
![GitHub](https://avatars2.githubusercontent.com/u/3265208?v=3&s=100 "GitHub,Social Coding")

####指定图片的显示大小

Markdown 不支持指定图片的显示大小，不过可以通过直接插入`<img />`标签来指定相关属性：  
`<img src="https://avatars2.githubusercontent.com/u/3265208?v=3&s=100" alt="GitHub" title="GitHub,Social Coding" width="50" height="50" />`  	
<img src="https://avatars2.githubusercontent.com/u/3265208?v=3&s=100" alt="GitHub" title="GitHub,Social Coding" width="50" height="50" />	

###强调
1. 使用 `* *` 或 `_ _`包括的文本会被转换为 `<em></em>` ，通常表现为斜体：
  
	*演示*  _文本_
	
2. 使用 `** **` 或 `__ __` 包括的文本会被转换为 `<strong></strong>`，通常表现为加粗:   

	**演示**  __文本__
	
3. 用来包括文本的 `*` 或` _ `内侧不能有空白，否则` * `和` _ `将不会被转换（不同的实现会有不同的表现）：   
	
4. 如果需要在文本中显示成对的` * `或` _ `，可以在符号前加入` \ `即可：  

5. `*`、`**` 、`_`和` __ `都必须成对使用。

###删除线

	这就是~~删除线~~
	
这就是 ~~删除线~~

###代码块和语法高亮
####代码块
与原来使用缩进来添加代码块的语法不同，这里使用 \`\`\` \`\`\`来包含多行代码：  

```
<p>code here</p>
```

####代码高亮
在上面的代码块语法基础上，在第一组 \`\`\` 之后添加代码的语言，如 'javascript' 或 'js'，即可将代码标记为`JavaScript`：  
```js
window.addEventListener('load', function() {
  console.log('window loaded');
});
```
###表格
####单元格和表头
使用 `| `来分隔不同的单元格，使用 `-` 来分隔表头和其他行：
```
name| age 
----| --- 
LearnShare| 12 
Mike| 32 
```

	name| age 
	----| --- 
	LearnShare| 12 
	Mike| 32 
	
	
####对齐
在表头下方的分隔线标记中加入 `:`，即可标记下方单元格内容的对齐方式：

* `:---` 代表左对齐
* `:--:` 代表居中对齐
* `---:` 代表右对齐
```
| left | center | right | 
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    | b      | c     | 
```

| left | center | right | 
| :--- | :----: | ----: |
| aaaa | bbbbbb | ccccc |
| a    | b      | c     |
	
	
####插入其他内容
表格中可以插入其他 Markdown 中的行内标记：

```
|     name     | age |             blog                |
| ------------ | --- | ------------------------------- |
| _LearnShare_ |  12 | [LearnShare](http://xianbai.me) |
| __Mike__     |  32 | [Mike](http://mike.me)          |         
```
	
|     name     | age |             blog                |
| ------------ | --- | ------------------------------- |
| _LearnShare_ |  12 | [LearnShare](http://xianbai.me) |
| __Mike__     |  32 | [Mike](http://mike.me)          |

###Task List

```
- [ ] Eat
- [x] Code
  - [x] HTML
  - [x] CSS
  - [x] JavaScript
- [ ] Sleep
```
- [ ] Eat
- [x] Code
  - [x] HTML
  - [x] CSS
  - [x] JavaScript
- [ ] Sleep