Title: Markdown
url: markdown

## 基本语法


> Markdown是一种非常简洁的写作语法，它以纯文本的写作方式，完成了富文本的显示格式。更多请参阅[《Markdown 语法说明 (简体中文版)》](http://wowubuntu.com/markdown/)

### 标题
	# Header 1
	## Header 2
	### Header 3

### 链接

	An [example](http://url.com/)
	<http://url.com>
	<you@email.com>
	
### 插入图片

	![alt text](/path/img.jpg)

但在FarBox Editor中，你只需要把图片拖入文本区域就可以了。

*其中的`/path/img.jpg`是相对于某个站点目录而言的。*

### 列表

有序列表:
```
1.  Foo
2.  Bar
```

无序列表:  
```
*   A list item.
*   Bar
```


### 水平分割线
3个（或以上）连续的`-`，中间允许空格:

### 表格
一个表格看起来如下所示:

```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```


### 脚注

    This is emphatically [^1]
    Something else [^key2].
    EOF
    
    [^1]: emphatically: 坚决地，着重地，强调地（~ deny 坚决否定）
    [^key2]: key2的注释


## 代码与高亮

### 嵌入代码块

以3个反引号为开始与结束:

\`\`\` 
import this
print 'something'
\`\`\`

> 反引号键一般在键盘按键1左侧，需要在英文状态下输入。

### 代码高亮

如果声明代码语言的，会进行代码高亮的处理。

\`\`\`python 
codes
\`\`\`

如果语言后面多一个`:n`，则会显示代码的行号。

\`\`\`python:n
codes
\`\`\`

> 代码的高亮的具体样式是由CSS控制的，由[Pygments](http://pygments.org/docs/styles/)生成。你可以访问[Pygments Styles](/pygments.html)查看常见的几种风格。

### 插入gist

单独一行，\`gist gist_id\`就可以了。反引号\`是必须的，以避免跟其它普通文字产生潜在的冲突。

但需要注意，如果你的页面CSS有对word-break定义过的，避免gist样式出错，需要对其重置一下。
```css
.gist{
    word-break: normal;
}
```


## Mathjax

\`\`\`mathjax
your mathjax codes.
\`\`\`

这样的写法，会被解析为Mathjax能够处理的脚本。但前提你已经知道Mathjax是如何运作的，或者直接在网站的后台管理中另外`启用数学公式`。


## FarBox对换行的特殊处理

一般的Markdown格式是要求`两个换行`才算是真正视觉上的换行。

如果文件的后缀名为`.mk`的话，或者在正文的首行写`md_linebreaks: two`进行声明，则按照常规的Markdown格式渲染。

