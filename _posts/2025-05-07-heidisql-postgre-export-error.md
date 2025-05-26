---
title: Heidi SQL 에서 Postgre 내보내기 오류
date: 2025-05-07 14:00:00 +0900
categories: [Programming, Database]
tags: [Program, database, heidisql, postgres]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---


![데이터베이스 생성 체크](https://i.ibb.co/7x6fTZB2/image.png)

 데이터베이스 생성 체크를 하고 내보내기를 진행하면 오류가 발생한다.

![내보내기 오류](https://i.ibb.co/KcHL6xrb/image.png)

**오류: 구문 오류, "CREATE" 부근 LINE 1: SHOW CREATE DATABASE "public"** 가 발생한다.

데이터베이스 생성 체크를 해제 하면 해결 가능

![데이터베이스 생성 체크 해제](https://i.ibb.co/2YMPB6XC/image.png)