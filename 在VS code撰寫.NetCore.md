---
tags: Programing, C#, .NetCore, VSCode
---
# 在VS code環境撰寫.NetCore
[TOC]

# 環境設定
## 詳細請見[官網教學](https://docs.microsoft.com/zh-tw/dotnet/core/tutorials/with-visual-studio-code)
# 建立.NetCore專案
## 在目標資料夾CLI輸入 

### 建立專案
```bash=
dotnet new console # 當前目錄建立專案

# 或是

dotnet new console [project-name] # 在當前目錄建立資料夾 [project-name]
```

### 執行專案
```bash=
dotnet run # 當前目錄下專案

#或是

dotnet run --project [project-name] # 開啟 [project-name] 此目錄
```

接著在VS Code內安裝C# Extension套件後  
便可以進行Debug模式了  
若要執行Release模式，可以執行  
```bash=
dotnet run --configuration Release
```

Debug跟Release兩個模式的差異在於
---
### Debug模式

    不會對程式進行優化，以及提供break point的方式
    讓使用者可以逐步、修改變數來進行測試

---
### Release模式

    則是會進行程序的最佳化
    使程式能跑得更快、空間更小
    潛在問題則是可能導致Race Condition
---
    
# 發佈應用程式

執行以下指令來產生publish後結果  

```bash=
dotnet publish --configuration Release
```

在專案目錄底下的`bin/Release/netcoreapp3.1`分別有四個重要的檔案  

## [專案名稱].deps.json
    
這個檔案為應用程式runtime的參考套件檔  
如果有應用其他 `dll` 或 `.net core components`  
    
## [專案名稱].dll
    
這個檔案為可執行的動態連結函式庫  
可透過以下指令來執行  
    
```bash=
dotnet [專案名稱].dll
```

## [專案名稱].exe
    
這個檔案為應用程式的可執行檔  
不過某些作業系統不支援 ex. mac  
    
## [專案名稱].pdb
    
這是debug的象徵檔(?)  
當要對publich後的應用程式debug時便會需要用到  

## [專案名稱].runtimeconfig.json

這是應用程式的run-time設定檔  
它會識別目前執行的 .NET Core版本  
我們可以對它增加設定  
詳情參考  
[.NET Core run-time時的設定檔](https://docs.microsoft.com/zh-tw/dotnet/core/run-time-config/#runtimeconfigjson)  

# 執行方法

## 詳情參見[官網教學](https://docs.microsoft.com/zh-tw/dotnet/core/tutorials/publishing-with-visual-studio-code#run-the-published-app)

# 建立程式庫
```bash=
dotnet new sln
```

依序建立 `Library` 、 `Console`  
建立方式為：在專案目錄底下  

## 建立 Library

```bash=
dotnet new classlib -o [LibraryName]
dotnet sln add [LibraryName]/[LibraryName].csproj
```

接著修改 `[LibraryName].csproj` 以下為範例

```C#=
using System;

namespace UtilityLibraries
{
    public static class StringLibrary
    {
        public static bool StartsWithUpper(this string str)
        {
            if (string.IsNullOrWhiteSpace(str))
                return false;

            char ch = str[0];
            return char.IsUpper(ch);
        }
    }
}
```

接著將此函式庫 build 起來

```bash=
dotnet build
```

libary的部分目前便完成了

## 建立 Console

接下來建立Console應用程式

```bash=
dotnet new console -o [consoleAppName]
dotnet sln add [consoleAppName]/[consoleAppName].csproj
```

建立好Template後  
便可以修改Progarm.cs  
下面的程式碼著重在載入函式庫的部分  
用的是 `[LibraryName]/[LibraryName].csproj` 內的 `namespace`  

```C#=
using System;
using UtilityLibraries; // 重點在此

class Program
{
    static void Main(string[] args)
    {
         //Todo
         //在此寫執行程式
    }    
}
```

到此 Console Application的部分也完成了  

## 加入專案參考
接下來要把Console Application所引用到的外部函式庫給加入參考  
指令如下  

```bash=
dotnet add [consoleAppName]/[consoleAppName].csproj reference [LibraryName]/[LibraryName].csproj
```

## 執行應用程式
如標題  
而指令如下=

```bash=
dotnet run --project [consoleAppName]/[consoleAppName].csproj
```
# `第四章` Literal Types
# `第五章` Union and Intersection Types

