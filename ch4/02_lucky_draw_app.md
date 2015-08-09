# 4.2 製作抽獎程式

在這則食譜中將會介紹如何運用 ```<GridView>``` 元件來顯示集合類型的資料。

## 事前準備

  * 大致熟悉 XAML 一些基礎 UI/Layout 元件的使用。
  * 瞭解基本的資料繫結（data binding）概念，可以參考 [4.1 改良的 BMI 計算機](01_bmi_calculator_using_data_binding.md)這則食譜的介紹。

## 操作步驟

  1. 建立一個名為 _LuckyDraw_ 的空白 UWP 專案後，在專案中新增一個 **Winner.cs** 的類別，這個類別要用來表示抽獎得主的個人資料，這裡我們顯示 3 個資料欄位：_姓名_、_部門_還有_性別_即可，而 **Winner.cs** 的內容如下：

  	```csharp
  	using System.ComponentModel;

	namespace LuckyDraw
	{
	  public class Winner : INotifyPropertyChanged
	  {
	    private string _name;
	    public string Name
	    {
	      get
	      {
	        return _name;
	      }
	      set
	      {
	        if (!value.Equals(_name))
	        {
	          _name = value;
	          NotifyPropertyChanged("Name");
	        }
	      }
	    }
	
	    private string _department;
	    public string Department
	    {
	      get
	      {
	        return _department;
	      }
	      set
	      {
	        if (!value.Equals(_department))
	        {
	          _department = value;
	          NotifyPropertyChanged("Department");
	        }
	      }
	    }
	
	    private int _gender;
	    public int Gender
	    {
	      get
	      {
	        return _gender;
	      }
	      set
	      {
	        if (value != _gender)
	        {
	          _gender = value;
	          NotifyPropertyChanged("Gender");
	        }
	      }
	    }
	
	    public event PropertyChangedEventHandler PropertyChanged;
	    private void NotifyPropertyChanged(string propertyName)
	    {
	      PropertyChangedEventHandler handler = PropertyChanged;
	      if (null != handler)
	      {
	        handler(this, new PropertyChangedEventArgs(propertyName));
	      }
	    }
	  }
	}
  ```

  雖然我們只想要定義 _Name_、_Department_、還有 _Gender_ 三個屬性，但為了讓這些屬性繫結在 UI 畫面時會主動通知畫面是否要更新，所以還是實作了 ```INotifyPropertyChanged``` 介面，而每個屬性在更新的部份也主動觸發 _Property Changed_ 事件。

  2. 有了基礎資料結構後，接下來便能建立一個 view model 來與操作介面做資料繫結，一樣在專中加入一個 **MainViewModel.cs** 的類別。先將內容修改為：

	```csharp
	using System.Collections.ObjectModel;

	namespace LuckyDraw
	{
  		public class MainViewModel
  		{
    		public ObservableCollection<Winner> Winners { get; private set; }

    		public MainViewModel()
    		{
      			Winners = new ObservableCollection<Winner>();
    		}
  		}
	}
	```

  這裡我們用了一個**集合（collections）資料結構** -- ```ObservableCollection```，它的用法就類似 ```List``` 這類的資料結構一樣是個**容器（container）**，裡面可以放入多筆資料，而容器內的資料型態則是在 ```<>``` 中定義，在這個例子中，我們定義的是剛才建立的 _Winner_。

  而這裡使用 ```ObservableCollection``` 而不使用 ```List``` 的理由，就與資料結構要實作 ```INotifyPropertyChanged``` 的理由相同 -- **需要通知畫面更新**，由於我們在 MainViewModel 中設定的 _Winners_ 屬性是用來在畫面上呈現中獎者的列表，牽扯到畫面的新新，所以選擇用會主動通知改變的容器。

  3. 有了基礎資料結構後，回到 **MainPage.xaml** 來設計抽獎程式的操作介面，我們簡單地在畫面上放個標題，然後一個按鈕，最後就是顯示中獎者的名單，所以設計成這樣：


	```xml
	<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <Grid.RowDefinitions>
	    <RowDefinition Height="120"/>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="*" />
	  </Grid.RowDefinitions>

      <TextBlock Grid.Row="0" Text="抽獎程式"
                 Style="{StaticResource HeaderTextBlockStyle}"
                 VerticalAlignment="Center" Margin="20,0"/>
    
      <Button Grid.Row="1" Content="抽獎" Margin="20,0"
              Click="Button_Click" />
    
      <TextBlock Grid.Row="2" Text="中獎名單" Margin="20"
                 Style="{StaticResource SubheaderTextBlockStyle}"/>

      <ScrollViewer Grid.Row="3" VerticalScrollBarVisibility="Auto">
        <GridView Margin="20,0" ItemsSource="{Binding Winners}" />
      </ScrollViewer>
	</Grid>
	```

  這個佈局也很簡單，就是主畫面有 4 個列，第一列放標題文字、第二列是按鈕、第三列是顯示中獎名單的文字標籤，最後一列則是要準備顯示中獎名單的資訊，這裡我們使用 ```<GridView>``` 元件來顯示中獎名單的列表，同時它也做好了繫結 _Winners_ 的設定，但由於資料內容可能會超過畫面的高度，所以用一個 ```<ScrollViewer>``` 元件包起來，這樣若是 ```<GridView>``` 的內容超過畫面高度，就會出現捲動軸得以捲動畫面。

     > 在桌面環境就是出現捲動軸，而在手機或觸控操作的環境中就是可以用手指捲動。

  4. 雖然有了基本佈局，但是當資料放入 view model 的 _Winners_ 時，```<GridView>``` 會以怎樣的方式呈現呢？所以我們必須再設定 ```<GridView>``` 的**資料範本**，告訴它應該要怎麼繪製資料，所以我們在 ```<GridView>``` 元件中再設定 ```GridView.ItemTemplate``` 屬性的內容：

	```xml
	<GridView Margin="20,0" ItemsSource="{Binding Winners}">
      <GridView.ItemTemplate>
        <DataTemplate>
          <Border Height="180" Width="180" Margin="5,10">
            <StackPanel Margin="12">
              <TextBlock Text="{Binding Name}" 
                         Style="{StaticResource TitleTextBlockStyle}" />
              <TextBlock Text="{Binding Department}"
                         Style="{StaticResource SubtitleTextBlockStyle}" />
            </StackPanel>
          </Border>
        </DataTemplate>
	  </GridView.ItemTemplate>
	</GridView>
	```

  在 ```<GridView>``` 中透過設定 ```ItemTemplate``` 的方式便能指定容器內的每一個元素要如何呈現，當然在這裡也可以做資料繫結，這時就會綁定容器內資料對應的屬性。

  5. 在操作介面都已經準備好的情況下，接下來就是把程式完成了，打開 **MainPage.xaml.cs** 完成這個 XAML 檔案的 code behind。

  首先建立一個 _MainViewModel_ 的物件，然後把這個物件設定到這一頁的 _DataContext_ 中：

	```csharp
	private MainViewModel viewModel = new MainViewModel();

	public MainPage()
	{
	  this.InitializeComponent();
	  this.DataContext = viewModel;
	}
	```

  然後按鈕的處理函式：

	```csharp
	private void Button_Click(object sender, RoutedEventArgs e)
	{
	  viewModel.Draw();
	}
	```
  
  這裡我們透過 view model 來呼叫 ```Draw``` 方法，意義就是要做抽獎的操作，所以稍後我們必須回到 **MainViewModel.cs** 中加入抽獎的部份。

  6. 打開 **MainViewModel.cs**，首先加入初始化資料的部份：

	```csharp
	private List<Winner> Attendees;

    public MainViewModel()
	{
	  Winners = new ObservableCollection<Winner>();
	  Attendees = new List<Winner>();
      Init();
	}

	private void Init()
    {
      Attendees.Add(new Winner() { Name = "Alice", Department = "Admin", Gender = 0 });
      Attendees.Add(new Winner() { Name = "Bob", Department = "Legal", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Catherine", Department = "Finance", Gender = 0 });
      Attendees.Add(new Winner() { Name = "David", Department = "Marketing", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Eric", Department = "Engineering", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Frank", Department = "Engineering", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Gary", Department = "Sales", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Helen", Department = "Marketing", Gender = 0 });
      Attendees.Add(new Winner() { Name = "Irene", Department = "Marketing", Gender = 0 });
      Attendees.Add(new Winner() { Name = "James", Department = "Sales", Gender = 1 });
      Attendees.Add(new Winner() { Name = "Kathy", Department = "Sales", Gender = 0 });
      Attendees.Add(new Winner() { Name = "Leslie", Department = "Sales", Gender = 0 });
      Attendees.Add(new Winner() { Name = "Mark", Department = "Sales", Gender = 1 });
    }
	```

  這裡用 _Attendees_ 來放所有可以抽獎的名單，因為只是內部使用的資料，不必與 UI 畫面做資料繫結，所以可以只需要使用 ```List``` 資料結構即可。

  7. 接下來是抽獎的操作，一樣在 **MainViewModel.cs** 檔案中，加入一個 _Draw_ 的公開方法：

	```csharp
	public void Draw()
	{
      Winners.Clear();

      var rand = new Random();
      var drawed = new List<int>();
      int length = Attendees.Count;
      int index;

      // 抽出 5 位
      for (int i = 0; i < 5; ++i)
      {
        do
        {
          index = rand.Next() % length;
        } while (drawed.Contains(index));
        drawed.Add(index);

        Winners.Add(Attendees[index]);
      }	
	}
	```

  在抽獎前，我們先將原本在 _Winners_ 裡的資料清空，然後再簡單地使用 _Random_ 物件來亂數決定中獎的序號，再把它加到 _Winners_ 裡，當這個簡單的程式執行完畢，畫面也就會出現中獎的名單了。

  ![抽獎後的中獎名單](https://skgitbook.blob.core.windows.net/uwprecipes/ch4/2_lucky_draw_first_draft.png)

## 參考資料

  * (MSDN) [GridView 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.gridview.aspx)
  * (MSDN)(Windows 8) [快速入門：新增 ListView 和 GridView 控制項 (XAML)](https://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/Hh780650.aspx)