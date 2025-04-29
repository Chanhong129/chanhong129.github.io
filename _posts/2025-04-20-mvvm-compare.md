---
title: 그래서 MVVM 패턴이 뭔데? (Winform vs WPF)
date: 2025-04-20 15:00:00 +0900
categories: [Programming, MVVM]
tags: [c#, wpf, mvvm]
description: CommunityToolkit.Mvvm
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
> Winform 만 사용했었다가 **WPF** 사용하면서 **MVVM** 패턴을 처음 알게되었다.

## Winform vs WPF (MVVM)
> 간단한 버튼 클릭 예제여도 두 방법은 확연히 차이 난다.

윈도우에 버튼을 만들고 버튼을 누르면 버튼 텍스트가 바뀌는 예제를 만들어보았다.

### Winform 버튼 예제
![Winform Button Example UI](https://i.ibb.co/MDk225gt/image.png)

버튼을 더블 클릭하여 코드로 진입
```cs
private void button1_Click(object sender, EventArgs e)
{
    button1.Text = "Clicked!";
}
```

### MVVM 을 이용한 WPF 버튼 예제
![WPF MVVM Example UI](https://i.ibb.co/ynpvdVNp/image.png)

```cs
<Button Content="Button"
        Width="150" Height="100"/>
```

버튼을 더블 클릭하면 Winform 과 똑같아진다.   

그러면 **MVVM** 패턴은 어떻게 사용하는가?

버튼 클릭 이벤트를 받으려면 Button 의 Command 라는 속성을 이용 해야 한다.   
Command 를 사용하기 위해서는 ICommand 를 상속받는 클래스 구현이 필요하다.
```cs
public class RelayCommand : ICommand
{
	private readonly Action<object?> _execute;
	private readonly Func<object?, bool>? _canExecute;
	
	public RelayCommand(Action<object?> execute, Func<object?, bool>? canExecute = null)
	{
		_execute = execute ?? throw new ArgumentNullException(nameof(execute));
		_canExecute = canExecute;
	}
	
	public event EventHandler? CanExecuteChanged;
	
	public bool CanExecute(object? parameter)
	{
		return _canExecute?.Invoke(parameter) ?? true;
	}
	
	public void Execute(object? parameter)
	{
		_execute(parameter);
	}
	
	public void RaiseCanExecuteChanged()
	{
		CanExecuteChanged?.Invoke(this, EventArgs.Empty);
	}
}
```

자! 이제 **ViewModel** 에서 **Command** 와 **Button Text** 속성을 만들어 준다.
또한, 버튼 텍스트가 바뀔 때마다 UI 에 알려줄 PropertyChanged Event 를 등록한다.

```cs
public class MainViewModel : INotifyPropertyChanged
{
	private string buttonText = "Button";
	public string ButtonText
	{
		get { return buttonText; }
		set { buttonText = value; OnPropertyChanged(); }
	}

    public ICommand? ButtonClickCommand { get; set; }

    public MainViewModel()
    {
        ButtonClickCommand = new RelayCommand(ButtonClick);
    }

    private void ButtonClick(object? obj)
    {
        ButtonText = "Clicked!";
    }

    public event PropertyChangedEventHandler? PropertyChanged;
    public void OnPropertyChanged([CallerMemberName] string? propertyName = null)
	{
		this.PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
	}
}
```
이제 모든 준비가 끝나... ... 지 않았다. View 를 ViewModel 과 연동시켜주어야한다.

**MainWindow.xaml.cs** 에서 
```cs
public MainWindow()
{
    InitializeComponent();
    this.DataContext = new MainViewModel();
}
```

**DataContext** 에 **ViewModel** 을 연결해주어야 한다.

이제 모든 준비가 끝나... 아직이다.. **View** 에 **Button** 속성에서 **ViewModel** 에 있는 속성과 연동 시켜주어야 한다.

```cs
<Button Content="{Binding ButtonText}"
        Command="{Binding ButtonClickCommand}"
        Width="150" Height="100"/>
```

이제 진짜 모든 준비가 끝났다.

실행하여보자.

버튼을 누를 때 버튼 텍스트가 변하는 것을 볼 수 있다.

1. **RelayCommand** 클래스 작성
2. **ViewModel** 에서 속성 작성
3. **View** - **ViewModel** 연결(**DataContext**)
4. **View** 에서 **ViewModel** 의 속성과 연결

> 정말 간단한 버튼 예제였음에도 불구하고 WPF MVVM 방식은 오래 걸리고 복잡했다. 
> 
> 그럼에도 불구하고 왜 MVVM 을 사용할까?

- View 와 ViewModel 을 끊고 단독적으로 사용할 수 있다.
- View 가 달라도 Winform 에서 작성한 코드를 또 작성할 필요없이 ViewModel 을 연결만하면 사용 가능하다.
- View 와 ViewModel 독립적으로 코딩이 가능하다.

**=> View 와 ViewModel 을 독립적으로 사용 가능하다.**

그리고 다시 한번 [MVVM 패턴이란?](../what-is-mvvm) 포스트를 읽어 보자.
<br>

> C# 은 Nuget Package 가 정말 다양한 걸로 유명한데 이 복잡한 MVVM 패턴도 역시 쉽게 사용할 수 있는 Nuget 이 있다.
>
>마이크로소프에서 지원하는 **Community.Toolkit.Mvvm**
>마소니까 믿고 사용할 수 있다.