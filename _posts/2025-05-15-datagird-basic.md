---
title: WPF Datagrid Custom 하기
date: 2025-05-15 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, datagrid]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> WPF 에서 DataGrid 는 다른 컨트롤보다 커스텀 하기가 어렵다.   
> DataGrid 가 단순한 컨트롤이 아닌 복잡한 컨트롤이기 때문이다.   
> 이번 포스팅에서는 간단한 DataGrid 커스텀에 대해서 포스팅하고자 한다.

## 기본 DataGrid
기본  DataGrid 생성과 간단한 데이터 입력 한다면 아래와 같다.

```cs
    <Grid>
        <Grid Margin="50">
            <DataGrid/>
        </Grid>
    </Grid>
```

![Basic DataGrid](https://i.ibb.co/x87jjSCD/image.png)

```cs
public partial class MainWindow : Window
{
    private ObservableCollection<Person> qwer = new ObservableCollection<Person>();
    public ObservableCollection<Person> QWER
    {
        get { return qwer; }
        set { qwer = value; }
    }


    public MainWindow()
    {
        InitializeComponent();
        DataContext = this;

        QWER = new ObservableCollection<Person>()
        {
            new Person(){Name="마젠타", Age=27},
            new Person(){Name="쵸단", Age=26},
            new Person(){Name="시연", Age=25},
            new Person(){Name="히나", Age=24}
        };
    }
}

public class Person : INotifyPropertyChanged
{
    private string name = string.Empty;
    public string Name
    {
        get { return name; }
        set { name = value; OnPropertyChanged(); }
    }
    private int age;
    public int Age
    {
        get { return age; }
        set { age = value; OnPropertyChanged(); }
    }

    public event PropertyChangedEventHandler? PropertyChanged;
    public void OnPropertyChanged([CallerMemberName] string? name = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
    }
}
```
![Basic DataGrid Data](https://i.ibb.co/hxntMHV2/image.png)

## WPF DataGrid 커스터마이징 - 실전 스타일 설정
WPF ```DataGrid```는 기본 상태로도 충분히 강력하지만, 디자인을 손보면 훨씬 깔끔하고 사용자 친화적으로 만들 수 있습니다. ```DataGrid```의 **헤더, 셀, 행 스타일**을 각각 지정하여 깔끔한 UI를 구성하는 방법을 소개합니다.

### 기본 설정

```cs
<DataGrid ItemsSource="{Binding QWER}"
          SelectedItem="{Binding SelectedItem}"
          HeadersVisibility="Column"
          GridLinesVisibility="None"
          BorderThickness="0"
          IsReadOnly="True"
          SelectionMode="Single"
          SelectionUnit="FullRow"
          CanUserAddRows="False"
          CanUserDeleteRows="False"
          CanUserReorderColumns="False"
          CanUserSortColumns="False">
```

- ```IsReadOnly="True"```: 읽기 전용으로 편집을 막습니다.
- ```SelectionUnit="FullRow"```: 셀 단위가 아니라 행 전체 선택이 됩니다.
- ```CanUserXXX="False"```: 사용자가 컬럼을 추가/삭제/정렬/순서 변경하지 못하게 제한합니다.
- ```HeaderVisibility="Column"```: 열 헤더만 표시되며, 행 번호는 숨깁니다.
- ```GridLineVisibility="None"```: 격자선 제거.

### 헤더 스타일 (DataGridColumnHeader)

```cs
<Style TargetType="{x:Type DataGridColumnHeader}">
    <Setter Property="Background" Value="#F4F4F5"/>
    <Setter Property="Foreground" Value="#707079"/>
    <Setter Property="FontSize" Value="13"/>
    <Setter Property="FontWeight" Value="Bold"/>
    <Setter Property="BorderBrush" Value="#E9E9EB"/>
    <Setter Property="BorderThickness" Value="0 0 0 2"/>
    <Setter Property="Padding" Value="10 5"/>
</Style>
```

- 밝은 회색 배경, 회색 텍스트로 시인성 있는 디자인.
- 아래쪽에만 보더를 주어 섹션 구분을 강조.
- 패딩과 폰트 지정으로 가독성 향상.

### 셀 스타일 (DataGridCell)

```cs
<Style TargetType="DataGridCell">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="DataGridCell">
                <Border BorderThickness="0" Background="Transparent">
                    <Border x:Name="border" Background="Transparent" Padding="10 5">
                        <ContentPresenter/>
                    </Border>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>

    <Style.Triggers>
        <Trigger Property="IsSelected" Value="True">
            <Setter Property="Foreground" Value="Black"/>
        </Trigger>
    </Style.Triggers>
</Style>
```

- 셀의 보더와 배경읠 제거하여 깔끔한 느낌.
- 선택된 셀일 경우 텍스트 색상을 검정으로 지정.

### 행 스타일 (DataGridRow)

```cs
<Style TargetType="DataGridRow">
    <Setter Property="BorderThickness" Value="0 0 0 2"/>
    <Style.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter Property="Background" Value="#FAFAFA"/>
        </Trigger>

        <DataTrigger Binding="{Binding IsHighlight}" Value="True">
            <Setter Property="Background" Value="#FFFDE7"/>
        </DataTrigger>

        <Trigger Property="IsSelected" Value="True">
            <Setter Property="Background" Value="#F2F9FF"/>
        </Trigger>
    </Style.Triggers>
</Style>
```

- 마우스 오버 시 부드러운 회색 배경 표시
- ```IsHighlight``` 속성 true 일 경우 강조 색상 (```#FFFDE7```) 적용.
- 선택된 행은 파란색 톤의 배경 (#```F2F9FF```)으로 강조.


## 커스텀 DataGrid

![Custom DataGrid](https://i.ibb.co/S709hXfp/image.png)

---

이런 식으로 스타일을 설정하면, UX를 고려한 **깔끔하고 현대적인 UI** 를 가진 로그 뷰어나 기록 관리 툴을 만들 수 있습니다.