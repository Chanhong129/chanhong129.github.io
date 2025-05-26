---
title: PostgreSQL, pgAdmin4 Backup/Restore
date: 2025-05-08 10:00:00 +0900
categories: [Programming, Database]
tags: [Program, database, postgres, pgadmin4, backup, restore]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## Backup

1. 원하는 데이터베이스 우클릭 - Backup   
![Backup](https://i.ibb.co/1tyhPz5K/image.png)

2. 파일 위치를 지정하고 Encoding UTF8 설정   
![Backup-General](https://i.ibb.co/YT1b2mQk/image.png)


3. Data Options   
![Backup-DataOptions](https://i.ibb.co/PvHJfcL3/image.png)
 - **Pre-data**: 테이블, 스키마, 인덱스 등의 **정의(구조)**를 백업할지 여부를 설정합니다.   
이 섹션을 활성화하면 테이블이나 스키마 정의와 같은 사전 데이터 구조를 포함합니다.
 - **Data**: 데이터베이스의 **실제 데이터**를 백업할지 여부를 설정합니다. 활성화되면 테이블에 저장된 데이터를 백업합니다.
 - **Owner**: 데이터베이스 객체의 **소유자 정보**를 백업에서 제외합니다. 복원 시 객체의 소유자를 자동으로 설정하지 않도록 할 수 있습니다.
 - **Privileges**: 테이블, 스키마 등에 설정된 **권한(Privileges)**을 백업에서 제외합니다.   
GRANT, REVOKE 등의 권한 관련 정보가 백업에 포함되지 않습니다.

4. Query Options   
![Backup-QueryOptions](https://i.ibb.co/tTD79tFk/image.png)
 - **Include CREATE DATABASE statement**: 백업 파일에 CREATE DATABASE 명령을 포함할지 여부를 결정합니다. 이 명령이 포함되면, 복월할 때 데이터베이스 자동으로 생성됩니다.

5. **Use Column INSERTS**: 활성화하면 INSERT 명령에 열(column) 이름을 포함합니다.   
 기본적으로 열 이름 없이 백업되지만, 활성화하면 INSERT INTO table_name (column1, column2, ...) 형식으로 백업됩니다.   
![Table Options](https://i.ibb.co/49sgt1S/image.png)

6. **Verbose messages**: 백업 작업 중에 자세한 로그를 출력하도록 설정합니다.   
 이 옵션을 활성화하면 백업 과정에 대한 더 많은 정보가 로그에 표시됩니다.   
![Backup-Options](https://i.ibb.co/W4FW4Vhk/image.png)

## Restore
1. 원하는 데이터베이스를 우클릭 - Restore   
![Restore](https://i.ibb.co/twBPBSsb/image.png)

2. 파일 위치 지정   
![Restore-General](https://i.ibb.co/XffnVDjj/image.png)

3. Data Options
 [x] Pre-data   
 [x] Data   
 [x] Owner   
 [x] Priviliges   

4. Restore 버튼 클릭