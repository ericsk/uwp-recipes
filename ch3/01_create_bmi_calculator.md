# 3.1 製作 BMI 計算機

在這則食譜中，藉著製作一個簡單的 BMI 計算機，瞭解如何運用 ```<Grid>``` 與 ```<StackPanel>``` 來做排版，以及基本的文字標籤、文字輸入框、與按鈕的基本用法。

## 事前準備

  * 安裝開發環境，可以參考 [2.1 安裝 UWP 開發工具](../ch2/01_setup_uwp_development_tool.md)。

## 操作步驟

  1. 開啟 Visual Studio 2015，在起始畫面中選擇**新增專案**（或是從功能表列選**檔案** > **新增...** > **專案...**），接著在 _Visual C#_ > _Windows_ > _通用_ 範本中選擇_空白應用程式 (通用 Windows)_，最後在下方名稱的欄位填入 _BMI Calculator_，最後按下右下角的**確定**按鈕開始建立專案。

    ![新增通用 Windows 專案](https://skgitbook.blob.core.windows.net/uwprecipes/ch3/1_create_new_project.png)

  2. 專案建立完成後，在方案視窗中點擊 **MainPage.xaml** 檔案來編輯這個計算機的操作介面，首先運用 ```<Grid>``` 元件來畫出介面的版型，在 XAML 檔案中將原本的 ```<Grid>``` 元件部份修改為：

    ```xml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
       <Grid.RowDefinitions>
          <RowDefinition Height="120" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="Auto" />
       </Grid.RowDefinitions>

       <Grid.ColumnDefinitions>
          <ColumnDefinition Width="100" />
          <ColumnDefinition Width="150" />
          <ColumnDefinition Width="*" />
       </Grid.ColumnDefinitions>
    </Grid>
    ```
  
  在這段 XAML 裡，我們在畫面上用 ```<Grid>``` 元件定義了 5 個列（row）、3 個行（column）。其中第一個列的列高為 120，其餘的列都是由該列填入的元件高度來決定；而行的部份一個 100、一個 150 寬，第三行設定為 __*__ 表示使用其餘的畫面寬度。

  設定完成後，可以在 XAML 設計視窗中看到輔助格線標示了這些定義。

  ![新增通用 Windows 專案](https://skgitbook.blob.core.windows.net/uwprecipes/ch3/1_bmi_xaml_designer.png)

  3. 接下來先放一個表示應用程式標題的文字標籤，在 ```</Grid.ColumnDefinitions>``` 之後加入

    ```xml
    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" 
                Text="BMI 計算機" Style="{StaticResource HeaderTextBlockStyle}"
                VerticalAlignment="Center" Margin="20,0" />
    ```

  ```<TextBlock>``` 是專門用來顯示文字使用的 UI 元件，只要在其中使用 _Text_ 屬性就可以設定要顯示的文字。而在這個例子中，我們要將它放在畫面最上方（也就是第一列），所以使用了 ```Grid.Row="0"``` 來指定這個元件要擺在第一列；同時，我們並不希望標題文字只在第一列第一行顯示，應該要橫跨三行，所以使用 ```Grid.ColumnSpan="3"``` 來指明這個元件會佔用三行的空間來顯示文字；樣式的部份，我們選用系統內建的標題文字樣式，所以使用 ```Style="{StaticResource HeaderTextBlockStyle}"``` 來作設定，另外 _VerticalAlignment_ 屬性指定垂置對齊方式、而 _Margin_ 則是設定文字標籤外的留白。

    > Margin 屬性內的值其實應該要有四個數值，依序代表_左_、_上_、_右_、_下_的留白，例如 ```Margin="10,5,15,20"``` 所設定的留白表示左側 10、上方 5、右側 15、以及下方 20。
    > 在這個例子中，設定兩個數值就代表左右都是 20，而上下都是 0。

  填入後，可以看到 XAML 設計視窗中的呈現：

  ![應用程式標題](https://skgitbook.blob.core.windows.net/uwprecipes/ch3/1_bmi_title_text_block.png)

  4. 接下來，在標題的 ```<TextBlock>``` 下方將其它元件填入：

    ```xml
    <!-- 身高 -->
    <TextBlock Grid.Row="1" Grid.Column="0" Margin="10,5"
                Text="身高:" Style="{StaticResource BodyTextBlockStyle}" 
                HorizontalAlignment="Right" VerticalAlignment="Center" />

    <StackPanel Orientation="Horizontal" Margin="10,5" 
                 Grid.Row="1" Grid.Column="1">
        <TextBox x:Name="Height" Width="80" />
        <TextBlock Text="cm" VerticalAlignment="Center" Margin="5,0,0,0" />
    </StackPanel>

    <!-- 體重 -->
    <TextBlock Grid.Row="2" Grid.Column="0" Margin="10,5"
                Text="體重:"  Style="{StaticResource BodyTextBlockStyle}"
                HorizontalAlignment="Right" VerticalAlignment="Center"/>

    <StackPanel Orientation="Horizontal" Margin="10,5"
                 Grid.Row="2" Grid.Column="1">
       <TextBox x:Name="Weight" Width="80" />
       <TextBlock Text="kg" VerticalAlignment="Center" Margin="5,0,0,0" />
    </StackPanel>

    <Button Grid.Row="3" Grid.Column="1" Margin="10,5" 
             Content="計算" Click="OnCalculateButtonClicked" />

    <!-- 結果 -->
    <TextBlock Grid.Row="4" Grid.Column="0"  Margin="10,5"
                Text="結果:" Style="{StaticResource BodyTextBlockStyle}"
                HorizontalAlignment="Right" VerticalAlignment="Center" />

    <TextBlock x:Name="BMIResult" Grid.Row="4" Grid.Column="1"
                Margin="10,5" Style="{StaticResource TitleTextBlockStyle}"/> 
  ```

  擺放 UI 元件時，因為已經使用 ```<Grid>``` 定義了排版佈局，所以都是用 ```Grid.Row``` 以及 ```Grid.Column``` 屬性來指定要擺放的位置，其中在文字輸入框的部份使用了 ```<StackPanel>``` 這個排版元件，這個排版元件主要用來讓 UI 元件垂直地或水平地依序排列，所以這裡用 ```Orientation="Horizontal"``` 來指定是水平排列（不使用這個屬性或設成 _Vertical_ 則表示垂直排列），這裡用來排列文字輸入框 ```<TextBox>``` 以及一個用來表示 cm 或 kg 的文字標籤。

  按鈕是 ```<Button>``` 標籤，而按鈕上的文字使用 _Content_ 屬性來設定顯示的文字，而 _Click_ 屬性是用來指定按鈕被按下後，要由哪一段程式（handler）來處理，這裡先設定好，稍後會再說明怎麼處理這部份的程式。

  最後用來顯示結果的部份使用了一個 ```<TextBlock>``` 來做為顯示 BMI 的區域，因為數值是稍後計算出來，所以沒有特別指定 _Text_ 屬性，而為了在程式中能存取這個 UI 元件，所以使用了 _x:Name_ 屬性設定名稱，之後用程式存取也是使用這個名稱。

    > 在程式中可能會存取到的元件都會使用 _x:Name_ 屬性設定名稱以便存取。

  所以這個 BMI 計算機最後的 UI 畫面便是如此：

  ![BMI 計算機 UI](https://skgitbook.blob.core.windows.net/uwprecipes/ch3/1_bmi_calculator_layout.png)

  5. 設計好操作介面，接下來就是處理程式的部份，每一個 XAML 檔案都會搭配一個 **code behind**，比方說 _MainPage.xaml_ 就會搭配一個 _MainPage.xaml.cs_，除了可以在方案總管的 XAML 檔下方開啟，或是在編輯 XAML 檔案時按下 F7 快速鍵打開 code behind 來編輯。

  ![開啟 XAML code behind](https://skgitbook.blob.core.windows.net/uwprecipes/ch3/1_open_xaml_code_behind.png)

  開起後，在 _MainPage_ 類別中加入一個 ```OnCalculateButtonClicked``` 的方法（與 XAML 中在 ```<Button>``` 設定 _Click_ 屬性相同），用來處理按鈕按下後的動作。

   ```csharp
   private void OnCalculateButtonClicked(object sender, RoutedEventArgs e)
   {
       double height, weight;
       
       if (double.TryParse(Height.Text, out height) && 
           double.TryParse(Weight.Text, out weight))
       {
           var heightInM = height / 100.0;
           BMIResult.Text = (weight / (heightInM * heightInM)).ToString();
       }
   }
   ```

  由於在身高的文字輸入框中設定了 ```x:Name="Height"```，所以在 code behind 中可以直接用 ```Height``` 來取得該文字輸入框，所以就可以藉著存取它的 _Text_ 屬性拿到輸入後的文字，由於拿回來的是一個 _string_ 型態的值，所以使用了 ```double.TryParse``` 將文字資料轉換成 _double_ 型態的浮點數資料，以便作後續的計算。

  最後，計算出來的 BMI 值要顯示在文字標籤上，這裡就要再把 _double_ 型態的資料再轉回 _string_ 型態才能設定到文字標籤上，所以計算完的結果呼叫了 ```ToString()``` 轉成 _string_ 型態，然後填入 ```BMIResult``` 的 _Text_ 屬性中。

  6. 最後建置執行沒有任何錯誤的話，這個 BMI 計算機就大功告成了。

## 參考資料

  * [Grid 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.grid.aspx)
  * [StackPanel 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx)
  * [TextBlock 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.textblock.aspx)
  * [TextBox 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.textbox.aspx)
  * [Button 類別](https://msdn.microsoft.com/zh-tw/library/windows/apps/windows.ui.xaml.controls.button.aspx)