# Git的使用筆記

## 1. 終端機常用指令

指令|操作
---|---
清除畫面|`ctrl+L`或`clear`
切換目錄|`cd`+目錄名稱
往上一層|`cd ..`
目前位置|`pwd`
建立資料夾|`mkdir`+資料夾名稱
目前位置的所有資料夾|`ls`+`--al`
建立檔案|`touch`+檔名
複製檔案|`cp`+複製的檔案+新檔名
更改檔名|`git`+`mv`+原檔名+新檔名
刪除檔案|`rm`+檔名
將所有檔案都加至暫存區|`git`+`add`+`--all`
將所有.html檔案都加至暫存區|`git`+`add`+`*.html`
檢視所有commit紀錄|`git`+`log`+`--oneline`+ `--graph`
將檔案從git中移除(不被git控管追蹤)|`git`+`rm`+檔名+`--cached`
查詢代碼是誰寫的|`git`+`blame`+檔名
^|`git`+`blame`+ `-L`+`(第?行,到第?行)`+檔名

## 2.用git 對目錄進行版本控制

    $cd 目錄
    $git init            #建立一個git目錄*整個專案目錄裡，什麼檔案或目錄刪了都救得回來，但`.git`目錄只要刪了就沒辦法了。
    $vi

    hello,git            #進insert模式後輸入hello,git
    :wq welcome.html     #進入normal模式存檔離開並命名檔名為welcome.html

- 此時git status 顯示Untracked files:

    welcome.html

  表示尚未被git追蹤，剛加入目錄而已

    $git add welcome.html #使git追蹤此檔

- 此時git status 顯示new file:

    welcome.html

    表示這個檔案已經被安置到暫存區（Staging Area）(index)，等待稍後跟其它檔案一起被存到儲存庫裡。

## 3.檔案放入暫存區裡又使用編輯器修改檔案內容

會導致出現兩個檔案(一個是修改前且加入暫存區的檔，一個是修改後未被加入暫存區的檔)。

**解決方法:**
再次用`git add` +檔名把檔案加入暫存區。

    $git add  welcome.html

    $git commit -m”init commit”    #說明在這次commit做了什麼事情，將暫存區的檔案移至儲存庫

***Git 每次的 Commit 都只會處理暫存區（Staging Area）裡的內容。***

## 4.不想讓檔案在被git控管

    $git rm welcome.html --cached   #讓welcome.html不再被控管
    $git status 
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            deleted:    welcome.html

    Untracked files:
        (use "git add <file>..." to include in what will be committed)

            welcome.html
    $git commit -m"remove welcome.html to be untracked"

## 5.修改commit 紀錄

    $git commit --amend --m "修改commit註解"
    $git log --oneline
    614a90c 修改commit註解
    7dbc437 add hello.html
    657fce7 add container

***`--amend`只能用於最後一次commit的註解***

## 6.追加檔案到最近一次的commit裡

    $git status 
    On branch master
    Untracked files:
        (use "git add <file>..." to include in what will be committed)

        cinderella.html

    nothing added to commit but untracked files present (use "git add" to track)

`cinderella.html`剛加入，狀態為`Untracked`

    $git add cinderella.html
    $git commit --amend --no-edit     #`--amend`追加提交
    [master 3128d00] update story
    Date: Wed Aug 16 05:42:56 2017 +0800
    2 files changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 cinderella.html
    create mode 100644 config/database.yml

***`--no edit`為告知git 不編輯commit 訊息，如不打，就會跳出編輯器編輯commit 訊息***

***空目錄無法被提交:***
git在計算、產生物件時無法感應到空目錄，故需先於空目錄下放一個`.keep`的檔案，才能順利`git add`&`git commit`

## 7.救回被刪除的檔案或目錄

    $git rm *.html       #刪除所有.html的檔案
    $ls -al              #檢視目錄，發現`.html`檔案都刪除了
    total 8
    drwxr-xr-x   6 eddie  wheel   204 Aug 17 04:38 .
    drwxrwxrwt  65 root   wheel  2210 Aug 17 03:38 ..
    drwxr-xr-x  15 eddie  wheel   510 Aug 17 04:44 .git
    -rw-r--r--   1 eddie  wheel   232 Aug 16 17:14 .gitignore
    drwxr-xr-x   3 eddie  wheel   102 Aug 16 16:45 config
    drwxr-xr-x   2 eddie  wheel    68 Aug 16 17:19 images
    $git status          #檢視目前況，發現刪除了4個.html檔案
    On branch master
    Changes not staged for commit:
    (use "git add/rm <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

    deleted:    cinderella.html
    deleted:    index.html
    deleted:    welcome.html
    deleted:    world.html

    no changes added to commit (use "git add" and/or "git commit -a")

救回`cinderella.html`

    $git checkout cinderella.html
    $ls -al                  #發現cinderella.html回來了
    total 8
    drwxr-xr-x   7 eddie  wheel   238 Aug 17 04:46 .
    drwxrwxrwt  65 root   wheel  2210 Aug 17 04:45 ..
    drwxr-xr-x  15 eddie  wheel   510 Aug 17 04:46 .git
    -rw-r--r--   1 eddie  wheel   232 Aug 16 17:14 .gitignore
    -rw-r--r--   1 eddie  wheel     0 Aug 17 04:46 cinderella.html
    drwxr-xr-x   3 eddie  wheel   102 Aug 16 16:45 config
    drwxr-xr-x   2 eddie  wheel    68 Aug 16 17:19 images
    $git checkout .
    $ls -al                  #發現.html的檔案全回來了
    total 24
    drwxr-xr-x  10 eddie  wheel   340 Aug 17 05:34 .
    drwxrwxrwt  65 root   wheel  2210 Aug 17 04:45 ..
    drwxr-xr-x  15 eddie  wheel   510 Aug 17 05:34 .git
    -rw-r--r--   1 eddie  wheel   232 Aug 16 17:14 .gitignore
    -rw-r--r--   1 eddie  wheel     0 Aug 17 04:46 cinderella.html
    drwxr-xr-x   3 eddie  wheel   102 Aug 16 16:45 config
    drwxr-xr-x   2 eddie  wheel    68 Aug 16 17:19 images
    -rw-r--r--   1 eddie  wheel   161 Aug 17 05:34 index.html
    -rw-r--r--   1 eddie  wheel    27 Aug 17 05:34 welcome.html
    -rw-r--r--   1 eddie  wheel     0 Aug 17 05:34 world.html

***`git checkout`指令是從暫存區抓檔案或內容回來覆蓋回工作目錄，亦即，這些被刪除的檔案或目錄會回復到上一次commit 的狀態。***

## 8.拆掉上一次的commit內容

剛才的commit後悔了，想要拆掉重做

    $git log --oneline       #查詢所有commit紀錄
    e12d8ef (HEAD -> master) add database.yml in config folded
    85e7e30 add hello
    657fce7 add container
    abb4f43 update index page
    cef6e40 create index page
    cc797cd init commit
    $git reset e12d8ef^      #我要前往e12d8ef以前一次的commit狀態(我要拆掉e12d8e這次的commit)

***`^`表示前一次，`git reset e12d8ef^^`表示前兩次，即為657fce7；前五次的cc797cd則使用`git reset e12d8ef~5`。***

也可以使用三種方法

1

    $git reset master^

因為`HEAD`和`commit`都指向e12d8ef這次commit

2

    $git reset HEAD^

3

    $git reset 85e7e30

*`rest`的三種參數

模式|mixed 模式|soft 模式|hard 模式
:---|:---:|:---:|:---:
工作目錄|不變|不變|丟掉|
暫存區|丟掉|不變|丟掉|

## 9.拆掉commit 後再把它加回來

    $git log --oneline  
    e12d8ef (HEAD -> master) add database.yml in config folder
    85e7e30 add hello
    657fce7 add container
    abb4f43 update index page
    cef6e40 create index page
    cc797cd init commit
    $git reset HEAD~2           #回到657fce7commit的狀態
    $git log --oneline
    657fce7 (HEAD -> master) add container
    abb4f43 update index page
    cef6e40 create index page
    cc797cd init commit
    $git reset 85e7e30 --hard   #回到85e7e30commit的狀態，並且強制放棄rest之後修改的檔案

如記不起commitSHA-1 值，也可以使用

    $git reset HEAD --hard               #回到657fce7 commit的狀態，並且丟掉了拆出來的檔案
    HEAD is now at 657fce7 add container
    $git reflog
    657fce7 (HEAD -> master) HEAD@{0}: reset: moving to HEAD~2
    e12d8ef (origin/master, origin/HEAD, cat) HEAD@{1}: checkout: moving from cat to master
    e12d8ef (origin/master, origin/HEAD, cat) HEAD@{2}: checkout: moving from master to cat            #查看`HEAD`變動的情況，變動前的`HEAD`是e12d8ef 
    $git reset e12d8ef --hard            #檔案又撿回來回到e12d8efcommit的狀態了

## 10.什麼是`HEAD`

為一指標，指向分支(branch)，HEAD 通常會指向目前所在的分支。

    $git branch          #目前有三個分支，`HEAD`指`master`分支
    cat
    dog
    * master
    $cat .git/HEAD       #目前`HEAD`指向`master`分支
    ref: refs/heads/master
    $git checkout cat    #切換分支到cat
    Switched to branch 'cat'
    $cat .git/HEAD       #查看目前的cat .git的`HEAD`
    ref: refs/heads/cat

## 11.commit 一個檔案中的部分內容

    $git add -p index.html             #只將部分的檔案內容放入暫存區
    diff --git a/index.html b/index.html
    .
    .
    .
    Stage this hunk [y,n,q,a,d,/,e,?]? #是否要把這個區塊（hunk）加到暫存區

`y`-->全部加入暫存區

`e`-->部分加入暫存區

    $e
    # Manual hunk edit mode -- see bottom for a quick guide.@@ -6,6 +6,16 @@
    .
    .
    .
    # ---
    # To remove '-' lines, make them ' ' lines (context).
    # To remove '+' lines, delete them.
    # Lines starting with # will be removed.
    #
    # If the patch applies cleanly, the edited hunk will immediately be
    # marked for staging.
    # If it does not apply cleanly, you will be given an opportunity to
    # edit again.  If all lines of the hunk are removed, then the edit is
    # aborted and the hunk is left unchanged.
    $ 直接刪除想刪除的行，存檔離開      #此時，暫存區有只有部分內容的檔案，工作區有全部內容的檔案

[參考資料-為你自己學Git](https://gitbook.tw/)

---

- [Git的使用筆記](#git的使用筆記)
  - [1. 終端機常用指令](#1-終端機常用指令)
  - [2.用git 對目錄進行版本控制](#2用git-對目錄進行版本控制)
  - [3.檔案放入暫存區裡又使用編輯器修改檔案內容](#3檔案放入暫存區裡又使用編輯器修改檔案內容)
  - [4.不想讓檔案在被git控管](#4不想讓檔案在被git控管)
  - [5.修改commit 紀錄](#5修改commit-紀錄)
  - [6.追加檔案到最近一次的commit裡](#6追加檔案到最近一次的commit裡)
  - [7.救回被刪除的檔案或目錄](#7救回被刪除的檔案或目錄)
  - [8.拆掉上一次的commit內容](#8拆掉上一次的commit內容)
  - [9.拆掉commit 後再把它加回來](#9拆掉commit-後再把它加回來)
  - [10.什麼是`HEAD`](#10什麼是head)
  - [11.commit 一個檔案中的部分內容](#11commit-一個檔案中的部分內容)
