## 2020-03-30

### 01. Jekyll 기본 테마인 Minima Theme 다운로드
### 02. Layout, Includes, Assets, Sass 파일 가져오기
```c++
bundle show minima // Minima 테마의 파일들이 저장된 위치를 보여준다.
```
### 03. Bootstrap을 이용하기 위해 Head에 CSS 링크를, Body에 JavaScript 링크를 넣어주기.
```html 
<!-- _includes/head.html 의 Head 태그 안에 코드 삽입-->
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
```
```html
<!-- _layouts/default.html 의 Body 태그 마지막 부분에 코드 삽입 -->
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
```

## 2020-03-31

### 01. Navigation Bar 변경

기존 Navigation Bar는 모든 카테고리가 나타나 내가 원하는 카테고리만 보여줄 수 있는 기능을 추가하고 싶어서 변경하게 되었다.
<br>Bootstrap의 Navigation Bar에 적용되는 클래스를 이용할 계획이며, 이후 Dropdown 기능을 추가하여 서브 카테고리를 선택할 수 있도록 할 예정이다.

```html
<!-- ../_includes/nav.html  Navigation Bar 관련 코드를 따로 정리 -->
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
<!-- /..includes/header.html -->
<header class="site-header" role="banner">
<!-- nav.html에 작성한 코드를 header 태그안에 포함 -->
	{% include nav.html %}
</header>
```

```markdown
<!-- /index.markdown -->
---
layout: home
title: Home
appear: true
order: 1
permalink: /
---
```

```markdown
<!-- /project/index.md -->
---
layout: home 
title: Project
appear: true
order: 2
permalink: /project
---
```
![Navigation 적용](https://user-images.githubusercontent.com/17156386/78041726-0d202100-73ac-11ea-94fe-5bbead6e969f.png)
