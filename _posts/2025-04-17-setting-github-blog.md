---
title: Github Blog 기본 설정 (Chirpy)
date: 2025-04-17 11:15:33 +0900
categories: [Github, Blog]
tags: [github, blog, chirpy]
description: jekyll, chirpy, github, blog
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> Github Blog 를 개설하려면 설정해야 하는 것들

## 기본 설정
```yaml
# The language of the webpage › http://www.lingoes.net/en/translator/langcode.htm
lang: ko-KR   # 상단 주소 확인

# Change to your timezone › https://kevinnovak.github.io/Time-Zone-Picker
timezone: Asia/Seoul  # 상단 주소 확인

# jekyll-seo-tag settings › https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md
title: Till The End Dev # 좌상단 아바타 밑에 큰 글씨
tagline: 집중!!! # title 밑에 글씨
description: >- # 검색어를 위한 설정
  TillTheEnd.Dev
url: "https://chanhong129.github.io"  # 본인 Git 블로그 주소에 맞게 변경
github:
  username: Chanhong129 # 본인 github 이름, 좌하단 링크 정보에서 사용됨
social:
  name: Chanhong129 # 하단 Footer에서 사용됨 (Powered by 있는곳)
  email: Chanhongv@gmail.com # 본인 이메일 주소, 좌하단 링크 정보에서 사용됨
```

## 좌하단 링크 정보들 변경
```yaml
# _data/contact.yml
- type: tistory
  icon: "fa-solid  fa-t"  # https://fontawesome.com/icons 에서 검색해서 원하는 것으로 바꾸기
```
```yaml
#_config.yml 에 내용 추가
tistory:
  url: https://ifeeligood.tistory.com/
```

```html
<!-- includes/sidebar.html -->
<!-- 태그 정확하지 않음. 정확한 코드는 직접 확인할 것. -->
for entry in site.data.contact
    case entry.type
        when 'github', 'twitter'
            capture url
                https://.com/
            endcapture
        <!-- 이런식으로 추가 -->
        when 'tistory'
            capture url
                site[entry.type].url
                <!-- site ==> _config.yml  //  entry.type ==> tistory, github 등등 -->
```

## 좌상단 아바타 이미지 변경
```yaml
#_config.yml
avatar: /assets/img/MorningBBAng.png  # 원하는 Local, CDN 등등의 주소로 설정하면 된다.
```

> [Github blog 만들기 포스트](https://chanhong129.github.io/posts/create-github-blog/) 에 이어 이 포스트를 따라했다면
> Git Add, Commit, Push 후 내가 설정한 URL 로 접속이 가능하다.
> 이렇게 쉽게 나만의 블로그를 배포 가능 !
> 도메인만 구매한다면 더 다양하게 사용 가능할 거 같다.