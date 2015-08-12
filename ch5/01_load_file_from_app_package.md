# 5.1 讀取應用程式套件內檔案的內容

在 [4.2 - 製作抽獎程式](../ch4/02_lucky_draw_app.md)中我們在程式中初始化抽獎名單，但把資料寫死在程式裡的作法其實不太漂亮，如果可以把這份名單獨立儲存在一個檔案中，然後程式只要去讀取檔案的內容就好了，所以在這則食譜中，我們把抽獎程式改良一下，讓它可以讀取檔案內容來初始化抽獎名單。

## 學習目標

在這則食譜中，您會學習到如何透過 **URI** 或 ```Windows.ApplicationModel.Package.Current.InstalledLocation``` API 的方式讀取應用程式套件內檔案的內容，並且學習如何運用 UWP 內建的 ```Windows.Data.Json``` 下的物件來解析 JSON 格式的資料。

  > 注意：應用程式套件中的檔案是**唯讀 (read only)** 的，所以您無法修改或刪除這個檔案。

## 事前準備

  * _(必要)_ 完成 [4.2 - 製作抽獎程式](../ch4/02_lucky_draw_app.md)的專案。

## 操作步驟

  1. 首先，在專案中新增一個 _JavaScript 檔案_，並且名稱取為 _attendees.json_，我們準備用這個檔案並且以 JSON 格式來放抽獎名單的資料。

  ![新增 JavaScript 檔案](https://skgitbook.blob.core.windows.net/uwprecipes/ch5/1_add_data_json.png)

  2. 建立 **attendees.json** 檔案後，到 Visual Studio 右下角（預設的位置）的屬性視窗中，將這個檔案的**建置動作**修改成_內容_，而**複製輸出到目錄**修改成_永遠複製_。

  ![修改 attendees.json 檔案的屬性](https://skgitbook.blob.core.windows.net/uwprecipes/ch5/1_setting_json_property.png)

  3. 打開剛才新增的 **attendees.json** 檔案，然後把抽獎名單以 JSON 格式放進這個檔案中，像是這樣：

	```json
	{
	  "Attendees": [
	    { "Name": "Alice", "Department": "Admin", "Gender": 0 },
	    { "Name": "Bob", "Department": "Legal", "Gender": 1 },
	    { "Name": "Catherine", "Department": "Finance", "Gender": 0 },
	    { "Name": "David", "Department": "Marketing", "Gender": 1 },
	    { "Name": "Eric", "Department": "Engineering", "Gender": 1 },
	    { "Name": "Frank", "Department": "Engineering", "Gender": 1 },
	    { "Name": "Gary", "Department": "Sales", "Gender": 1 },
	    { "Name": "Helen", "Department": "Marketing", "Gender": 0 },
	    { "Name": "Irene", "Department": "Marketing", "Gender": 0 },
	    { "Name": "James", "Department": "Sales", "Gender": 1 },
	    { "Name": "Kathy", "Department": "Sales", "Gender": 0 },
	    { "Name": "Leslie", "Department": "Sales", "Gender": 0 },
	    { "Name": "Mark", "Department": "Sales", "Gender": 1 }
	  ]
	}
	```

  我們在這個 JSON 檔案中定義了一個 JSON 格式的陣列（array），然後裡面的欄位就是跟 4.2 介紹的例子相同。

  4. 打開 **MainViewModel.cs** 的檔案，到之前寫好的 ```Init()``` 函式中把內容砍掉重練，改以下面的內容：

	```csharp
	private async void Init()
    {
      // 透過 URI 取得 attendees.json 檔案的物件
      var file = await StorageFile.GetFileFromApplicationUriAsync(
        new Uri("ms-appx:///attendees.json"));
      // 讀取檔案內容
      var jsonText = await FileIO.ReadTextAsync(file);

      // 將文字解析成 JsonObject 物件
      var jsonObj = JsonObject.Parse(jsonText);
      // 拿到 "Attendees" 這個 JsonArray
      var attendeesArray = jsonObj["Attendees"].GetArray();

      // 拿出每一個物件，然後藉此產生 Winner 物件加入
      foreach (var attendeeValue in attendeesArray)
      {
        var attendeeObj = attendeeValue.GetObject();
        Attendees.Add(new Winner()
        {
          Name = attendeeObj["Name"].GetString(),
          Department = attendeeObj["Department"].GetString(),
          Gender = (int) attendeeObj["Gender"].GetNumber()
        });
      }
    }
	```

  首先，因為我們在這個新的 ```Init()``` 中使用了檔案存取相關的非同步 APIs，所以在最上面的函式修飾詞中加入了 ```async``` 的關鍵字，讓編譯器知道這個函式中使用到了 ```await``` 關鍵字及非同步 API。

  因為我們產生的 **attendees.json** 檔案是跟著應用程式一起打包的，所以我們透過特定的 URI 格式：**ms-appx:///** 來取得檔案的路徑，所以可以直接用 _ms-appx:///attendees.json_ 來建立 ```Uri``` 物件，接下來就只是單純的檔案讀取操作了。

  檔案內容讀取出來後，接下來就是使用 ```Windows.Data.Json.*``` 的相關物件及 APIs 來解析及讀取 JSON 格式的資料，它讓開發人員可以輕易地像是存取 ```Dictionary``` 資料結構一樣直接使用鍵值就可以抓到欄位，但要注意的是，取得之後要根據它的資料型態使用 _GetArray()_、_GetString()_ 或是 _GetNumber()_ 這些方法來拿到真正的內容。

  經過這樣改寫之後，要異動名單就只需要修改 **attendees.json** 的內容，而不必跑來修改程式了。

  5. 如果不想用 URI 的方式存取檔案，也可以使用 ```Windows.ApplicationModel.Package.Current.InstalledLocation``` 物件取得應用程式套件的路徑，然後再指到檔案的路徑即可，像是這樣：

	```csharp
	var file = await StorageFile.GetFileFromPathAsync(
        Package.Current.InstalledLocation.Path + "\\attendees.json");
	```

    > 注意：```Windows.ApplicationModel.Package.Current.InstalledLocation``` 是一個 ```StorageFolder``` 物件，所以要加入 _Path_ 屬性才能取得路徑的字串資料，再與檔案路徑接起來。另一方面，透過這個方式指定路徑就要記得使用 **\\\\** 分隔而不是 **/**。

## 參考資料

  * [MSDN] [Create, write, and read a file](https://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/mt185401.aspx)