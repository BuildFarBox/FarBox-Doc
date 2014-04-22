Title: Markdown
url: markdown

## Basic Syntax

> [Markdown](http://daringfireball.net/projects/markdown/syntax‎
) is a simple but amazing syntax for writing. 

### Headers
	# Header 1
	## Header 2
	### Header 3

### Links and Email

	An [example](http://url.com/)
	<http://url.com>
	<you@email.com>
	
### Images

	![alt text](/path/img.jpg)

If you are using FarBox Editor, just dragging images into the textarea.

*the `/path/img.jpg` is a path relative to your site folder.*

### Lists

Ordered:
```
1.  Foo
2.  Bar
```

Unordered:  
```
*   A list item.
*   Bar
```


### Horizontal Rules

Three or more dashes or asterisks.

### Tables
A simple table looks like this:

```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```


### Footnotes

    This is emphatically [^1]
    Something else [^key2].
    EOF
    
    [^1]: emphatically: some thing here
    [^key2]: footnote for key2


## Code and Highlight

### Fenced Code Blocks

Starts (and ends) with a line containing 3 \` characters:

\`\`\` 
import this
print 'something'
\`\`\`


### Highlight

Declare your code language, then the code will be highlighted.

\`\`\`python 
codes
\`\`\`

If you want to show the lines of code, add `:n` at the end of the code language.

\`\`\`python:n
codes
\`\`\`

> We are using the [Pygments](http://pygments.org/docs/styles/) package to highlight code and the style is controled by CSS. There are some common kinds of [Pygments Style](/pygments.html).

### Insert a Gist

In a separate line ，\`gist gist_id\`. The character \` is necessity.

Tip: sometimes, you should set the `word-break` to `normal` in your customed CSS style to avoid to break the gist styles.

```css
.gist{
    word-break: normal;
}
```


## Mathjax

\`\`\`mathjax
your mathjax code.
\`\`\`

Make sure, you already understand how Mathjax works, and in your site admin dashbord, the `Use MathJax` is set to `On` or `yes`.


## Speical Linebreaks in FarBox

Markdown syntax thinks  a line ends with two or more spaces, then there is a linebreaks. But we do not think so.

If the susfix of filename is `.mk` or `md_linebreaks: two` declared in your post metadata, FarBox will follow the Marydown syntax to break lines.

Other wise, we treat every linebreak as a linebreak.
