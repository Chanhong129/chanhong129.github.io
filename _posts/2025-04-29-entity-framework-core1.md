---
title: Entity Framework Core 사용법 (1/2)
date: 2025-04-29 13:00:00 +0900
categories: [Programming, EFCore]
tags: [c#, wpf, efcore, entity, framework, entityframework]
description: Entity Framework Core
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> C# 응용 프로그램에서 데이터베이스와 연동할 때, SQL 스크립트를 직접 작성하는 방법도 있지만, 더 고급스로운 방법으로 Entity Framework Core 를 사용할 수 있습니다. Entity Framework Core를 사용하면 클래스 생성만으로 데이터베이스와 테이블을 만들 수 있으며, CRUD 작업도 클래스를 객체를 통해 수행할 수 있습니다.

## 1. 프로젝트 생성
Visual Studio 에서 새로운 C# 프로젝트를 생성합니다.

## 2. Nuget Package 설치
- ```Microsoft.EntityFrameworkCore```
- ```Microsoft.EntityFrameworkCore.Tools```
- ```Npgsql.EntityFrameworkCore.PostgreSQL``` (PostgreSQL을 사용하는 경우)

> 다른 데이터를 사용하는 경우, 해당 데이터베이스에 맞는 패키지를 설치하시면 됩니다.

## 3. Entity Class 생성
데이터베이스 테이블과 매핑될 클래스를 생성합니다:

```cs
public class Language
{
    [Key]
    public int Id { get; set; }
    public string Name { get; set; }
    public string Korean { get; set; }
    public string English { get; set; }
}
```

- ```Id```는 기본 키로 설정되며, INTEGER 타입으로 생성됩니다.
- ```Name```, ```Korean```, ```English```는 TEXT 타입으로 생성됩니다.

## 4. DbContext 클래스 생성
데이터베이스 컨텍스트 클래스를 생성하여 데이터베이스 연결을 설정합니다:

```cs
public class EFCoreDbContext : DbContext
{
    public DbSet<Language> Languages { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseNpgsql("Host=127.0.0.1;Port=5432;Database=SampleDatabase;Username=postgres;Password=123!");
    }
}
```

- ```Languages```는 ```Language``` 엔티티와 매핑됩니다.
- ```OnConfiguring``` 매서드에서 데이터베이스 연결 문자열을 설정합니다.

## 5. 코드 작성 후 데이터베이스 및 테이블 생성 (Code First)
```EntityFrameworkCore.Tools``` 패키지가 설치되어 있는지 확인합니다.

1. 해당 프로젝트를 시작 프로젝트로 설정하고, 패키지 관리자 콘솔에서도 기본 프로젝트로 설정합니다.   
2. 패키지 관리자 콘솔에서 다음 명령어를 실행하여 도움말을 확인할 수 있습니다:

```powershell
get-help entityframeworkcore
```

3. 다음 명령어들을 사용하여 데이터베이스를 생성하고 마이그레이션을 적용합니다:
- ```Add-Migration Init```: 코드 변경 후 실제 데이터베이스 업데이트 전 커밋하는 작업입니다.
- ```Update-Database```: 커밋된 마이그레이션을 실제 데이터베이스에 적용합니다.

> 명령어를 사용하기 위해서는 프로젝트에 빌드 오류가 없어야 합니다.