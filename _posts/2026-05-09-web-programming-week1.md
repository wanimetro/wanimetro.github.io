---
title: "Web Programming week1 0509"
date: 2026-05-09
categories: [MyStudy, Web_Programming_Paris]
tags:
  - HTML
  - CSS
  - JavaScript
---

# 1주차(0509)

# 인터넷과 웹

클라이언트(사용자가 쓰는 컴퓨터, 크롬)

서버(클라이언트가 요청하면 결과물 만들어주는 것)

## 1. HTML

웹페이지의 뼈, 태그를 열고 닫는 마크업 언어

암기과목..이해<암기

마크업 언어: SGML, HTML, XML

태그로 열고 닫는 언어

<태그> 내용 </태그>

HTML: 마크업 언어

1. 코드 작성 시에는 <태그>열고 </태그> 닫는 식으로 타이핑
2. 안에 있는 소스는 들여쓰기→들여쓰기는 Tab 키로
3. 여러줄 들여쓰기를 할 때에는 블록 씌운 후 Tab
4. 왼쪽으로→shift+tab

```html
<!DOCTYPE html>   //필수
<html>  //웹페이지 시작태그
<head>  //문서제목, css(디자인),js(동작), 메타데이터(부가설명)
	<meta charset="UTF-8">
	<title>간단한 웹 페이지</title>    //문서제목
	<link rel="stylesheet" href="styles.css">
</head>
<body>  //실제 문서의 본문
	<!-- 주석=메모 -->
	<!--
	오소원
	웹공부
	-->
	<h1>환영합니다!</h1>
	
	<a href="http://www.naver.com" target="_blank">
	네이버로 이동해라
	</a>
	<div id="box"></div>
	<script src="script.js"></script>
</body>
</html>

```

태그: html을 구성하는 가장 큰 구성요소

html 문서는 “태그”들로 구성

태그에는 여러가지 종류

html-html문서다라는 것을 나타내는 태그

head-타이틀, 메타태그, css, js

title-웹 페이지 이름

body-실제 웹 페이지, 보이는 몸통

h1~hN-문서 내부에서 출력

```<a href="http://www.naver.com" target="_blank>네이버로 이동해라</a>```

href: ~링크로 이동해라, 이동할 페이지 주소

target=”_blank”: 새 페이지에서 새로운 링크 열기

target=”_self”: 기존 페이지에서 새로운 링크 열기

a-하이퍼링크 (클릭시 페이지 이동)

태그에 속성값 표기할때 “ “ 쌍따옴표 권장

ex) target=”_blank”, style=” “

<br/> 줄바꿈 태그

<p> </p> 문단

엔티티코드

&lt; html문서 안에서 < 기호(그냥 < 이렇게 쓸 경우 태그로 인식함)

&gt; html문서 안에서 >기호

&quot; html문서 안에서 큰따옴표

&apos; html문서 안에서 작은 따옴표

&nbsp; html문서 안에서 띄어쓰기(띄어쓰기 여러번 하려면 &nbsp를 여러개 써야함)

&amp; html문서 안에서 & 이 기호

<hr/> 수평선태그, 열고 닫는 거 없음.. 수평선 그어줌

문자꾸미기 태그

<b> 굵게

<i> 글자 기울이기

리스트 태그

 <ul>                                        동글뱅이 리스트

<li>첫번째 </li>

</ul>

<ol type=”I” start=”3”>       로마자type, 숫자3부터 시작

<li>첫번째 </li>

</ol>

테이블(표)

<table>

<tr>       한 row

<td>음료</td><td>1500원</td><td>1개</td>       <td>는 열

</tr>

</table>

<th colspan=”3”> 3개의 열을 합침


### 메타데이터

```<meta name=”keyword” content=”web programming, HTML5, CSS3, javascript”>

해석: 인터넷에 키워드 web programming을 검색하면 이 사이트가 뜨도록

<meta name=”author” content=”sowon oh”>

해석: 작가 이름 넣기

<meta charset=”UTF-8”> 한글 문자 set
```

고급 태그…

<img/> 이미지태그
```
<img src="seojun.jpg" alt="서준"/> 태그 안에 이미지를 가져와서 출력해라, alt는 설명
```
## 2. CSS

디자인(글씨크기, 색깔..)

암기과목

id는 중복되지 않는 값, 고유의 값

스타일에 !important 를 추가하면 우선 적용(가급적이면 사용x)

실습

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>웹 수업</title>
	<link rel="stylesheet" href="style.css">
</head>

<body>
	<div id="sec-1">
		1번 구역</br>
		1번 구역 내용</br>
		1번 구역 내용
	</div>
	<div class="inner-sec" id="sec-2" style="color: #FF0000 !important;">
		내부 1구역
	</div>
	<div class="inner-sec">
		내부 2구역
	</div>
	<div class="inner-sec">
		내부 3구역
	</div>
</body>
</html>

```

```css
#sec-1{
		width: 300px;     /*가로*/
		height: 200px;     /*세로*/
		line-height: 40px;  /* 줄 간격*/
		
		font-size: 20px;   /*글자 크기*/
		font-weight: bold;  /*글자 굵기*/
		color: #3d00f2;     /*글자 색상 rgb, Hex */
		
		border: 3px solid #FF0000;   /*테두리 굵기/종류/색상 */
		border-radius: 50px;      /*테두리 둥글게*/
		
		margin: 10px;   /*바깥부분과의 여백*/
		padding: 10px;  /*안쪽부분과의 여백*/
		padding-top: 20px;
		padding-bottom: 15px;
		padding-left: 10px;
		padding-right: 25px;
	}
#sec-2{     /*아이디는 # */
		color: #rgb(0,255,0);
	}
.inner-sec{   /*클래스는 . */
		font-size: 40px;
		margin: 20px;
		color: #0000FF;
	}
```
```
<input type="radio" name="login-type" value="1">1번</br>
<input type="radio" name="login-type" value="2">2번</br>
<input type="checkbox" name="login-type2" value="2">체크</br>
<button type="button"><img src="img/cat.jpeg"/></button>
```

## 3. JavaScript

동작

서버에서 동작x, 크롬 내부에서 자체적으로 동작

예를 들면..

HTML→로그인 입력창 생성

CSS→예쁘게 꾸밈

JavaScript→버튼 누르면 반응하게 만듦

사용자가 웹사이트에 들어가면, 서버가 HTML/CSS/JS 파일을 보냄→크롬이 파일을 받음→크롬 내부 엔진이 JS를 실행함

------프론트

## 4. PHP

xampp설치

프론트(HTML, CSS, JS): 내 컴퓨터, 클라이언트(크롬)에서 실행

PHP: 서버에서 실행, 아파치(서버 프로그램)

프론트엔드만으로는 그냥 “화면”만 만들 수 있음.

벡엔드는 로그인, 회원가입, DB저장, 게시판, PHP실행이 필요함->웹서버(Apache), 데이터베이스(MySQL), PHP 실행환경이 필요함→XAMPP가 이것을 한번에 설치해주는 패키지 

XAMPP들어가서 Apache랑 MySQL start해놓고 

[localhost / 127.0.0.1 | phpMyAdmin 5.2.1](http://localhost/phpmyadmin/)

여기 들어가기

c드라이브>xampp폴더>htdocs

데이터베이스

쿼리문

1. INSERT: 데이터 삽입 문법

INSERT INTO `member` (`user_id`, `user_pw`, `user_name`, `user_email`, `user_reg_datetime`) VALUES ('sowon', '1234', '오소원', 'sow@naver.com', '2024-05-17 16:08:13');

1. DELETE: 데이터 삭제 문법

"DELETE FROM member WHERE `member`.`user_id` = 'sowon'"

1. UPDATE: 데이터 수정 문법

[UPDATE](http://localhost/phpmyadmin/url.php?url=https://dev.mysql.com/doc/refman/8.0/en/update.html) `member` [SET](http://localhost/phpmyadmin/url.php?url=https://dev.mysql.com/doc/refman/8.0/en/set.html) `user_pw` = 'hixing0808' WHERE `member`.`user_id` = 'xingxing';

### delete, update 문에서 where 빠졌다→모든 행을 삭제, 수정

1. SELECT: 조회

SELECT* FROM ‘member’;

MySQL 패스워드 변경

1. XAMPP Control Panel 에서 MySQL Start ★ 중요
2. 명령 프롬프트에서
cd C:\xampp\mysql\bin
3. mysql -uroot -p
4. Enter Password 가 뜨면 그냥 앤터
5. 아래에 있는 것 순서대로
use mysql;

ALTER USER 'root'@'localhost' IDENTIFIED BY ‘sowon57';

flush privileges;

exit: 종료

과제 이용코드

SELECT * FROM `member` WHERE
`user_id` = 'admin' AND `user_pw` = '1234';

xampp열고 이렇게 치기

[http://127.0.0.1/login.html](http://127.0.0.1/login.html)

과제
/* 프론트 */

1. 로그인 페이지
2. 회원가입 페이지
3. 관리자 페이지
디자인 변경
-> CSS 파일만 수정

/* 백앤드 */

1. login_check.php 구현
- login.html 로 부터 받은 아이디 패스워드로 DB에서 존재하는지 확인 후, 존재한다면 [http://127.0.0.1/admin_users.php](http://127.0.0.1/admin_users.php) 로 페이지 이동
1. 회원가입 로직 구현
2. 관리자페이지(admin_users.php) 에서 회원 수정 구현
(회원 수정 인터페이스는 register.html 참조 하여 php 파일로

## 5. MySQL

-----백엔드
