# 5.2 將應用程式的內容儲存在檔案中

有些應用程式可能會需要將程式中的內容儲存下來，之後程式可以再根據需要把內容再讀取出來。像我們在 [4.2 製作抽獎程式](../ch4/02_lucky_draw_app.md)、[5.1 讀取應用程式套件內檔案的內容](01_load_file_from_app_package.md) 提到的抽獎程式，也可以在中獎名單抽出後儲存下來，以免不小心關掉應用程式後名單就不見了。

所以在這則食譜中，我們會把抽獎程式加入儲存名單的功能，以及重新開啟應用程式時能檢查檔案是否存在，再讀取出來。

## 學習目標

在這則食譜中，您會瞭解到如何運用 UWP 應用程式的 **AppData** 空間來儲存或讀取檔案。

  > 與 [5.1 - 讀取應用程式套件內檔案的內容](01_load_file_from_app_package.md)提到的應用程式套件空間不同，放在 **AppData** 中的內容是可以讀寫的。

## 事前準備

  * 完成 [4.2 製作抽獎程式](../ch4/02_lucky_draw_app.md)及 [5.1 讀取應用程式套件內檔案的內容](01_load_file_from_app_package.md)的功能。

## 操作步驟

  1. 首先完成抽完獎後把名單儲存下來，打開 **MainViewModel.cs** 檔案，在抽獎的 ```Draw()``` 方法中抽完獎後進行存檔。

	```csharp
	public void Draw()
    {
	   ...
	   SaveDataAsync();
	}

    private async void SaveDataAsync()
    {
       // 取得 AppData 的 local 目錄
       StorageFolder folder = ApplicationData.Current.LocalFolder;
       // 建立新檔案 data.json
       StorageFile file = await folder.CreateFileAsync("data.json", CreationCollisionOption.ReplaceExisting);

       // 把中獎名單變成 JSON 物件
       var jsonObj = new JsonObject();
       var winnersArray = new JsonArray();
       foreach (var winner in Winners)
       {
         var obj = new JsonObject();
         obj["Name"] = JsonValue.CreateStringValue(winner.Name);
         obj["Department"] = JsonValue.CreateStringValue(winner.Department);
         obj["Gender"] = JsonValue.CreateNumberValue((double)winner.Gender);
         winnersArray.Add(obj);
       }
       jsonObj["Winners"] = winnersArray;
       // 將 JSON 物件轉成文字
       string jsonText = jsonObj.Stringify();

       // 寫入檔案
       await FileIO.WriteTextAsync(file, jsonText);      
    }
	```

  在 ```SaveDataAsync()``` 方法中，首先使用了 ```Windows.ApplicationData.Current``` 取得 ```LocalFolder``` 的目錄（註 1），然後在這個目錄下叫叫 _CreateFileAsync_ 建立一個新檔案，檔案名稱設成 _data.json_，_CreationCollisionOption.ReplaceExisting_ 參數就是在檔案存在時直接覆蓋掉。

  接下來的工作，就是要把_儲存在 Winners 這個資料結構裡的資料轉換成 JSON 格式的資料_，在 [5.1 - 讀取應用程式套件內檔案的內容](01_load_file_from_app_package.md) 中我們已經瞭解如何把 JSON 格式的文字轉成 _Windows.Data.Json_ 物件，在這裡就是要做相反的操作，所以針對每一個中獎名單的資料轉成 _JsonObject_、放進 _JsonArray_，統整到一個 _JsonObject_ 中，最後便能呼叫 **Stringify()** 方法把 JSON 物件轉換成純文字。

  順利轉成 JSON 格式的文字資料後，就呼叫 ```FileIO.WriteTextAsync()``` 把文字寫進檔案中，所以加上這樣的修改後，抽完獎就會同時把結果儲存在 **AppData** 目錄中的檔案。

  2. 順利把名單儲存在檔案中後，下一步就是在應用程式開啟時，若發現有存檔就把資料讀取進來，所以一樣打開 **MainViewModel.cs** 檔案來編輯，然後在 ```Init()``` 方法的最後加上以下的程式碼：

	```csharp
	private async void Init()
    {
	   ...
	   LoadFromAppDataFileAsync();
	}

	private async void LoadFromAppDataFileAsync()
    {
       var folder = ApplicationData.Current.LocalFolder;
       // 確認檔案是否存在
       if (folder.TryGetItemAsync("data.json") != null)
       {
          var file = await folder.GetFileAsync("data.json");

          string jsonText = await FileIO.ReadTextAsync(file);

          var winObj = JsonObject.Parse(jsonText);
          var winArray = winObj["Winners"].GetArray();
          foreach (var win in winArray)
          {
             var obj = win.GetObject();
             Winners.Add(new Winner()
             {
                Name = obj["Name"].GetString(),
                Department = obj["Department"].GetString(),
                Gender = (int) obj["Gender"].GetNumber()
             });
          }
       }
    }
	```
  
  先檢查 _data.json_ 檔案是否存在，如果存在就表示之前已經有存檔，所以就把它讀出來，並且使用相同的方法把 JSON 格式資料用 ```Windows.Data.Json``` 來解析資料，最後塞進 _Winners_ 的資料結構中。

  這樣一來，就可以確保中獎名單在抽獎完成時儲存，並且重新開啟應用程式時也能顯示已經儲存過的資料。


## 參考資源

  * [MSDN] [使用 JavaScript 物件標記法 (JSON)](https://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/hh770289.aspx)
  * [MSDN] [Create, write, and read a file](https://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/mt185401.aspx)