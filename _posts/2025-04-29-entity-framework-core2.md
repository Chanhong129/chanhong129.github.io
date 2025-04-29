---
title: Entity Framework Core 사용법 (2/2)
date: 2025-04-29 14:00:00 +0900
categories: [Programming, EFCore]
tags: [c#, wpf, efcore, entity, framework, entityframework]
description: Entity Framework Core
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> 이전 글에서는 Entity Framework Core 의 기본적인 사용법에 대해 다루었습니다.    
> 이번 글에서는 보다 고급 기능과 성능 최적화 기법에 대해 알아보겠습니다.

## 1. Data Annotations 활용
Data Annotations 는 Entity Class 에 속성(Attribute)을 추가하여 데이터 모델을 구성하는 방법입니다. 이를 통해 데이터베이스 스키마를 세밀하게 제어할 수 있습니다.

**주요 Data Annotations**
- ```[Required]```: 해당 속성은 null을 허용하지 않습니다.
- ```[MaxLength(n)]```: 문자열의 최대 길이를 제한합니다.
- ```[MinLength(n)]```: 문자열의 최소 길이를 제한합니다.
- ```[StringLength(max, MinimumLength = min)]```: 문자열의 최소 및 최대 길이를 제한합니다.
- ```[Table("TableName")]```: 엔티티를 특정 테이블과 매핑합니다.
- ```[ForeignKey("NavigationProperty")]```: 외래 키를 지정합니다.

예를 들어, ```Product``` 크래스에 이러한 속성을 적용하면 다음과 같습니다:

```cs
[Table("Products")]
public class Product
{
    [Key]
    public int ProductId { get; set; }

    [Required]
    [MaxLength(100)]
    public string Name { get; set; }

    [ForeignKey("Category")]
    public int CategoryId { get; set; }

    public Category Category { get; set; }
}
```

## 2. 관계 설정: One-to-One, One-to-Many, Many-to-Many
EF Core 에서는 다양한 관계를 설정할 수 있습니다.

**One-to-One**

```cs
public class User
{
    public int Id { get; set; }
    public UserProfile Profile { get; set; }
}

public class UserProfile
{
    [Key, ForeignKey("User")]
    public int UserId { get; set; }
    public User User { get; set; }
}
```

**One-to-Many**

```cs
public class Category
{
    public int Id { get; set; }
    public ICollection<Product> Products { get; set; }
}

public class Product
{
    public int Id { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }
}
```

**Many-to-Many (EF Core 5.0 이상)**

```cs
public class Student
{
    public int Id { get; set; }
    public ICollection<Course> Courses { get; set; }
}

public class Course
{
    public int Id { get; set; }
    public ICollection<Student> Students { get; set; }
}
```

## 3. Fluent API를 통한 모델 구성
Fluent API는 ```OnModelCreateing``` 매서드 내에서 모델 구성을 세밀하게 제어할 수 있는 방법입니다.

```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .IsRequired()
        .HasMaxLength(100);

    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId);
}
```

Fluent API는 Data Annotations보다 우선순위가 높으며, 복잡한 관계나 제약 조건을 설정할 때 유용합니다.

## 4. 성능 최적화 기법
### AsNoTracking
읽기 전용 쿼리에서는 변경추적을 비활성화하여 성능을 향상시킬 수 있습니다.

```cs
var products = context.Products.AsNoTracking().ToList();
```

### Compiled Queries
자주 실행되는 쿼리는 컴파일하여 성능을 향상시킬 수 있습니다.

```cs
private static readonly Func<MyDbContext, int, Product> _compiledQuery =
    EF.CompileQuery((MyDbContext context, int id) =>
        context.Products.FirstOrDefault(p => p.Id == id));

var product = _compiledQuery(context, 1);
```

### Batch Save
여러 엔티티를 한 번에 저장하여 데이터베이스와의 통신 횟수를 줄일 수 있습니다.

```cs
context.AddRange(product1, product2, product3);
context.SaveChanges();
```

## 5. Raw SQL 쿼리 실행
EF Core에서는 복잡한 쿼리나 저장 프로시저를 직접 실행할 수 있습니다.

```cs
var products = context.Products
    .FromSqlRaw("SELECT * FROM Products WHERE Price > {0}", 100)
    .ToList();
```

주의: Raw SQL 쿼리를 사용할 때는 SQL 인젝션에 주의해야 하며, 가능한 한 매개변수를 사용하여 쿼리를 작성해야 합니다.

## 6. 데이터 시드(Seed) 설정
초기 데이터를 데이터베이스에 삽입하려면 ```HasData``` 메서드를 사용할 수 있습니다.

```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category { Id = 1, Name = "Electronics" },
        new Category { Id = 2, Name = "Books" }
    );
}
```

```Update-Database``` 명령을 실행하면 지정된 데이터가 삽입됩니다.

## 7. 마이그레이션 관리
**마이그레이션 제거 및 재생성**   
마이그레이션을 잘못 생성한 경우, 다음 명령어로 제거할 수 있습니다:

```powershell
Remove-Migration
```

그 후, 다시 마이그레이션을 생성하고 데이터베이스를 업데이트합니다:

```powershell
Add-Migration InitialiCreate
Update-Database
```