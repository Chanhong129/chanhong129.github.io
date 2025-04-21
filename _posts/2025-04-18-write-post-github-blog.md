---
title: Github Blog Post 작성하기 (Chirpy)
date: 2025-04-18 12:15:33 +0900
categories: [Github, Blog]
tags: [github, blog, chirpy, post]
description: jekyll, chirpy, github, blog
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> 블로그를 만들었다면 포스트를 작성해야겠지? (광역 도발)

## Post 작성하기
**_post** 폴더에 **yyyy-mm-dd-title.md** 로 파일을 만들면 포스트 작성이 가능하다. 파일은 형식에 맞게 만들어야 한다.
> yyyy-mm-dd-title.md
> ex. 2025-04-17-Github Blog Post 작성하기.md

## Front Matter 작성하기
Tistory 나 Naver blog 등의 글들을 보면 카테고리, 태그, 타이틀 등이 있는 것을 볼 수 있다.

그럼 Github Blog Chirpy 테마에서도 되는가?
물론이다. Post 를 작성할때 **Front Matter** 를 작성하여 설정할 수 있다.
> Front Matter 는 Post 작성시 가장 위에 입력되는 문단이다.

많은 속성들이 있지만 그 중 자주 사용되는 것들은 아래와 같다.

---
```markdown
---
title: "글 제목"
date: YYYY-MM-DD HH:MM:SS +0900
categories: [카테고리1, 카테고리2]
tags: [태그1, 태그2, 태그3] #tag 는 소문자여야 하며 여러 개 설정  가능
description: "요약 설명 (SEO, 썸네일에 사용됨)"
image:
&emsp;src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
&emsp;width: 1000   # in pixels
&emsp;height: 400   # in pixels
&emsp;alt: image alternative text
---
```
---

## 내용 작성
Front Matter 아래 부분에는 포스트 내용을 작성하면 된다.<br>
Mark down 텍스트 문법을 따라 작성하면 되지만 미리 보기 없이 작성하는 것은 쉽지 않기 때문에 미리보기를 지원해주는 사이트에서 작성을 한 후 붙여넣기 하여 포스트를 완성한다.

[https://stackedit.io/](https://stackedit.io/) 를 이용하면 Mark down 을 미리보기를 보며 작성을 할 수 있어서 편하다.
지금 이 포스트도 stackedit 를 이용하여 작성 했다.

Chirpy 에서는 내부적으로 목차를 지원 하고 있어서

\## (목차 생성) <br>
\### (하위 목차 생성)

으로 작성하면 목차가 자동으로 생성된다.

> 이미지는 [https://imgbb.com/](https://imgbb.com/) 에 업로드를 하고 링크를 참조하는 방식으로 사용하고 있다.

 - 한 장당 32MB 이하 파일이면 무료로 업로드 가능
 - 업로드 개수 제한이 없음 (실제 사용자는 무제한처럼 사용 가능)
 - 영구 저장도 가능

## 배포하기
[Github Blog 만들기 (Chirpy)](https://chanhong129.github.io/posts/2025-04-16-create-github-blog/) 포스트에서 Git add, commit, push 를 사용한 것을 이용하면 된다.

수정하거나 새로 추가된 모든 파일을 추가 하는 작업
```shell
> git add .
```

커밋을 진행하는 작업
updates 부분은 아무거나 작성해도 된다.
```shell
> git commit -m 'updates'
```

푸시를 진행하는 작업
```shell
> git push
```

## 배포가 잘 되었는지 확인
Git Actions 에서 내가 Commit 한 제목이 업로드 되었는지 확인 하고 **빨간색**인지 **노란색**인지 **초록색**인지 확인한다.

**노란색**: 업데이트 중이니 기다려야함. <br>
**초록색**: 정상 업로드 되었고 배포가 완료 되었음. <br>
**빨간색**: 문제가 있음

* 빨간색의 경우 내용을 보면 어떤 문제가 있는지 알 수 있고 그 부분을 수정하여 다시 Git add, commit, push 를 해주어 해결 하면 된다.

**미리 로컬에서 확인하고 업로드 하는 것도 편하다.**

**Repository 에서 git bash 를 열고 jekyll serve 를 입력한 후 127.0.0.1:4000 사이트에서 확인 할 수 있다.**

> 포스트를 자주 작성하자 !!!