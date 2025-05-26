---
title: PostgreSQL, 외부 서버 접속
date: 2025-05-08 14:00:00 +0900
categories: [Programming, Database]
tags: [Program, database, postgres, pgadmin4, connect]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## 외부 서버에서 설정
1. C:/Program Files/PsotgreSQL/15/data/**pg_hba.conf** 파일 수정

```
...

IPv4 local connections:
host all all 127.0.0.1/32 scram-sha-256
host all all 0.0.0.0/0 m5	<< 이 부분 추가

...
```

2. C:/Program Files/PsotgreSQL/15/data/**postgresql.conf** 파일 수정

```
...

listen_address = '*'
```

3. 외부 서버의 방화벽을 5432 포트를 허용하거나 해제해야한다.

## 클라이언트 pgAdmin 에서 설정

1. 새로운 Server Group 생성 및 이름 설정
 - 새로운 Server Group 생성   
![새로운 Server Group 생성](https://i.ibb.co/tTY5RWd2/image.png)

 - 이름 설정   
![이름 설정](https://i.ibb.co/fY16Z6fW/image.png)

2. 서버 등록
 - 새로운 Server Group 에 Server 등록   
![새로운 Server 등록](https://i.ibb.co/Cjj4FHQ/image.png)

 - 서버 이름 설정   
![서버 이름 설정](https://i.ibb.co/kkTzg8G/image.png)

 - 연결 정보 입력   
![서버 연결 정보](https://i.ibb.co/C5BhqpX6/image.png)

 ## 주의 사항
만약 외부 서버의 설정이 안되있거나 접속할 수 없는 상태라면 **다음 에러 발생**   
![접속 에러](https://i.ibb.co/v60DTrd6/image.png)