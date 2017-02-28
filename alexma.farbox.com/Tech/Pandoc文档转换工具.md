---
date: 2016-10-17 13:36
status: public
title: Pandoc文档转换工具
---

有没有感到虽然$\LaTeX$功能强大但语法过于复杂,而markdown生成的pdf又没有那么多可操作性,那么可以用**pandoc**直接实现两者的转换

[pandoc](http://www.pandoc.org)是一个文档转换工具,可以支持将 

> commonmark, docbook, docx, epub, haddock, html, json*, $\LaTeX$,markdown,markdown_github, markdown_mmd, markdown_phpextra, markdown_strict, mediawiki, native, odt, opml, org, rst, t2t,textile, twiki

格式的文件转换为

>asciidoc, beamer, commonmark, context, docbook, docx, dokuwiki,dzslides, epub, epub3, fb2, haddock, html, html5, icml, json*,$\LaTeX$, man, markdown, markdown_github, markdown_mmd,markdown_phpextra, markdown_strict, mediawiki, native, odt,opendocument, opml, org, pdf**, plain, revealjs, rst, rtf, s5,slideous, slidy, texinfo, textile

格式的文件(是不是很强)

pandoc+Typora使用方法

1. 打开markdown文件

2. 点击属性栏的File->Export->你需要的格式

3. 生成.tex文件后,不要忘记加上你本机使用的中文宏包,否则是无法打开pdf的


使用效果可见 NOIPTEST11_Solution