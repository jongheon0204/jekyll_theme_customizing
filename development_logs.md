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
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.0.0/css/font-awesome.min.css">
```
```html
<!-- _layouts/default.html 의 Body 태그 마지막 부분에 코드 삽입 -->
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
```
