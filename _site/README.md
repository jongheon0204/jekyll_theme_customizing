## 2020-03-30

### 01. Jekyll 기본 테마인 Minima Theme 다운로드
### 02. Layout, Includes, Assets, Sass 파일 가져오기
```c++
bundle show minima // Minima 테마의 파일들이 저장된 위치를 보여준다.
```
### 03. Bootstrap을 이용하기 위해 Head에 CSS 링크를, Body에 JavaScript 링크를 넣어주기.
```html 
<!-- ./_includes/head.html 의 Head 태그 안에 코드 삽입-->
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
```
```html
<!-- ./_layouts/default.html 의 Body 태그 마지막 부분에 코드 삽입 -->
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
```

## 2020-03-31

### 01. Navigation Bar 변경

기존 Navigation Bar는 모든 카테고리가 나타나 내가 원하는 카테고리만 보여줄 수 있는 기능을 추가하고 싶어서 변경하게 되었다.
<br>Bootstrap의 Navigation Bar에 적용되는 클래스를 이용할 계획이며, 이후 Dropdown 기능을 추가하여 서브 카테고리를 선택할 수 있도록 할 예정이다.

```html
<!-- ./_includes/nav.html  Navigation Bar 관련 코드를 따로 정리 -->
<nav class = "navbar navbar-expand-lg navbar-dark bg-dark"> 

<!-- 화면 크기가 작아 졌을때 navbar-brand만 보인다 -->
	<a class = "navbar-brand" href="#">{{site.title}}</a>
	<button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
		<span class="navbar-toggler-icon"></span>
	</button>

	<div class="collapse navbar-collapse" id="navbarSupportedContent">
    		<ul class="navbar-nav mr-auto">

<!-- 최상위 카테고리들만 보여주기 위해 페이지의 appear가 true 인것만 순서대로 가져온다 --> 
			{% assign categories = site.html_pages | where_exp: "page","page.appear == true" | sort : 'order' %}
			{% for category in categories %}
				{% assign title = category.title %}

				{% if page.url != category.permalink %}
					{% assign link = category.permalink %}
				{% else %}
					{% assign link = '#' %}
				{% endif %}

				{% if page.url == '/' and title == 'Home' %}
					{% assign link = '#' %}
				{% endif %}

				{% if link == '#' %}
					{% assign li_class = 'active' %}
				{% else %}
					{% assign li_class = '' %}
				{% endif %}

<!-- 카테고리의 제목, 링크 등을 통해 하나의 li를 만든다 -->
				<li class = "nav-item {{li_class}}">
					<a class = "nav-link" href={{link}}>{{title}}</a>
			</li>{% endfor %}

		</ul>
	</div>
</nav>
```

```html
<!-- ./_includes/header.html -->
<header class="site-header" role="banner">
<!-- nav.html에 작성한 코드를 header 태그안에 포함 -->
	{% include nav.html %}
</header>
```

```markdown
<!-- ./index.markdown -->
---
layout: home
title: Home
appear: true
order: 1
permalink: /
---
```

```markdown
<!-- ./project/index.md -->
---
layout: home 
title: Project
appear: true
order: 2
permalink: /project
---
```
![Navigation 적용](https://user-images.githubusercontent.com/17156386/78041726-0d202100-73ac-11ea-94fe-5bbead6e969f.png)

## 2020-04-01

### 01. 이중 카테고리 작업을 위한 Navigation Bar 수정

카테고리들이 많아질 경우를 대비해서 카테고리들을 묶는 카테고리 즉 이중 카테고리를 적용하자는 생각이 들어 작업을 하게 되었다.
<br>Navigation Bar에 있는 카테고리를 이요하여 추가 작업을 해 주었는데 먼저 Home 부분과 다른 카테고리를 분류하는 작업을 통해 Home을 클릭시 메인 페이지로 이동하게 해 주었고, 다른 카테고리를 클릭시 Dropdown 방식으로 서브 카테고리들을 선택할 수 있도록 해 주었다.

```html
<!-- ./_includes/nav.html Navigation Bar {% if link == '#'} 구문 다음에 적용 -->

<!-- 해당 카테고리를 보고 클릭한 경우 active 클래스 적용 -->
{% if page.categories contains category.categories %}
	{% assign link = '#' %}
{% else %}
	{% assign link = category.permalink %}
{% endif %}

<!-- 카테고리의 이름이 Home이 아니면 Dropdown을 적용한다 -->
{% if title != 'Home' %}
	{% assign drop_class = 'dropdown' %}
{% else %}
	{% assign drop_class = '' %}
{% endif %}

<!-- 카테고리들을 네비게이션에서 표시하기 -->
<li class = "nav-item {{li_class}} {{drop_class}}">
{% if drop_class == 'dropdown' %}
<!-- Dropdown을 적용 -->
	<a class = "nav-link dropdown-toggle" id="navbarDropdown" role = "button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">{{title}}</a>
	<div class="dropdown-menu" aria-labelledby="navbarDropdown">
<!-- 이중 카테고리를 위해 해당 카테고리 이름을 이용하여 서브 카테고리들을 가져온다 -->
	{% assign sub_categories = site.html_pages | where: "parent",category.categories | sort: 'order' %}
	{% for sub_category in sub_categories %}
		<a class = "dropdown-item" href={{sub_category.permalink}}>{{sub_category.title}}</a>
	{% endfor %}
	</div>
{% else %}
<!-- 카테고리 이름이 Home인 경우에는 메인 페이지로 이동할 수 있도록 해준다 -->
	<a class = "nav-link" href="{{link}}">{{title}}</a>
{% endif %}
</li>
```

### 02. 카테고리에 속하는 post들만 보여주기

카테고리들을 분류해 주었다면 이제는 해당 카테고리를 클릭시 그에 해당하는 포스트들만 보여주는 작업을 진행 하였다.
<br>포스트는 생성시 자동으로 카테고리가 분류가 되니 현재 보고있는 카테고리에 속하는 포스트들만 가져오는 작업을 해주면 된다.
<br>이때, 메인 홈페이지와 분류하기 위해서 ../\_includes 폴더에 blog.html 레이아웃을 추가해 주었다.

```html
<!-- ./_inicludes/blog.html 파일 생성 후 작업 -->

<!-- ./_includes/default.html 파일의 {{content}}에 해당 파일 내용을 추가 -->
---
layout: default
---

<!-- 모든 포스터들을 생성일 반대 순서로 가져온다 -->
{% assign posts = site.posts | "sort: 'date' | reverse %}
<div class="home">

{%- if page.title -%}
	<h1 class="page-heading">{{ page.title }}</h1>
{%- endif -%}
	
{{ content }}
	
<!-- 포스트 개수가 1개 이상일 경우 -->
{% if psts.size > 0 %}
	<ul class="post-list">
	{% for post in posts %}
	<!-- 현재 페이지의 카테고리에 해당하는 포스트들만 나타낸다 -->
	{% if post.categories == page.categories %}<li>
		{% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
		<span class="post-meta">{{post.date | date: date_format}}</span>
		<h3>
			<a class="post-link" href="{{post.url | relative_url}}">{{post.title | escape}}</a>
		</h3>
	</li>{% endif %}
	{% endfor %}
	{%- if site.show_excerpts -%}
		{{ post.excerpt }}
	{%- endif -%}
	</ul>
{%- endif -%}
</div>
```

```markdown
---
layout: blog
title: Github Blog
parent: project
permalink: /project/github_blog
categories: [project,github_blog]
---
<!-- category 와 parent를 설정해 주어야 한다 -->
```

![Dropdown 적용](https://user-images.githubusercontent.com/17156386/78143634-7d40ac80-7469-11ea-962c-f5910bc903ba.png)

## 2020-04-02

### 01. Codepen에 있는 Blog Layout 적용

[Blog Layout 주소](https://codepen.io/russbeye/pen/MYeroq)
![레이아웃](https://user-images.githubusercontent.com/17156386/78219444-8037ad80-74fa-11ea-955b-c36be3a27afd.png)

[Codepen](https://codepen.io/)은 다른 사람들이 제작한 웹 사이트 레이아웃과 코드를 확인할 수 있는 사이트 입니다.
<br>기존의 포스트 목록을 변경해 주기 위해서 Blog Layout을 검색하여 찾던 도중 발견하였고 이 css를 제 Blog에 적용해 보았습니다.

Jekyll에 CSS를 적용하기 위해서는 SCSS파일을 만들어 CSS 코드를 넣어주면 자동으로 \_site의 assets폴더에 css파일이 생성이 됩니다. 

#### 순서
1. ./assets 폴더에 blog.scss 파일을 만들어 준 후, Codepen 사이트의 css 코드를 복사합니다.
2. 포스트들의 목록을 나타내는 ./\_layouts/blog.html 파일의 코드 Codepen 사이트의 html 코드를 참조하여 변경해줍니다.

```css
/* ./assets/blog.scss */
/* Codepen에서 가져온 코드에 변경 부분만을 현재 나타냈습니다 */

/* sass을 사용하기 위해서는 ---을 꼭 해주어야 한다 */
---
---

/* Minima 테마의 css와의 호환 문제로 id값을 통해 body에 배경색을 지정 */
#blog-body{
	background: #e5ded8;
	box-sizing: border-box;
}

/* background: url안에 보여주고 싶은 프로필 사진을 넣으면 된다  */
.blog-author h3::before,
.blog-author--no-cover h3::before {
	background: url("https://pbs.twimg.com/profile_images/1211311075871739905/UsXtIgky_400x400.jpg");
	background-size: cover;
	border-radius: 50%;
	content: " ";
	display: inline-block;
	height: 32px;
	margin-right: .5rem;
	position: relative;
	top: 8px;
	width: 32px;
}

/* 폰트의 굵기를 따로 조절해 주었다 */
.blog-title h1 a {
	color: #333;
	font-weight: 400;
}

/* 요약 부분과 밑에 부분이 너무 붙어있길래 padding-bottom으로 조절해 주었다. */
.blog-summary p {
	color: lighten(#333, 10%);
	padding-bottom: 10px;
	margin-bottom: 0px;
}

/* tag를 하나씩 보여주기 위해서 몇 가지 변경 */
.blog-tags ul {
	border-top: 1px solid lighten(#333,70%);
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
	list-style: none;
	margin-left: 0px;
	padding-top: 10px;
	padding-left: 0;
	padding-bottom: 10px;
	width: 80%;
}
```

```html
<!-- ./_layouts/blog.html -->
---
layout: default
---

{% assign posts = site.posts | sort: 'date' | reverse %}
<div class="home">

	{{ content }}
	
	{% if posts.size > 0 -%}
	{% for post in posts %}
	{% if post.categories == page.categories %}

<!-- 기존에 포스트 목록을 ul로 나타내는 대신 div로 나타내 주었다 -->
	<div class = "blog-container">
		<div class = "blog-header">
			<div class = "blog-author--no-cover">
				<h3>Jongheon</h3>
			</div>
		</div>
		<div class = "blog-body">
<!-- 포스트의 제목과 생성일을 표시 --> 
			<div class = "blog-title">
				<h1><a href="{{post.url|relative_url}}">{{post.title}}</a></h1>
				{% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
				<span class="post-meta">{{post.date | date: date_format}}</span>
			</div>
<!-- 포스트 요약을 표시 -->
			{% if post.summary %}
			<div class = "blog-summary">
				<p>{{post.summary}}</p>
			</div>
			{% endif %}

<!-- 포스트에 해당하는 태그가 존재할 경우 태그 표시 -->
			{% if post.tags.size > 0 %}
			<div class = "blog-tags"><ul>
				{% assign tags = post.tags %}
				{% for tag in tags %}
				<li><a href="#">{{tag}}</a></li>
				{% endfor %}
			</ul></div>
			{% endif %}
		</div>
	</div>
	{% endif %}
	{% endfor %}

		{%- if site.show_excerpts -%}
		{{ post.excerpt }}
		{%- endif -%}
	</ul>
	{%- endif -%}
</div>
```

![Post 변경](https://user-images.githubusercontent.com/17156386/78218817-5fbb2380-74f9-11ea-8982-8307bd9122f2.png)

## 2020-04-03

### 01. Home에 Portfolio 레이아웃 적용 

기존의 Home 화면은 모든 포스트 리스트를 보여주었고, 이를 보완하고자 하였습니다.
<br>생각을 하다 Codepen에서 Portfolio 레이아웃을 검색한 후, 사용할 만한 레이아웃을 3개 찾게 되었습니다.

1) [Inifinito Web Design Studio - Portfolio Zipline](https://codepen.io/ThiagoFerreir4/pen/eNMxEp)
2) [Portfolio Website](https://codepen.io/LewisBriffa/pen/BoyZYd?editors=1000)
3) [Horizontal Portfolio](https://codepen.io/WebSonick/pen/JkLcf)

목표는 위의 3 레이아웃중 어느 한 가지 레이아웃만을 사용하는게 아닌 한 가지 특징 씩 가져와서 사용하는 것입니다.

#### 순서

1. assets 폴더 안에 home.scss 파일을 만든 후, 1, 2번 레이아웃의 css 코드를 넣어준다.
2. \_layouts 폴더 안에 default.html에서 div class = "wrapper" 를 지워준다.
3. \_layouts 폴더 안에 home.html에서 section을 나누어 꾸며준다.

```html
<!-- ./_layouts/home.html -->
---
layout: default
---

<!-- 1번 레이아웃에서 첫 번째 section을 가져온다 -->
<div class = "intro-header">
	<div class = "bg-overlay">
		<div class = "container">
			
			<div class = "row">
				<div class = "col-lg-12">
					<div class = "intro-message">
						<h1>{{site.title}}</h1>
						<h3>Portfolio Web Page</h3>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>

<!-- 2번째 레이아웃에서 portfolio 관련 코드를 넣어준다 -->
<section id = "portfolio" class = "bg-light-gray bounds"> 
	<section>
		<div class="overlay"> 
			<div class="description">
				<p> Designed using Sketch, developed using PHP whilst incorporating the BEM methodology. A wide range of  hooks and filters were utilized to create an E-Commerece themed WordPress site that offers a high degree of  color customization to reflect company branding.  </p> 
				<h6>Tools<span class="glyphicon glyphicon-cog"></span></h6>
				<ul class="tools vertical-list">
					<li>PHP</li>
					<li>BEM</li>
					<li>SASS</li>
					<li>Sketch</li>
				</ul>
			</div>
			<img src="https://res.cloudinary.com/dj7k0lade/image/upload/v1526688745/codepen/portfolio/venditore.png" alt="Editorial Monitoring Automated Excel Screenshot">
			<h4 class="heading"><span class="glyphicon glyphicon-hand-up"></span>WordPress (WooCommerce) Theme: Venditore</h4>
		</div>
	</section>

	<section>
		<div class = "overlay">
			<div class = "description">
				<p>Designed using Sketch, developed using PHP whilst incorporating the BEM methodology. A wide range of 
											hooks and filters were utilized to create an E-Commerece themed WordPress site that offers a high degree of 
											color customization to reflect company branding.</p>
				<h6>Tools<span class="glyphicon glyphicon-cog"></span></h6>
				<ul class="tools vertical-list">
					<li>Python</li>
					<li>Selenium</li>
					<li>Test</li>
				</ul>
			</div>
			<img src="https://res.cloudinary.com/dj7k0lade/image/upload/v1526688773/codepen/portfolio/editorial_excel.jpg" alt="Editorial Monitoring Automated Excel Screenshot">
			<h4 class="heading"><span class="glyphicon glyphicon-hand-up"></span>Editorial Monitoring Job - Automated</h4>
		</div>
	</section>

</section>
```  

![home01](https://user-images.githubusercontent.com/17156386/78368828-32f13400-75ff-11ea-9fba-8b8b57e501c5.png)
![home02](https://user-images.githubusercontent.com/17156386/78368846-3b496f00-75ff-11ea-88ed-3b187ea7e7d5.png)

## 2020-04-04

### 01. Home.scss 코드 정리

497줄이나 되는 Home.scss 코드에서 필요 없는 부분들을 삭제 하고, 공통 코드들을 묶어 주었으며, 여러 레이아웃에서 가져온 css다 보니 이름이 동일한 id, class들에 대해서 수정작업을 진행하여 187줄 코드로 줄이는 작업을 하였다.

### 02. Portfolio에 Horizontal Scroll 적용

Home 화면의 Project 들을 나타내는 부분에서 Project의 개수가 4개 이상이 될 경우, 박스의 범위를 초과하여 개행된 후, Project Section이 보여지는 현상이 발생하게 되었다.
<br>개행이 된 후, Project Section이 보여지는것 보다는 Horizontal Scroll을 이용하여 개수가 많이 추가 되더라도, 스크롤을 통해서 볼 수 있게 하는게 좋을 것 같아 이를 번경해 주었다.

```css
/* ./assets/home.scss 의 horizontal scroll 적용 부분 */

#second_section{
	overflow-x: auto;
	overflow-y: hidden;
	white-space: nowrap;

/* id가 second_section인 div 안의 section에만 적용 */

	section{
		height: 100%;
		width: 330px;
		overflow: auto;
		margin-left: 10px;
		margin-right: 10px;
/* white-space 는 속성을 상속받기 때문에 다시 normal로 지정해 주어야 한다. */
		white-space: normal;
/* display를 inline-block으로 설정시, block이 가로 형태로 하나씩 생기게 된다 */
		display: inline-block;
		background-color: rgba(4,14,22,0.83);
	}
```

![home01](https://user-images.githubusercontent.com/17156386/78418936-b51f3e00-767b-11ea-92d0-3c429f4e0a8d.png)
![home02](https://user-images.githubusercontent.com/17156386/78418939-bbadb580-767b-11ea-8744-f7d1b3e33027.png) 

## 2020-04-08

### 01. Portfolio에 Project부분 수정

기존 Portfolio에는 Project를 추가하기 위해서는 사람이 코드를 일일이 입력해 줘야 했다.
<br>물론 동일한 구조이기 때문에 복사하면 되지만 그것보다는 jekyll을 이용하여 여러 정보만 입력하면 자동으로 생성이 되도록 해 주었다.

```html
<!-- ./_layouts/home.html 의 second_section 부분 수정 -->

<!-- 사람이 코드를 복사 붙여넣기가 아닌 md파일로 만들면 자동으로 반영이 되도록 한다-->
<section id = "second_section" class = "home_section">
<!-- 모든 html파일중 project 값이 true인 html 파일들을 가져온다 -->
	{% assign projects = site.html_pages | where_exp: "page","page.project == true" | sort : 'order' %}
	{% for project in projects %}
	<a href = "{{project.link}}" target="_blank">
		<section>
			<div class = "overlay">
				<div class = "description">
					<p>{{project.description}}</p>
					<h6>Tools<span class = "glyphicon glyphicon-cog"></span></h6>
					{% assign tools = project.tools %}
					<ul class = "tools vertical-list">
						{% for tool in tools %}
						<li>{{tool}}</li>
						{% endfor %}
					</ul>
				</div>
				<img alt = "{{project.title}}" src = "{{project.img_link}}">
				<h4 class = "heading"><span class = "glyphicon glyphicon-hand-up"></span>{{project.title}}</h4>
			</div>
		</section>
	</a>
	{% endfor %}
</section>
```

```markdown
---
project: true
title: Github Blog Theme
order: 1
link: https://github.com/jongheon0204/jekyll_theme_customizing
description : Github 웹 호스팅 기능에 정적 웹 사이트 생성기인 Jekyll을 결합하여 블로그 테마를 제작하게 되었다. <br>제작하게 된 이유는 기존의 Jekyll 테마는 많았지만 무료는 업데이트가 오랫동안 안된 경우가 많았으며, 최신의 예쁜 테마들은 유료여이기 때문이다. <br>Jekyll의 기본 테마인 Minima의 구조를 사용하고 여기에 Bootstrap의 여러 기능을 추가해 주었으며 Codepen에서 Blog에 사용할 만한 레이아웃들을 참조하였다.
tools : [Github, Jekyll, Bootstrap]
img_link : https://user-images.githubusercontent.com/17156386/78416684-8f873a00-7665-11ea-9e8c-74ff4e55dd6c.png
---
```

실제로 ./resources/project 폴더에 Github_Blog_Theme.md 파일을 만들어 위와 같이 입력해 준다면 Project가 자동으로 보여진다.
