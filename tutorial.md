# Jekyll介紹

# 網站建置

## jekyll安裝
先安裝好ruby, gem，接著安裝jekyll bundler
透過以下指令

```
$ gem install jekyll bundler
```

## 網站初始化

```
$ jekyll new <name>
```

此步驟後會產生一些init的資料夾

## 跑起網站

```
$ bundle exec jekyll serve
```

網站會跑在 localhost:4000

## 架構介紹

### posts資料夾

放各種blog post的地方(就是每篇廢文放的地方)

### config.yml

jekyll網站的設定

### Gemfile

存所有這個網站的dependencies
如: jekyll, minima(一種前端framework)

### index.markdown

網站首頁

### about.markdown

網站的關於頁面

## 網站攥寫

寫好的文章要以markdown格式，而且要包含以下所述的front matter

```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2020-04-24 15:01:11 +0800
categories: jekyll update
---
```

- Front matter以上下3個橫線做區隔，內容為這篇post的資訊，如作者、時間、主題等等
- Front matter本身可以用json或yaml的格式來攥寫
- 當改front matter的值時，如把時間改掉，那網頁上的呈現的post時間就會改變
- 並且這些時間、title也會當成是連到文章的url，例如這篇就會連到 XXX/update/2020/04/24XXX這樣的url
- 也可以建立custom的key,value，不一定只有上述四種
- layout: 要使用的template

### 文章規則

1. 寫好的markdown文章要放在posts資料夾內
2. 名字要依據sample的命名: <時間>-<文章標題>.markdown
3. Jekyll會parse filename來看這篇文章的日期，所以filename要這樣命名會比較好，但事實上如果front-matter也有命名的話，那會依據front matter所記錄的時間、標題
4. posts裡面可以有資料夾，不會影響jekyll讀取post，所以可以用資料夾來歸類自己的post

### 草稿

- 可以在資料夾內建一個drafts的資料夾，把檔案放到drafts就行
- 如果想要看draft的效果，可以跑以下指令來看drafts資料夾內的草稿檔
- 草稿話的檔名沒差，不一定要符合上面規則，日期的話jekyll會給他default現在日期。

```
$ jekyll serve --draft
```

### page and posts

Jekyll裡面有兩種形式的網頁
- posts: 部落格文章
- pages: 其他網頁，如about.html, index.thml等等不屬於部落格文章的頁面

#### pages製作方式

- 直接寫一個html或markdown的檔案放在資料夾內即可。
- 如果自創資料夾，把檔案放在該資料夾內，則url會變(多增加那個資料夾)
	- test/about.html 則網址url會變成test/about
- 檔案的front matter layout這個值可以選擇page

## url產生

當跑起網站，瀏覽部落格時，該文章的url格式如下
locahost:4000/<category>/<timestamp>/<filename>

以default的post為例

```
檔案: posts/2020-04-24-welcome-to-jekyll.markdown
url: http://localhost:4000/jekyll/update/2020/04/24/welcome-to-jekyll.html
```

其中url中的jekyll/update這個dir是因為那份檔案的front matter的category為jekyll update
如果改變front-matter裡面的值，則網頁的url也會改變

### 產生permanent url

如上所述，如果改變front-matter裡面的值，則網頁的url也會改變。
有時候我們會想改變文章的發布日期或分類，但不希望文章的url也跟著改變，或者有時候我們希望能自訂url的話。
此時只要在front-matter裡加一個參數permalink

```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2020-04-24 15:01:11 +0800
categories: jekyll update
permalink: my-url
---
```

此時url就會變成my-url這樣，就算文章的分類、日期改變，也不會影響文章的連結

permalink也可以用跟nodejs一樣的變數模式

> permalink: /:categories/:year/:title

此時網址url會是定義在front-matter的categories, year, title這些key的值
甚至還能夠設定網站的extension，給予.html或者.php結尾之類的

> permalink: /:categories/:title.html

### Custom front-matter

因為像author之類的front matter每篇都依樣，不想每篇都要加或者要改的時候每篇都要改，所以用了一個config的方式讓使用者改的時候能夠直接改一個config檔就可

#### config.yml

```
defaults:
- 
	scope:
	path: ""	# 決定這些更改的front-matter要改哪些檔案(如果是posts或空字串，那就是所有部落格文章都會改到)
	type: "post" # 所有post資料夾底下的檔案都要用post的layout
	values:
		layout: "post"	# 所有path下面的檔案的front-matter都會用post layout
		title: "hi"	# 設定每篇文章的title
```

## Themes

最重要的單元，改變網站的主題!!!

Theme定義在config.yml
default的theme為 minima，在config.yml裡面

> theme: minima

找到自己喜歡的theme後依據該github的安裝方法安裝通常方法為先在config.yml裡加入該library，並打下面的指令安裝所有缺的library
然後install，接著再config.yml的theme裡面改成該theme

> bundle install 

### Layout 更改

如果改用新的theme後，很有可能網站執行後跑不出東西，原因是因為新的theme不一定會有原本default theme的layout
也就是說，新的layout可能不知道layout: pages, layout: post 這種東西，此時就要回去看git repo該theme的規範

#### 主題網站

以下兩個為Jekyll theme的網站
[Jekyll Themes](http://jekyllthemes.org/)
[ruby-gem-Jekyell Theme](https://rubygems.org/search?utf8=%E2%9C%93&query=jekyll-themes)


## Customize layout

Jekyll default theme會有兩個layout: post和pages
可以透過覆寫或客製化新的layout

作法:
1. 先建立一個 _layouts資料夾
2. 在_layouts建立html檔(如果建立post.html，只要用post layout的文章都會使用這個html template)
3. 使用{{content}} 這種jinja的template格式引入文章到html裡面，只要再html裡面用這種格式，瀏覽網頁時就會把文章內容放到content裡面，content相當於一個變數，引入文章內容

```lang=html
<h1>post</h1>
{{content}}
```

### 進階用法

像網站依樣，他可以layout疊layout，就是在html檔上面新增一個front-matter，然後在裡面限定layout:XXX，這樣有點像import另一個layout，基於import的layout在建立新的layout，一層疊一層

用layout.XXX存取此網頁的fromt-matter參數
用page.XXX存取import進來的文章的front-matter

```lang=html
---
layout "wrapper"
author Jimmy
---

<h1>post</h1>
<h3>layout.author</h3>
<h4>page.XXX</h4>	<!-- 存取文章裡面的front-matter的變數 -->
<h5>{{site.title}}</h5> <!-- 存取config.yml裡面的設定 -->
{{content}}
```

更詳細的用法可以參考官方文件: https://jekyllrb.com/docs/variables/

## include

在網頁中可以include其他html檔當作header, footer, sidebar之類的
創一個_include的資料夾，裡面放html檔，可以是header, footer之類的
接著在layout資料夾裡的html include這些include裡面的html檔

layout/html

```
<body>
{% include hedaer.html color=blue%}	<!-- 引入header.html color=blue等於傳進去header.html的參數-->
{{ content }}
```

include / html

```
<h1 style="color: {{include.color}}">{{site.title}}</h1>
```