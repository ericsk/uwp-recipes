# 4.1 改良的 BMI 計算機

在 [3.1 - 製作 BMI 計算機](../ch3/01_create_bmi_calculator.md)這則食譜中，做了一個簡單的 BMI 計算機，現在我們從這則食譜改良，簡單介紹在 XAML 程式設計中一個重要的觀念：_資料繫結（Data Binding）_。

## 事前準備

  * 首先完成 [3.1 - 製作 BMI 計算機](../ch3/01_create_bmi_calculator.md)這則食譜的內容。

## 操作步驟

  1. 打開 **MainPage.xaml**，找到_身高_及_體重_的文字輸入框，還有最後顯示結果的文字標籤，分別將 ```<TextBox>``` 及 ```<TextBlock>``` UI 元件修改成這樣：

  ```xml
  <!-- 身高 -->
  ...
    <TextBox Width="80" Text="{Binding Height, Mode=TwoWay}" />
  ...
  <!-- 體重 -->
  ...
    <TextBox Width="80" Text="{Binding Weight, Mode=TwoWay}" />
  ...
  <!-- 結果 -->
  ...
  <TextBlock Grid.Row="4" Grid.Column="1"
             Text="{Binding BMI}"
             Margin="10,5" Style="{StaticResource TitleTextBlockStyle}"/>
  ```

  這裡雖然加上 _Text_ 屬性，但是並不是直接填值進去，而是使用資料繫結（data binding）的語法，讓填入身高數值的 ```<TextBox>``` 文字數值去 \*bind\* _Height_ 變數、體重的就是 bind _Weight_ 變數、而顯示結果的文字標籤去 bind _BMI_，透過這樣的資料繫結，文字輸入框的 _Text_ 值就跟繫結的變數同步了（因為我們設定了 _Mode=TwoWay_），下一個問題就是，那 _Height_、_Weight_、以及 _BMI_ 變數要放在哪裡？

  另外，如果您仔細看，這裡還把原本的 _x:Name_ 屬性移除掉了，那在 code behind 中要如何存取這個 UI 元件？稍後我們就會說明。

  2. 到方案總管裡，在專案的名稱按右鍵，選擇_加入_ > _類別..._。

  ![加入新的類別](https://skgitbook.blob.core.windows.net/uwprecipes/ch4/1_add_new_class.png)

  接著在**名稱**欄位輸入 _MainViewModel.cs_，然後打開 **MainViewModel.cs** 檔案編輯，內容如下：

  ```csharp
  namespace BMI_Calculator
  {
    public class MainViewModel
    {
      public string Height { get; set; }
      public string Weight { get; set; }
      public string BMI { get; set; }
    }
  }
  ```

  在這個 MainViewModel 類別中，我們定義了三個字串資料型態的屬性（property），這個部份就是要用來與 MainPage.xaml 上面身高及體重的文字輸入框以及顯示 BMI 結果做資料繫結使用。

  3. 接下來就是如何把 _MainViewModel_ 中的三個屬性與 _MainPage.xaml_ 中的文字輸入框繫結起來，做法很容易，就是在 MainPage.xaml 的 code behind 中指定 **DataContext**，這樣一來，在 XAML 中繫結的欄位都會與 DataContext 所設定的物件產生關聯，這樣也就繫結起來。

  打開 **MainPage.xaml.cs** 檔案，先在類別的開頭處定義一個 MainViewModel 的物件並且初始化它：

  ```csharp
  public sealed partial class MainPage : Page
  {
    private MainViewModel viewModel = new MainViewModel();
  ```

  產生了一個 _MainViewModel_ 的物件後，剩下的工作就是把 _MainPage_ 的 _DataContext_ 設定成這個物件，只要在建構式中呼叫 _InitializeComponent()_ 的敘述句後設定即可：

  ```csharp
  public MainPage()
  {
    this.InitializeComponent();
    this.DataContext = viewModel;
  }
  ```

  這樣就完成資料繫結了，經過這樣的修改，頁面上輸入身高及體重的輸入欄位資料，就會與 _viewModel_ 物件下的 _Height_、_Weight_、以及 _BMI_ 屬性產生繫結，也就是資料會同步，只要使用者輸入了身高欄位的資料，_viewModel_ 下的 _Height_ 屬性就會被設定成該值。

    > 因為我們在 XAML 上設定 binding 時有設定了 _Mode=TwoWay_，這表示兩個方向 1) 在介面上輸入 2) 更新 view model 的值 都會同步資料，如果不設 _Mode_ 則預設的值是 _OneWay_，也就是只有更新 view model 的值才會同步到畫面上，在畫面上輸入則不會同步到 view model 中。
    > 在顯示結果的文字標籤上，我們只要使用 _OneWay_ 就可以了。

  4. 但是程式還沒有做完事，既然我們繫結了 _viewModel_ 與頁面的 UI 控制元件，那在按鈕按下後的處理函式也要做一些修改，這時存取資料時就不必再透過控制項來拿資料，只要直接拿 _viewModel_ 中的屬性就可以了，所以處理函式就要修改成：

  ```csharp
  private void OnCalculateButtonClicked(object sender, RoutedEventArgs e)
  {
    double height, weight;

    if (double.TryParse(viewModel.Height, out height) &&
        double.TryParse(viewModel.Weight, out weight))
    {
      var heightInM = height / 100.0;
      viewModel.BMI = (weight / (heightInM * heightInM)).ToString();
    }
  }
  ```

  注意現在我們要存取 XAML 上面的值都是透過 _viewModel_ 來拿，只要做好資料繫結，就不必在程式裡直接去存取 XAML 上的 UI 元件，這麼做也可以讓 code behind 或是 view model 的程式不會跟 UI 綁得那麼緊，在我們撰寫複雜一點的程式後就能體會這樣的作法更有彈性。

  5. 當你完成上述的程式碼後，你一定很開心地把程式執行起來操作，結果發現 BMI 值好像沒有被計算出來，到底這樣的資料繫結做法有沒有問題？其實這裡還有一件事情沒有做，雖然我們已經完成了資料繫結的工作，但是**當 _viewModel_ 的資料更新時，沒有通知 XAML 更新畫面**，也就是說，按鈕的處理函式做完後，雖然 _viewModel.BMI_ 的值已經修改了，但 XAML 頁面不知道要更新畫面，所以看起來好像程式沒有效果一樣，所以我們要修改 **MainViewModel** 的內容，在 _BMI_ 屬性被修改時發出**通知**。

  ```csharp
  using System.ComponentModel;

  namespace BMI_Calculator
  {
    public class MainViewModel : INotifyPropertyChanged
    {
      public string Height { get; set; }
      public string Weight { get; set; }

      private string _bmi;
      public string BMI
      {
        get
        {
          return _bmi;
        }
        set
        {
          if (!value.Equals(_bmi))
          {
            _bmi = value;
            NotifyPropertyChanged("BMI");
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

  注意到這裡我們把 _MainViewModel_ 加上實作了 _INotifyPropertyChanged_ 的介面，並且在 _BMI_ 值更新時送出一個 _Property Changed_ 的事件，這樣一來，_MainPage_ 因為 _DataContext_ 被主動通知了內容更新，也就會更新畫面。將 **MainViewModel.cs** 的內容修改完畢後，重新建置應用程式執行，是不是就會正常運作了呢？


## 參考資料

  * (MSDN) [Windows 10 app 開發指南 - Data Binding](https://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/mt210947.aspx)