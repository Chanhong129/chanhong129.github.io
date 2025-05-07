---
title: Github Blog 댓글 기능 추가하기 (giscus)
date: 2025-05-01 14:00:00 +0900
categories: [Github, Blog]
tags: [github, blog, giscus, comments]
description: Github Blog Comments
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> Github blog 의 댓글 기능을 지원하는 대표 시스템은 **disqus, utterances, giscus** 등이 있다.   
> 이 포스팅에서는 **giscus** 에 대해 알아봅니다.

## Github 에서 Discussions 활성화하기
**Github Blog Repository** - **Settings** - **General** - **Features** 에서 **Discussions** 를 체크하여 활성화 한다.

**Discussions** 탭이 활성화되고 들어가보면 기본 **Categories** 가 등록 되어있다.   

필자는 새로운 카테고리 **Comments** 추가하였다.   
**Discussion Format** 은 **Announcement** 를 선택하였다.

## giscus 앱 설치
[giscus](https://giscus.app/ko) 에 들어가 **Repository 를 giscus 와 연결**해주는 작업을 해야한다.   
1. **저장소** 연결합니다.
- **공개** 저장소여야 합니다. 그렇지 않으면 방문자들은 **Discussion** 을 볼 수 없습니다.
- **giscus** 앱이 설치되어 있어야 합니다. 그렇지 않으면 방문자들은 댓글과 반응을 남길 수 없습니다.
- **Discussions** 기능이 해당 저장소에 활성화 되어 있어야 합니다.

2. **Discussions** 카테고리
- 내가 만든 Comments 로 연결

3. **기능**
- 원하는 기능 활성화

4. **테마**
- GitHub Light 로 설정 또는 원하는 테마로 변경

5. 나머지는 그대로 둬도 무방하다.

6. giscus 사용에 **script** 가 생성된다.   
**script** 에서 ```data-repo-id```, ```data-category-id``` 가 필요하다.

## _config.yml 수정하기
```yml
comment:
  provider: giscus
giscus:
  repo: chanhong129/chanhong129.github.io
  repo_id: "R_kgDOOaYvpQ" #script 의 data-repo-id
  category: "Comments" #내가 만든 Discussion Catogry
  category_id: "DIC_kwDOOaYvpc4Cp2hm" #script 의 data-category-id
```

**_깃허브 블로그 댓글 기능 추가하기 끝._**

