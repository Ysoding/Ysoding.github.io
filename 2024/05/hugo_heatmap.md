## Ref

[如何给 Hugo 博客添加热力图](https://blog.douchi.space/hugo-blog-heatmap/#gsc.tab=0)

## Use

这里用的时候是当[shortcodes}(https://gohugo.io/content-management/shortcodes/)，还是就是` {{ range ((where .Site.RegularPages "Type" "post")) }}`是判断注意你的 post 的类型，如果文章内容没有指定type的话默认好像是page，所有需要给所有文章加上`type: 'post'`
