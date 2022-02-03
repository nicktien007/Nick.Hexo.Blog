title: 常用Git 指令
author: Nick
tags:
  - Git
categories:
  - 技術文件
abbrlink: 2237860706
date: 2021-01-25 18:30:00
---
## Git 基礎架構
<img src="https://i.imgur.com/sYRriVZ.png" alt="" width="600"/>


## Git 基礎指令

- 查看版本： `git --version`
- 設定個人資料
    - 姓名：`git config --global user.name "nick"`
    - mail：`git config --global user.email "mail"`
    - 查看git設定檔：`git config --list`
- 初始化：`git init`
- 查詢當前狀態：`git status`
- 將檔案加到索引：`git add .`
- 將索引檔加入Commit：`git commit -m "異動內容"`
- 查詢Commit紀錄：`git log`
- 下載遠端Repository：`git clone 遠端網址`
- 拉取遠端Repository：`git pull`
- 更新遠端Repository：`git push origin master`

## Git 遠端 Repository

### Git 和 GitHub
- Git 是一個分散式版本控制軟體，可藉由它產生一個*儲存庫( git Repository)*。
- Github：支援 git 程式碼存取和*遠端託管儲存庫*的平台服務
- 關係像是本地端有一個 index.html，但可以放到 dropbox、Google Drive 進行雲端託管

### 熱門遠端儲存庫
* [GitHub](https://github.com/)：擁有 GitHub Pages 功能，可擁有私人數據庫，免費方案是 3 人以下
* [Bitbucket](https://bitbucket.org/product/pricing)：可擁有私人數據庫，免費方案是五人以下團隊
* [GitLab](https://about.gitlab.com/)：自架 Git 伺服器，有提供 web 視覺化管理介面，常用於企業內部開發

> 賴人包：公司專案的小型團隊可用 Bitbucket，想要有一個公開對外網站的話，可用 GitHub

###  遠端儲存庫(Repository)操作
* 註冊遠端儲存庫：`git remote add origin 遠端儲存庫網址`
* 更新資料到遠端 master 分支：`git push -u origin master`
* `-u` 是指他預設會推到哪個遠端數據庫服務(平常可省略)
* origin 可以改成它的遠端數據庫名稱，例如 `git push -u github master`

### Git 版本觀念
- branch：分支，預設分支叫`master`
- HEAD：指標，代表目前版本的停留位置
- origin：預設遠端儲存庫名稱
- 查看版本內容：`git checkout 編號`
- 返回最新版本：`git checkout master(分支名稱)`

### Git 還原觀念
![](https://i.imgur.com/jH20ytf.png)

- **新增檔案，檔案還沒加入追蹤，清空工作目錄**
    - 顯示這次操作會刪除的檔案：`git clean -n`
    - 強制刪除檔案：`git clean -f`
- **檔案已加入追蹤，清空工作目錄**
    - 還原工作目錄上已更改的檔案：`git checkout -- <file>`
- **檔案已加入到索引，退到工作目錄**
    - `git reset HEAD`

#### 版本還原
* 還原上個版本：`git reset HEAD^`
* 還原前兩個版本：`git reset HEAD^^`
* 還原前兩個版本，所有更新檔案都放棄：`git reset HEAD^^ --hard`
* 觀看詳細歷史紀錄：`git reflog`
* 還原到特定 commit：`git reset commit編號 --hard`
* [git reset 參數介紹](https://gitbook.tw/chapters/using-git/reset-commit.html)

#### **checkout** 與 **reset** 差異
- checkout：移動`HEAD`
- reset：移動`branch`

## Git 分支 (branch)

### 為什麼要分支
- 多人協作時，不可能都在 master
- 可以讓 master 都是正式版資料，可以開分支來做測試或開發，藉此不影響正式主機分支

### 分支簡介
- 分支就像便利貼，貼在某個commit上
- 分支合併：兩個commit進行合併

#### 開分支
* 新增分支：git branch 分支名稱 (**遠端分支名稱**)
* 查看分支：`git branch`
* 切換分支：`git checkout 分支名稱`
* 刪除分支：`git branch -d 分支名稱 、-D 是強制刪除`

##### 常見分支
- master：預設分支
- develop(dev)：開發分支
- fetaure：開發新功能分支

#### 合併分支
* 合併分支：`git merge 分支名稱`
* 取消快轉：`git merge 分支名稱 --no-ff`
* 觀看線圖：`git log -—oneline --graph`
* 還原合併前狀態：`git reset —hard ORIG_HEAD`

##### 快轉機制？
- 使用快轉(`預設`)：本地端與遠端兩條相同branch合併時，可使用快轉
- 取消快轉(`--no-ff`)：兩個不同branch合併時，建議`取消快轉`，在線圖上會跑出**支線**

![](https://i.imgur.com/eOSVs8S.png)

##### 解決衝突

>HEAD 是當前 HEAD 分支位置，develop 是你想合併的分支

```
<<<<<<< HEAD
      <h1>我是標題</h1>
=======
      <h2>我是大標題</h2>
>>>>>>> develop
```

>取消 merge 衝突狀態： `git merge --abort`

#### rebase
> 另一種合併方式

>語法： git rebase 分支名稱

**rebase前**
![](https://i.imgur.com/DCnfiSZ.png)

**rebase後**
![](https://i.imgur.com/EYpsIom.png)


> 看起來是`剪下、貼上`，實際上是**復製、貼上**(SHA1值不一樣)

> [進階用法](https://blog.yorkxin.org/2011/07/29/git-rebase.html)
> [修改歷史訊息](https://gitbook.tw/chapters/rewrite-history/change-commit-message.html)

##### 還原方式
- `git reset ORIG_HEAD --hard`
- `git reflog`

#### cherry-pick

> 撿別的分支的 Commit 過來合併

語法：
`git cherry-pick 編號1 編號2 編號3`

參數：
`--no-commit`：撿過來但先不合併

## Git 其它狀況

### 做到一半，臨時要切換到別的任務
> https://gitbook.tw/chapters/faq/stash.html

- 先Commit，稍候再使用`git reset HEAD^`回來做
- 使用`git stash`暫存工作
    - `git stash list`：查看當前分支stash列表
    - `git stash pop stash@{編號}`：回來進行stash@{編號}的工作，並==移除==stash
    - `git stash apply stash@{編號}`：拿取工作==不移除==stash
    - `git stash pop "不指定stash@{編號}"`：從編號最小開始拿
    - `git stash drop stash@{編號}`：移除stash
    
### 不小心把ignore的檔案加進版控
> 加進版控會導致`.ignore` 對該資料夾或檔案的ignore失效

**1 .移除跟蹤**

移除指定文件夾即文件夾下所有文件：
`git rm --cached --force -r .idea 
`

移除指定文件：

`git rm --cached --force ydq-api/ydq-api.iml
`

**2. 進行commit，再下次commit就可ignore了**

## 補充
- [Git 練習網站](https://learngitbranching.js.org/)

> Generating a new SSH key：
```
ssh-keygen -t rsa -b 4096 -C "trojanhorse0077@gmail.com"
```

### Cli 終端機 基本指令

| Windows | Mac/Linux | 說明 |
| -------- | -------- | -------- |
| cd 路徑   | cd 路徑     | 前往資料夾路徑     |
| cd  | pwd     | 取得目前所在的位置     |
| dir  | ls     | 顯示資料夾裡的檔案     |
| mkdir  | mkdir     | 新增資料夾     |
| 無  | touch     | 開新檔案     |
| copy  | cp     | 複製檔案     |
| move  | mv     | 移動檔案     |
| del  | rm     | 刪除檔案     |
| cls  | clear     | 清除畫面上內容     |