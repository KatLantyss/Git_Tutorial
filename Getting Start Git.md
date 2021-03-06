# Beginning Git

## What is Git?
Git 是一套分散式的版本控制系統，版本控制是一個開發團隊中不可或缺的工具，Git 最強大的一個特點就是可以無窮無盡的開 *branch* (分支)，好處就是不論是修 Bug ，開發新功能，或是研究 feature 都非常的方便。

## Setting Git
在每一次的 `Git commit`  都會記錄作者的訊息像是 name 及 email ，因此我們使用下面的指令來設定：
```
$ git config --global user.name "YOUR_NAME"
$ git config --global user.email "YOUR_MAIL"
```

加上 `--global` 表示是全域的設定。你可以使用 `git config --list` 這個指令來看你的 Git 設定內容：
```
$ git config --list
    user.name = YOUR_NAME
    user.email = YOUR_MAIL
```
或是其實 Git 的設定檔是儲存在你的家目錄的`.gitconfig` 隱藏檔中，你可以使用編輯器將他打開
```
$ cat ~/.gitconfig
    [user]
        name = YOUR_NAME
        email = YOUR_MAIL
```
使用終端機來操作 Git 常會讓人覺得一直打指令很繁瑣，因此 Git 也有提供 `alias` 的功能，例如你可以將`git status` 縮寫為 `git st`，`git checkout` 縮寫為 `git co` 等，你只要這樣設定：
```
$ git config --global alias.st status
```
這樣一來只要打 `git st` 就等同於打 `git status` 了。

空白對有些語言是有影響的(像是Ruby)，因此我們會希望 Git 去忽略空白的變化，這時候你需要在你的設定加入：
```
$ git config --global apply.whitespace nowarn
```
如此一來 Git 對於空白的變化便會忽略不計。

Git 預設輸出是沒有顏色的，我們可以讓他在輸出時加上顏色讓我們更容易閱讀：
```
$ git config --global color.ui true
```
## Starting Git (init, clone)
要開始使用 Git 你必須先建立一個 Git 的 Repository，你可以把它想做是一個資料庫的意思，有兩種方法可以建立一個 Git 的 Repository：

### 自己建立一個新的 Repository
例如我現在有個叫做 c++ 專案資料夾，我現在想要開始使用 Git 開始管理，因此我先將目錄切換到 c++ 底下後輸入`git init`
```
$ git init
Initialized empty Git repository in /Users/katlantyss/Projects/c++/.git/
```
這時你就會看到 Git 告訴你說已經在這邊建立好一個新的 Git Repository。

### Clone(複製)別人的 Repository
例如我們在 Github 上面看到人家的程式碼想要抓下來自己修改，或是團隊中別人的程式碼，這時候他們通常會有一個 Git 的檔案位置像是在 Github 的話你就會看到可以讓你複製 git 的 clone 位置，將他複製起來後到你的目錄下輸入 `git clone`
```
$ git clone https://katlantyss@github.com/katlantyss/c++.git
```
如此便會將這個 Git Repository下載到我們的資料夾， `git clone` 預設會將下載的 git 存成一樣檔名的資料夾，如果你要更改成別的名稱的話只需要在網址後面加上你想要更改的名稱即可，像是：
```
$ git clone https://katlantyss@github.com/katlantyss/Progamming.git java
```
這樣子下載下來的 Repository 的名稱就會從原本的 c++ 變成 java 了。

### Git的基本功(status, add, commit, log, .gitignore)
在一個 Git 的 Repository 中你可以輸入`git status`來檢查目前 Git 的狀態
```
$ git status
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
```
這代表目前是一個乾淨的目錄狀態，沒有未被追蹤或是被修改的檔案， On branch master 表示你目前正在名為 master 的 branch 上，等等會再說明 branch 的功用。我們現在在這個目錄新增一個 test 檔案後，再使用`git status` 來查看：
```
$ git status
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add ..." to include in what will be committed)
#
#   test.rb
nothing added to commit but untracked files present (use "git add" to track)
```
這時候你會看到我剛剛新增的 test.rb 檔案變成在 Untracked files (未被追蹤的檔案)，表示過去在這個 Git Repository 中從未有這支檔案，是一支未被追蹤的檔案，我們要把這個檔案讓 Git 能夠追蹤的話，我們使用 `git add` 這個指令來把它加入追蹤：
```
$ git add test.rb
$ git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached ..." to unstage)
#
#   new file:   test.rb
#
```
你可以看到原本 test.rb 還在 Untracked files 中，經過我們使用 `git add test.rb` 後他就變成了 Changes to commit，通常我們稱這個狀態叫做 stage ，修改過但還沒使用 `git add` 的檔案稱為 unstage 。


>Tips: 一次加入全部的檔案
如果你一次修改了很多檔案，懶得一個一個輸入 `git add` 的話，你可以輸入 `git add .` 這會幫你將所有剛剛修改過或新增加的檔案一次 Add 進 stage 狀態，但強烈不推薦這樣的作法，這樣的方法雖然方便但很容易會不小心加入一些其他不必要的檔案，一個正確的觀念是你必須隨時都很清楚你的檔案狀態，因此最好是手動將你確定要加入的檔案使用 `git add` 來加入，有一個更好的作法是使用互動模式 `git add -i` ，在互動模式下你可以方便的選擇你要加入的檔案，或是移除剛剛不小心加入的檔案 (revert)。


已經在 stage 狀態的檔案的下一步就是準備提交( commit )，commit 是寫程式時一個很重要的動作，一個 commit 在 Git 中就是一個節點，這些 commit 的節點就是未來你可以回朔及追蹤的參考，你可以想像就像是電玩遊戲時的存檔，每一個 commit 就是一次存檔，讓我們未來在需要的時候都可以回到這些存檔時的狀態。因此我們將剛剛做的修改提交：
```
$ git commit
```
此時你會看到畫面跳到你在 git 中設定的編輯器畫面：
```
#
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
#
# Initial commit
#
# Changes to be committed:
#  (use "git rm --cached …" to unstage)
#
#       new file: test.rb
#
```
這個視窗中最上面的空行是要給你寫下你這次 commit 的訊息，例如在這裡寫下 “Add test.rb file to test git function”來表示這一次提交的目的，強烈建議在 commit 的時候要盡量清楚表達這次 commit 的內容為何，因為這會讓你未來要回頭看 code 的時候能讓你快速的找到你想要找的內容，也能對團隊中其他成員了解你在做什麼。

在 commit 完後會顯出出你這次 commit 的更動：
```
[master 5f76371] add ignore files
 2 files changed, 8 insertions(+), 0 deletions(-)
 create mode 100644 .gitignore
 create mode 100644 test.rb
 ```
如果你覺得每次這樣跳出編輯器很麻煩，你也可以在 commit 時加上 `-m` 的參數來快速提交：
```
$ git commit -m "Add test.rb to test git function"
```
若使用 `-am` 的話還能將所有未被 add 的檔案一併 add 進來( 如果是第一次新增還沒有被 add 的檔案是不會一起加入的，只有之前已經被 add 過 commit 的檔案才會被加入 )，但就像前面所說這樣的作法並不推薦，你應該清楚的加入你應該加入的檔案就好：
```
$ git commit -am "Add test.rb to test git function"
```
若使用 `-v` 的話會列出更動的紀錄：
```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#   (use "git reset HEAD ..." to unstage)
#
#   new file:   .gitignore
#   new file:   test.rb
#
diff --git a/.gitignore b/.gitignore
new file mode 100644
index 0000000..61521a9
--- /dev/null
+++ b/.gitignore
@@ -0,0 +1,3 @@
+.DS_Store
+*.swp
+log/*.log
diff --git a/test.rb b/test.rb
new file mode 100644
index 0000000..23df1c1
--- /dev/null
+++ b/test.rb
@@ -0,0 +1,5 @@
+class Test
+  def test
+    puts "This is a test file"
+  end
+end
```
>加號(+)代表增加的部份，減號(–)代表刪除的部份。

我們可以使用 `git log` 的指令查看過去 commit 的紀錄：
```
$ git log
5f76371 - (HEAD, master) add ignore files (3 minutes ago) 
4ca268d - (origin/master) First commit (33 minutes ago) 
```
前面的亂碼代表的是當次 commit 的版號，後面的是 commit 的訊息、時間以及 commit的作者，你可以使用 `--stat`參數看到更詳盡的訊息：
```
$ git log --stat
5f76371 - (HEAD, master) add ignore files (7 minutes ago) 
 .gitignore |    3 +++
 test.rb    |    5 +++++
 2 files changed, 8 insertions(+), 0 deletions(-)

4ca268d - (origin/master) First commit (37 minutes ago) 
 lib/animal.rb       |    5 +++++
 spec/animal_spec.rb |    5 +++++
 2 files changed, 10 insertions(+), 0 deletions(-)
```
如果你想看到檔案更詳細的變更內容，你可以加上 `-p` 的參數：
```
$ git log -p
5f76371 - (HEAD, master) add ignore files (8 minutes ago) 
diff --git a/.gitignore b/.gitignore
new file mode 100644
index 0000000..61521a9
--- /dev/null
+++ b/.gitignore
@@ -0,0 +1,3 @@
+.DS_Store
+*.swp
+log/*.log
diff --git a/test.rb b/test.rb
new file mode 100644
index 0000000..23df1c1
--- /dev/null
+++ b/test.rb
@@ -0,0 +1,5 @@
+class Test
+  def test
+    puts "This is a test file"
+  end
+end
```
因此我們稍微做一下流程的整理：


#### **修改檔案 => 加入 stage (git add) => 提交( git commit )=> 繼續修改其他檔案**




> Tips: commit 的最佳時機？ 什麼時候該 commit ?
什麼時候才是 commit 的最好時機並沒有一個定論，大部分人會告訴你通常你完成了一個階段性的小工作就做一次的 commit ，我的感覺是當你想要記錄目前的狀態的時候就是你 commit 的最佳時機，例如說剛完成某個page，某個任務需求而你想要做個記錄的時候。



有些檔案我們不希望加入版本控制的追蹤，例如說Database的schema或是一些log檔，這時候你可以將他們加入 `.gitignore` 中來讓 Git 忽略他們，使用編輯器來打開你的 `.gitignore` 檔案。
```
$ vim .gitignore
```
在這邊假設我想將 Mac 自動產生的.DS_Store， vim 的 swp 暫存檔及 log 檔忽略，我便分行加入：
```
.DS_Store
*.swp
log/*.log
```
如此一來便會將這些檔案從git追蹤忽略了。


> Tips: 被加入 gitignore 的檔案一樣出現在 status 中？
有時候你會發現即時你將檔案加入了 `.gitignore` 卻一樣會出現在 Git 的追蹤狀態中，這是由於你想要忽略的檔案之前已經被 Git 追蹤了，因此你現在想要讓 Git 忽略他的話只能先將他刪除然後再 commit 一次，再來這支檔案再出現就不會再追蹤這支檔案了。
****
