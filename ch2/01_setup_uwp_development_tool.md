# 2.1 安裝 UWP 開發工具

在這一篇食譜中，我們會介紹如何安裝 UWP 開發工具，包括 Visual Studio 、UWP 開發工具與模擬器。

## 事前準備

  * 已經安裝好 **Windows 10 專業或企業版 64 位元版**的電腦。
  		
  	> 若您已經擁有 Windows 7、Windwos 8 或 Windows 8.1 的合法授權，可以直接在 Windows Update 中免費升級 Windows 10；若尚無 Windows 10 的合法授權，也可以至 [TechNet 評估中心](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)下載 Windows 10 企業評估版，可以免費試用 90 天。
  	
  * (_選擇性_) 將電腦開啟硬體虛擬化的功能。
  
  	> 若要執行 Windows 10 Mobile 的模擬器，系統必須安裝並開啟 Hyper-V 虛擬化環境，這部份需要電腦硬體支援 SLAT (Second Layer Address Translation) 虛擬化技術。詳細內容請參考[如何檢查處理器是否支援第二層位址轉譯 SLAT](https://support.microsoft.com/zh-tw/kb/2781250)這篇文章。
  	
  	> 不過就算 CPU 不支援或是沒開啟這個功能，還是可以執行 UWP 的應用程式來偵錯，只是不能使用 Windows 10 Mobile 的模擬器而已。

## 操作步驟

1. UWP 開發工具結合 Visual Studio 2015 開發工具，您可以使用 Community、Professional 或是 Enterprise 版本來整合 UWP 開發工具。若您尚未使用過 Visual Studio，可以在 [Visual Studio 官方網站](http://www.visualstudio.com/zh-tw/)免費下載 Visual Studio Community。

    ![Visual Studio 官網](https://skgitbook.blob.core.windows.net/uwprecipes/ch2/1_vshome.jpg)

2. 執行安裝程式時（_注意：執行 Visual Studio 2015 安裝程式時必須要連接網際網路_），首先選擇**自訂**安裝，按下**下一步**後，根據需要勾選要在 Visual Studio 2015 中安裝的工具，要開發 UWP 應用程式，就必須注意勾選**通用 Windows 應用程式開發工具**。

	![自訂安裝](https://skgitbook.blob.core.windows.net/uwprecipes/ch2/1_setup_custom.png)
	
	![勾選 UWP 開發工具](https://skgitbook.blob.core.windows.net/uwprecipes/ch2/1_setup_check_uwp_tools.png)
	
3. 勾選需要的工具後（_注意：日後若需要安裝其它工具還可以再啟動安裝程式來勾選_），按**下一步**，接下來 Visual Studio 安裝程式便會根據勾選的工具下載安裝。

4. 安裝完成，執行 **Visual Studio 2015**，然後新增專案，若能在 _Visual C# > Windows_、_Visual Basic > Windows_、_Visual C++ > Windows_、以及 _JavaScript > Windows_ 下能看到**通用**（英文版則是 **Universal**）的範本，並且能夠選取建立專案，那就表示正常安裝了。

    ![勾選 UWP 開發工具](https://skgitbook.blob.core.windows.net/uwprecipes/ch2/1_uwp_project_template_csharp.png)


## 參考資源

  * [Visual Studio 官方網站](http://www.visualstudio.com/zh-tw/)。
  * [通用 Windows 平台開發](https://www.visualstudio.com/zh-tw/features/universal-windows-platform-vs.aspx)。
  * [Windows 開發人員中心](http://dev.windows.com/zh-tw)。