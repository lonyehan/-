---
tags: Programing, C#, .NetCore, VSCode
---
# 在VS code環境撰寫.NetCore
[TOC]

# 第一段 環境設定
## 詳細請見[官網教學](https://docs.microsoft.com/zh-tw/dotnet/core/tutorials/with-visual-studio-code)
# 第二段 建立.NetCore專案
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
    
# `第三章` Funcions
# `第四章` Literal Types
# `第五章` Union and Intersection Types

