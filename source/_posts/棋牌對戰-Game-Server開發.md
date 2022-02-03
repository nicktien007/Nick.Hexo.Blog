title: 棋牌對戰 Game Server開發
author: Nick
tags:
  - GameServer
  - 棋牌對戰
  - .net Core
  - Google Protobuf
  - Nuget
categories:
  - 作品集
abbrlink: 625696656
date: 2021-01-29 20:37:00
---
{% gp 2-2 %}
<img src="https://i.imgur.com/qvejjHk.jpg" alt="" width="350"/>
<img src="https://i.imgur.com/098KPOY.png" alt="" width="350"/>
{% endgp %}

## 特點
- CQ9 第一款棋牌對戰遊戲
- 負責項目：
	- Server架構
    - 後端開發
    - 協議
- 採用 .NET Core 3 開發
- 採用主流的遊戲架構：**ECS架構**(Entity-Component-System)
- 協議採用 Google Protobuf
- 後期進行 **NuGet 模組化**，開發遊戲超 Easy !!

## GameServer 架構

## 架構圖
![](https://i.imgur.com/iRrJmLi.png)

## 基礎元件介紹

### Game
- 頂層類別，主要包含`Scene`、`EventSystem`

### Componet
- 框架裡萬物皆是`Componet`
- 建構子會調用`IdGenerator類`產生唯一的 Id
- 繼承`IDisposable`，在調用Dispose後會被ObjectPool 回收

### ComponentWithId
- 繼承 Componet  
- 使用指定 Id創建 Componet

### Entity
- 可以`掛載` Componet
- 繼承 `ComponentWithId`

> 以上皆使用`ComponentFactory` 靜態類別創建

### Scene
- 重要的單例實體
- 繼承 Entity 類，包含的 component代表了服務端進程能提供的基礎功能集合

### EventSystem
- 單例實體
- 在component 創建時，註冊事件到相對應的列表
- 主要事件介面
	- IAwake
    - IStart
    - IDestroy
    - ILoad
    - IUpdate
    - ILateUpdate
    - IChange
    - IDeserialize



## 運作流程

{% note success %}
框架並自定義一個`OneThreadSynchronizationContext`類，在有異步回調到來的時候保存delegate和參數到一個多線安全的Queue，然後一個While True 迴圈不停調用`OneThreadSynchronizationContext.Update`，在主線程中取出本次主循環產生的所有異步回調，從而保證了所有的回調函數都被捕捉到單一主線程執行。
{% endnote %}

**{% label success@
該框架進入點為`app.proj` 裡面只有一個帶 main的program類型，啟動之後主要分以下四個流程 %}**


### 第一步
首先註冊 Model程序集中提供的各種基礎 Model類，隨後加載 Hotfix中可以熱更新的類型並且註冊到`EventSystem`。一方面是提供IAwake、ILoad、IUpdate 等等介面中的事件處理，一方面檢索各類 Attribute並且註冊到相應容器中以便後續查找。
### 第二步
載入`OpcodeTypeComponent`和`MessageDispatherComponent`提供Server 端進程相應網絡消息回調的功能，後需章節會繼續介紹。

### 第三步
隨後是根據本身的 appType添加自身需要的Component，後需章節會繼續介紹

### 第四步
進入Sever 主循環：每此循環休息1ms，處理發生的異步回調；並處理各種Compont的update事件。