I find the template of this blog from Yihui's github, and thus I would like to give a credit to him. I have tried octopress before. In my personal view, this blog system is both simpler and better, since you do not have to write the git script to conver md to htmls, and you only store md on github, the category and tag is very convenient to use, and the red color is great. 

# Set up the Headers
When you write the md file, you have to define the headers. 
When you mark the categories and tags, do forget to put a blank space between  "-" and your categories or tags.
For example: https://raw.github.com/chengjun/cn/gh-pages/_posts/2013-09-07-part-of-speech-analysis-with-opennlp.markdown

![](http://farm6.staticflickr.com/5547/12715481793_6f7835b3a4_o.jpg)

You can view the header here: https://github.com/chengjun/cn/blob/gh-pages/_posts/2013-09-07-part-of-speech-analysis-with-opennlp.markdown

Using MathJax on Github:Pages
http://christopherpoole.github.io/using-mathjax-on-github-pages/

modify your _config.yml by setting markdown: kramdown
add the following script to default.html in the layout folder

<!--mathjax start-->
<script type="text/javascript"
src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
<!--mathjax end-->
highlight.js
There are many styles of highlighting to choose, click here to decide.

download highlight.pack.js together with styles,languages from here, and upload to your folder of js.
modify the layout by adding the following code to default.html

<!--highlight.js Start-->
<link rel="stylesheet" title="Default" href="/media/js/styles/tomorrow-night-blue.css">
<script type="text/javascript" src="/media/js/highlight.pack.js"></script>
<script>
hljs.configure({tabReplace: '    '});
hljs.initHighlightingOnLoad();
</script>
<!--highlight.js End-->
