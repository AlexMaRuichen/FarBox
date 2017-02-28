Title: 如何给你的farbox增加搜索栏
在成功给blog增加分类后,通过扒扒扒成功把别的模板的搜索栏加了进来,实现方式如下

1. 你需要下载一个模板的源码,并将其存到根目录的template文件中,并将模板优先级设置为自定义模板

2. 首先打开base.jade,选择一个合适的位置,比如我想将搜索栏加在标题之下那么找到这个位置
``` javascript
    .header
        .logo-title
            .title.animated.fadeInDown
                h1(title="")
                    a(href="/") {{ site.title }}
                if site.raw_content
                    .description.animated.fadeInDown
                        p= '&#9829; %s &#9829;' % site.raw_content[:100]

        ul.social-links.animated.fadeInDown
            if site.configs.twitter
                li
                    a(href="https://twitter.com/{{site.configs.twitter}}")
                        i.fa.fa-twitter
                        span Twitter
            if site.configs.instagram
                li
                    a(href="http://instagram.com/{{site.configs.instagram}}")
                        i.fa.fa-instagram
                        span Instagram
            if site.configs.dribbble
                li
                    a(href="https://dribbbble.com/{{site.configs.dribbble}}")
                        i.fa.fa-dribbble
                        span Dribbble
            if site.configs.weibo
                li
                    a(href="http://weibo.com/{{site.configs.weibo}}")
                        i.fa.fa-weibo
                        span Weibo
```
在下面增加代码
``` javascript
#layout.pure-g.animated.fadeInDown
    .pure-u-7-24:#sidebar
        .widget
            form#search
                input#search_value.text(placeholder="Search",type="text",value=request.args.s,	onkeyup="if (event.keyCode == 13) search();")
```
**.animated.fadeInDown是我博客的淡入效果,请更换为自己博客的动画或直接删除**
之后将搜索函数加入文件的最下面
``` css
script
    function search() {
        if (document.getElementById('search_value').value) {
          location.href = 'http://' + location.host + '?s=' + document.getElementById('search_value').value;
        }
        return false;
     }
```
3. 在template文件夹中找到style目录下的style.css,加入如下代码
``` css
#sidebar {
	border-left: 1px solid #ddd;
	padding-left: 900px;
	margin-top: 45px;
	padding-bottom: 20px;
	word-wrap: break-word;

	.widget {
		margin-bottom: 30px;
		.widget-title {
			color: #6E7173;
			line-height: 2.7;
			margin-top: 0;
			font-size: 16px;
			border-bottom: 1px solid #ddd;
			display: block;
			font-weight: normal;
		}
		ul {
			list-style: none;
			padding: 0;
			li {
				margin: 5px 0;
				line-height: 1.5;
			}
		}
	}

	#search {
		position: relative;
		overflow: hidden;
		input {
			padding: 12px 30px 11px 15px;
			line-height: 1.5;
			border:1px solid #ddd;
			width:50%;
			-webkit-box-sizing:border-box;
			-moz-box-sizing:border-box;
			box-sizing:border-box;
			outline:none;
		}
	}
}
```
其中比较重要的属性是padding和width,可根据网页自行调节

这样搜索框和功能就已经实现了

5. 对于强迫症患者,可能还想在搜索后显示"搜索包含xxx的结果",请在index.jade下添加

``` css
    block title
    	title= request.args.s or tags.join('+') or category.title or site.title
    block content
    	+_('reading_label', 'currently reading articles under <b>%s</b>')
    	+_('reading_label', '正在查看 <b>%s</b> 下的文章', 'zh_cn')
    	+_('search_label', 'post contains <b>%s</b>  found below')
    	+_('search_label', '搜索包含 <b>%s</b>  的结果', 'zh_cn')
    	if request.path.strip('/') == 'category'
    		+redirect('/categories')
    	if request.path.strip('/') == 'tag'
    		+redirect('/tags')

    	if tags or category
    		h1.label-title= _('reading_label') % (request.args.s or tags.join('+') or category.title)
    	if request.args.s
    		h1.label-title=_('search_label') % request.args.s

```
由于并不会jade和css的语法,这些代码全是在现成模板中找的,如有问题请谅解