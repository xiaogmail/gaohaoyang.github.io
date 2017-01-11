---
layout: post
title: 测试帖
---

* content
{:toc}

Github Markdown 注意点（区别于简书的任性）：

* 用`Tab`代替随意的空格；
* 勤换行；
* `**强调文本**`，两端不能有空格；
* `~~删除线~~`；
* 用在引用中形成段落：
```
> **引用标题**
> 
> 内容
```
* 代码：
```
```javascript
code
\```
```
* 目录支持标记！！
`* content`
`{:toc}`
* 最后别忘了写开头的 yaml 信息。








---
文件名统一用英语，中文几乎肯定挂。title用中文就行了。

这样之后，在本地测试就完美了。

---
svg 图片测试成功！（以后思维导图这种就用 svg ，放大后不会失真；普通图片就用 png ）

`![svgtest](/images/svgtest.svg)`

![svgtest](/images/svgtest.svg)

```
> r1
> 
> > r2
```
> r1
> 
> > r2

```
> r3

> > r4
```
> r3

> > r4

```
> r5
> > r6
```
> r5
> > r6

```
> These violent delights have violent ends
>
> And in their trimuph die, like fire and powder,
>
> Which, as they kiss, consume.
```
> These violent delights have violent ends
>
> And in their trimuph die, like fire and powder,
>
> Which, as they kiss, consume.

Github 的 MarkDown ~~只支持两层列表：~~

### 支持！但要严格用 Tab ！不能用两个空格！！

```
* 第一层
	* 第二层
		* 第三层
```
* 第一层
	* 第二层
		* 第三层

```
~~删除线~~
```
~~删除线~~

```
** 强调，但前后有空格 **
```
** 强调，但前后有空格 **

```
**强调，前后没空格**
```
**强调，前后没空格**

















---
看[这里](https://guides.github.com/features/mastering-markdown/)，GFM.