---
title: 使用 SHFB 自動化建立 Assembly 的 Help 文件
date: 2022-02-09 20:11:47
tags:
- SHFB
categories:
- 技術文件
---

# 使用 SHFB 自動化建立 Assembly 的 Help 文件

* SHFB：Sandcastle Help File Builder
* 一套能動態產生 MSDN Style 文件的工具
* 可以指令化，方便整合 CI/CD
* GitHub：https://github.com/EWSoftware/SHFB/releases

## 背景

### 問題描述

專案開發時，PG最不喜歡的無非是：

1. 寫文件
2. **對方沒寫文件**

> 心裡OS：「寫Code都來不及，還要維護文件」，然後....文件就這樣爛了....

因為以上種種問題，Live Document 需求就由然而生

如果可以「依據實際的 Code與註解，動態產生文件」...不是很美好嗎？？

接下來就來說明如何實作。

## 如何開始

### 安裝

#### Step 1 - 下載 SHFB

> https://github.com/EWSoftware/SHFB/releases

![下載 SHFB](https://i.imgur.com/3tXWvBI.png)

#### Step 2 - 安裝主程式

> 雙擊安裝程式→Next→Install SHFB

![](https://i.imgur.com/gMJZYDg.png)
![](https://i.imgur.com/sU41OvM.png)

#### Step 3 - 安裝 Visual Studio 插件`重點`

![](https://i.imgur.com/5ySl5wn.png)

#### Step 4 - 安裝 Visual Studio Schemas(可選的)

![](https://i.imgur.com/gzWW1bA.png)

#### Step 5 - 安裝 Visual Studio Snippets(可選的)

![](https://i.imgur.com/gR8vTrY.png)

#### Step 6 - 完成安裝

![](https://i.imgur.com/KnAVyS2.png)

### 設定

#### Step 1 - 啟用「建置時產出XML文件檔案」

> 專案上點擊滑鼠右鍵 → 屬性 → 建置頁籤 → 勾選 XML文件檔案 → 儲存後，進行編譯

![](https://i.imgur.com/Co83pqq.png)

:::info
**什麼是XML文件檔案？**

主要是編輯的時候，根據我們在開發程式時所加上的XML註釋，動態產生的文檔。
:::

例如：

```c#
/// <summary>
/// 這是一個範例方法
/// </summary>
/// <param name="param">輸入的參數</param>
/// <returns>回傳的結果</returns>
public Result MyMethod(Input param)
{
    //do soming..

    return new Result();
}
```

#### Step 2 - 新建 Document 專案

> 方案上點擊滑鼠右鍵 → 加入 → 新增專案

![](https://i.imgur.com/GGcQOrf.png)

> 選取「Documemtsation」 → 指定專案名稱 → 確定

![](https://i.imgur.com/uII1RtS.png)

#### Step 3 - 加入需要產出文件的 dll、 xml

> Documentation Source 滑鼠右鍵 → Add Documentation Source → 找到需要產出文件的 dll 與 xml

![](https://i.imgur.com/XIZt1pn.png)

#### Step 4 - Documemtsation 專案配置

> Documemtsation專案 滑鼠右鍵 → 屬性

##### Step 4-1 配置「Build」頁籤

1. 左邊頁籤選擇「Build」
2. PresentationStyle ：選擇`VS2013`
3. Build These help file formats`(輸出文件格式)`：Website(HTML/ASP.NET)
4. Syntax：輸出程式語言格式

![](https://i.imgur.com/zce8Zf4.png)

##### Step 4-2 配置「Help File」頁籤

1. 左邊頁籤選擇「Help File」
2. Help title：自訂 title
3. Help File Language：文件語言(有中文)

> 其他頁籤的設定，可依需求再進行設定 → 儲存後，即可編譯。

![](https://i.imgur.com/v4L1yQs.png)

### 產生說明文件

> 建置成功 → Documemtsation專案 底下會產生「Help」資料夾 → 裡面有產生的說明文件

![](https://i.imgur.com/iy52rlf.png)

![](https://i.imgur.com/lmUQSly.png)

---

![](https://i.imgur.com/YDjrY46.png)

### 產生 「Word」格式說明文件

> Documemtsation專案 滑鼠右鍵 → 屬性

1. 左邊頁籤選擇「Build」
2. PresentationStyle ：選擇`Open XML Document`
3. Build These help file formats`(輸出文件格式)`：Open XML(docx)

![](https://i.imgur.com/oI0X88P.png)

> 儲存後，編譯，Help資料夾即生成 Word Help 文件。

![](https://i.imgur.com/mHYLuJy.png)

### 指令 - 方便整合 CI/CD

```powershell
#Powershell SHFB 自動建置佈暑 HELP 文件指令

#Sandcastle Help File Builder 根目錄 (請確認執行路徑)
$SHFB_Path = "/property:SHFBROOT=C:\Program Files (x86)\EWSoftware\Sandcastle Help File Builder"
 
#Sandcastle Help File Builder Documemtsation 專案目錄 (請自行調整路徑)
$SHFB_ProjectPath = "C:\Nick\Gitlab\GameServer\Indica\Indica.Help.Doc\Indica.Help.Doc.shfbproj"
 
#文件站台 (請自行調整路徑)
$Result_WebSite = "C:\Nick\Gitlab\GameServer\Indica\Indica.Help.Doc\Help\index.html"
 
#Compiler
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe /p:Configuration=Debug $SHFB_Path $SHFB_ProjectPath

#使用Chrome 查看文件(自行調整相關工具)
START Chrome $Result_WebSite
```

![](https://i.imgur.com/WFm4Fee.png)

![](https://i.imgur.com/KC7opJw.png)
:::success
這邊的警告是告訴我們相關XML注解沒有填寫。
:::

## 結論

* 養成 `XML注解` 習慣
* 產文件可以很優雅
* 不需要維護文件
* 整合 CI/CD
* 如果想制作 WebApi2，更加適合的套件
  * [Microsoft Help Page](http://bit.ly/2zIK0KJ)
  * [Swagger](https://swagger.io/)

## Ref

[Sandcastle Help File Builder 安裝紀錄](http://bit.ly/2Fe3cG4)
[使用 Sandcastle Help File Builder 建立 Assembly 的補助說明檔案](http://bit.ly/2F9O7Fp)
[使用 Sandcastle Help File Builder 製作類別庫文件](http://bit.ly/2FcY3OI)
[使用 Sandcastle工具在Visual Studio環境產生Help文件](http://bit.ly/2FdvVL9)

###### tags: `說明文件` `SHFB`

