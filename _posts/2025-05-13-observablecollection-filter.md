---
title: Observable Collection 에 필터 거는 방법
date: 2025-05-13 09:00:00 +0900
categories: [Programming, WPF]
tags: [c#, observablecollection, filter, icollectionview]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> ```ObservableCollection<T>``` 은 WPF 에서 데이터 바인딩 시 가장 자주 사용하는 컬렉션입니다.   
> 하지만 아쉽게도 **LINQ 나 Where로 필터를 바로 적용할 수는 없어요.**   
> 그럴 때 사용하는 것이 바로 **CollectionView**입니다.

## 왜 ObservableCollection 에는 Filter 가 없을까 ?
- ```ObservableCollection<T>``` 는 단순히 데이터 변경을 UI에 알리는 용도
- **필터링, 정렬, 그룹핑 기능이 없음**
- 대신, WPF 는 내부적으로 ```CollectionView```**를 통해 데이터를 바인딩**함

## 해결책: CollectionViewSource or ICollectionView

### 1. 기본 구조 (ICollectionView 사용)

```cs
public ObservableCollection<Person> People { get; set; } = new();

public ICollectionView PeopleView { get; set; }

public MainViewModel()
{
    // 예시 데이터
    People.Add(new Person { Name = "Alice" });
    People.Add(new Person { Name = "Bob" });
    People.Add(new Person { Name = "Charlie" });

    // View 생성
    PeopleView = CollectionViewSource.GetDefaultView(People);

    // 필터 설정
    PeopleView.Filter = obj =>
    {
        if (obj is Person person)
            return person.Name.StartsWith("A"); // A로 시작하는 이름만
        return false;
    };
}
```

> ```PeopleView```는 필터링된 데이터를 나타내며, ```People```은 전체 원본입니다.

### 2. XAML 바인딩

```cs
<ListBox ItemsSource="{Binding PeopleView}">
    <ListBox.ItemTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding Name}" />
        </DataTemplate>
    </ListBox.ItemTemplate>
</ListBox>
```

### 3. 동적으로 필터 바꾸기

```cs
public string SearchText
{
    get => _searchText;
    set
    {
        _searchText = value;
        OnPropertyChanged();

        PeopleView.Refresh(); // 필터 다시 적용
    }
}

private bool FilterPerson(object obj)
{
    if (obj is Person person)
        return string.IsNullOrEmpty(SearchText) || person.Name.Contains(SearchText);
    return false;
}
```

설정:   

```cs
PeopleView.Filter = FilterPerson;
```

## 정리

|**항목**|**설명**|
|--|--|
|```ObservableCollection<T>```|데이터 소스 (필터 기능 없음)|
|```ICollectionView```|필터/정렬/그룹핑 기능을 제공하는 View|
|```CollectionViewSource.GetDefaultView```|View를 생성하는 메서드|
|```Filter```|필터 조건 설정|
|```Refresh()```|필터 조건 변경 시 호출해야함|

## 주의할 점
- ```PeopleView.Filter = ...``` 후 반드시 ```PeopleView.Refresh()``` 호출해야 필터 반영됨
- View를 바인딩할 때는 ```ObservableCollection```이 아닌 ```ICollectionView```로 해야 필터 된 결과가 표시됨
- 필터 조건 안에 UI 관련 로직을 넣지 말 것(MVVM 위반)

## 마무리
```ObservableCollection```에는 필터 기능이 없지만,   
```CollectionView```를 통해 필터링, 정렬, 그룹핑까지 모두 손쉽게 구현할 수 있습니다.   
MVVM 구조에서도 깔끔하게 적용 가능하니 꼭 익혀두세요!