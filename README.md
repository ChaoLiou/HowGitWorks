# HowGitWorks
- I init a local repo, and copy the `.git` directory to this `HowGitWorks` repo.
- Then, I start to do some simple git commands, but when ever I command, I replace the `.git` in the `HowGitWorks` repo.
- So that I can study the changes in `.git` directory, I especially focus on the `.git/objects/` directory and check the object content

## Often Used Command
- git cat-file
  - use this command to check the binary file under `.git/objects`
  - `git cat-file -p abcd###`: check the content of `.git/objects/ab/cd###`
  - `git cat-file -t abcd###`: check the type of `.git/objects/ab/cd###`

[List all binary files under .git objects](https://github.com/ChaoLiou/HowGitWorks/wiki/List-all-binary-files-under-dotgit-objects-)

> 上面的說明是給有興趣的外國人想要理解這個 repo 在做甚麼, 以下開始解說, 並且不想再用英文寫了...

> 以下講的 `.git` 皆指 .git 資料夾

## 引導
- 這個專案是用來理解 - 下 git 指令到底實際上對 `.git` 做甚麼事情
- 首先, 我們都知道 git 是用來幫我們的專案做`版控`, 所有專案的歷史都會存到 `.git`
- 那現在我們想知道 git command 對 `.git` 做了甚麼事情, 換句話說是想知道 `.git` 的歷史, 那就幫 `.git` 做版控吧

## 起步
- 因此, 我們需要兩個 git repo, 一個 repo 是當作`觀察對象`, 是 local repo 即可, 因為我們只要他的 `.git`, 之後會對它做一些簡單的修改(如: 新增檔案, 修改檔案等)和下一些常用的 git 指令, 但每下一次指令, 都要把 `.git` 更新到另一個 repo, 也就是這個 `HowGitWorks` repo, 把 `觀察對象` 的 `.git` commit 上來(會把 `.git` 改名叫 `dotgit`, 因為裡面已經有 `.git` 了嘛), 這個 repo 的用途就是用來看 `觀察對象` 之`.git` 資料夾內變化
- 接下來, 我們會一邊對`觀察對象`做下列行為, 一邊觀察 `dotgit` 的變化:
  - git init
  - add README.md and git add .
  - git commit
  - modify README.md and git add .
  - git commit
  - create a branch
  - modify README.md on branch and git add .
  - git commit

## 享受吧
### git init
- 首先是 `git init`, 一開始的 `.git` 內, 加入了 15 個檔案(我們只專注在`檔案`, 不要管`資料夾`, 尤其是不要管`空資料夾`, 不然太多麻煩事)
- 專注在打勾的檔案, 非打勾的是設定檔(config, description 和 exclude)與 hook 的範例(簡單來說, 能透過 hook 讓 git 幫你在執行指令後, 做你想做的事情), 都先不管這些
  - [x] dotgit/HEAD
  - [ ] dotgit/config
  - [ ] dotgit/description
  - [ ] dotgit/hooks/applypatch-msg.sample
  - [ ] dotgit/hooks/commit-msg.sample
  - [ ] dotgit/hooks/fsmonitor-watchman.sample
  - [ ] dotgit/hooks/post-update.sample
  - [ ] dotgit/hooks/pre-applypatch.sample
  - [ ] dotgit/hooks/pre-commit.sample
  - [ ] dotgit/hooks/pre-push.sample
  - [ ] dotgit/hooks/pre-rebase.sample
  - [ ] dotgit/hooks/pre-receive.sample
  - [ ] dotgit/hooks/prepare-commit-msg.sample
  - [ ] dotgit/hooks/update.sample
  - [ ] dotgit/info/exclude 
- 篩選後:
  - [dotgit/HEAD](https://github.com/ChaoLiou/HowGitWorks/commit/20395a54e04892c69bff5828181935b7a4ba0d92?diff=unified#diff-a812495751449f2e9862a7d9bcdf2020) 它是代表目前的 repo 是指向 master(光看內容推斷)
```
ref: refs/heads/master
```

### add README.md and git add .
- 新增一個 `README.md` 檔案, 裡面的內容是一行 `add README.md`, 然後下 `git add .`(我是懶人直接就用 `.`, 代表全部修改過的檔案, 按照規矩是下 `git add README.md`)
- 此時, 產出 2 個檔案...到這邊先暫停一下, 出現了未來旅程中, 最重要的一群角色 - `object`(git binary file), 總共有 `blob object`, `tree object` 和 `commit object` 三種, 但先不要苦惱它們的差別, 這篇文章看完就會懂了, 以下先簡單介紹
  - `object` 是以 sha1 產出的 hash code 命名(前兩碼會當作上一層資料夾名稱, 之後就是當作 binary 檔案)
  - 要使用 git 的指令來觀察這些 `object` 檔案
    - `git cat-file -t abcd`: 會輸出 ab/cd#### 的 類型
    - `git cat-file -p abcd`: 會輸出 ab/cd#### 的 內容
- 剛剛說到..., 產出兩個檔案
  - [x] dotgit/index
  - [x] dotgit/objects/d0/5730973bc53f1e9a18c91e309cae59243dcd48
- 篩選後:
  - [dotgit/index](https://github.com/ChaoLiou/HowGitWorks/commit/388683c305f1fb8396b095001ec8d894561f7959#diff-78efb0626c470ad821ed98671ac9a5b3) 也是 binary file, 主要的目的是會紀錄每個檔案, 在 working directory(也就是你正在改的資料夾底下), stage(準備要commit前的階段) 和 repo(已經存入 `.git` 架構) 的 hash code, hash code 的特性就是相同內容文件產出的 hash code 一定會相同, 所以每個檔案就可以靠它的三個 hash code 來判斷檔案是在哪個階段, 而 `git status` 其實就是去讀取 `index` 顯示出哪些檔案修改過和哪些在 stage 階段, 不過我們還是專注在 `object` 上吧, 其他更深入資訊可以讀 git index 文章, 我也只是片面了解而已
  - [dotgit/objects/d0/5730973bc53f1e9a18c91e309cae59243dcd48](https://github.com/ChaoLiou/HowGitWorks/commit/388683c305f1fb8396b095001ec8d894561f7959#diff-1a79af057bfe77d7d28fe13611951c34) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t d057` 輸出 `blob`, 原來是 `blob object`
    - `git cat-file -p d057` 輸出
  ```
  add README.md
  ```
- 結論: 
    1. `git add README.md` 會產出 `blob object`, 而它的內容是 `add README.md` 其實就是當初我 `git add .` 時, README.md 的內容
    2. 再腦力激盪一下會發現, 它都沒有紀錄 README.md 這個檔名, 之後要怎麼知道這個 `blob` 的內容是 README.md 的內容, 我再簡化一下, 就是`沒有檔名`與`檔案內容`的`關聯`
    3. 所以, 可以合理懷疑, 檔名與檔案內容的關聯就會紀錄在 `dotgit/index` 檔案中
    
### git commit
- 下 `git commit -m "add README.md"`
- 產出 7 個檔案, 與上一次一樣, 我們只關注打勾的檔案即可, 非打勾的檔案中, COMMIT_EDITMSG 是 commit message 的暫存檔案, 就是呢如果你沒使用 `-m MESSAGE` 的方式 commit, git 就會打開這個檔案, 讓你輸入 message, 所以它就只是一個 暫存檔案, 而 logs 就不多說了, 不過要注意的是當我們下 `git log` 其實就是讀取這些 logs
  - [ ] dotgit/COMMIT_EDITMSG
  - [ ] dotgit/logs/HEAD
  - [ ] dotgit/logs/refs/heads/master
  - [x] dotgit/index
  - [x] dotgit/objects/1d/04ab30f8f00ab6d8d39df76713f692d4861013
  - [x] dotgit/objects/cd/232ec441e272951b5fc8e6e834b9413a9bdefe
  - [x] dotgit/refs/heads/master
- 篩選: 
  - [dotgit/index](https://github.com/ChaoLiou/HowGitWorks/commit/efdb531e95b89c477551f7254dcdb6c1db8996d8#diff-78efb0626c470ad821ed98671ac9a5b3) 上一次已經簡略介紹, 不再論述. 了解它存在的使命, 你就會發現, 基本上每一個指令都會讓它改變
  - [dotgit/objects/1d/04ab30f8f00ab6d8d39df76713f692d4861013](https://github.com/ChaoLiou/HowGitWorks/commit/efdb531e95b89c477551f7254dcdb6c1db8996d8#diff-7bcad682506dc714839558dba5029db2) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t 1d04` 輸出 `tree`
    - `git cat-file -p 1d04` 輸出
  ```
  100644 blob d05730973bc53f1e9a18c91e309cae59243dcd48 README.md
  ```
- - - 會產出一個 `tree object`, 它的內容看起來是 README.md 這份檔案 與 d057 這個 `blob object` 的關聯(100644 是檔案權限, 先擱著)
- - - 這個資訊就是我們在 `add README.md and git add .` 時的結論探討的, 注意到這一次指令只有 `git commit`(-m MESSAGE), 完全沒有甚麼檔案與檔案內容關聯的資訊, 所以, 很顯然產生這個 `tree object`, 是透過 `dotgit/index` 裡的資訊來的, 這樣又發現了 `dotgit/index` 存在的另一個目的, 就是要生出 `tree object`
  - [dotgit/objects/cd/232ec441e272951b5fc8e6e834b9413a9bdefe](https://github.com/ChaoLiou/HowGitWorks/commit/efdb531e95b89c477551f7254dcdb6c1db8996d8#diff-f420814c6cc4f95ab7c212484c65f610) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t cd23` 輸出 `commit`
    - `git cat-file -p cd23` 輸出
  ```
  tree 1d04ab30f8f00ab6d8d39df76713f692d4861013
  author ChaoLiou <ucnsaythtagn@gmail.com> 1527831831 +0800
  committer ChaoLiou <ucnsaythtagn@gmail.com> 1527831831 +0800
  
  add README.md
  ```
- - - 會產出一個 `commit object`, 它的內容第一行看起來是 這個 `commit object` 與 1d04 這個 `tree object` 的關聯, 後面接著 author, committer 和 commit message 的資訊不太重要
  - [dotgit/refs/heads/master](https://github.com/ChaoLiou/HowGitWorks/commit/efdb531e95b89c477551f7254dcdb6c1db8996d8#diff-c5cb6fc4892afc15a5fe9864cc122b94)
```
cd232ec441e272951b5fc8e6e834b9413a9bdefe
```
- - - 我們解讀成: 目前的 repo 是指向 `master`(從檔名看出來的), 而 `master` 目前是指向 cd23 這個 `commit object` 上
- 結論: 
  - 終於將三個 `object` 都串起來了, 而且還解釋了包含 repo 版本也指向 `master`, `master` 指向 `commit object`, 這樣就完成第一次 commit 了
  - 再串一次給你看, `blob` <- `tree` <- `commit`, 當我要還原到這個版本, 就可以從這個 `commit` 一路往下找到, 找到 `應該要有甚麼檔案`(`treeobject`) 和 `檔案裡是甚麼內容`(`blob object`), 這就是 git 的 snapshot 機制

### modify README.md and git add .
- 修改 README.md 再 `git add .`, 基本上與第二次的 `add README.md and git add .` 非常類似, 因為對 git 來說, 每一次的改變, 其實是記錄當時的 snapshot, 所以不管`新增`或`修改`, 皆是`新增`
  - [ ] dotgit/index
  - [x] dotgit/objects/34/6472775c16595419d093c716dbc1d7a85468f7
- 篩選後:
  - [dotgit/objects/34/6472775c16595419d093c716dbc1d7a85468f7](https://github.com/ChaoLiou/HowGitWorks/commit/525853b64d461cb071b925e4edaceb1448ab87f4#diff-a1f80d7d36ecfdaf90ec997b1e468aea) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t 3464` 輸出 `blob`
    - `git cat-file -p 3464` 輸出
  ```
  modify README.md
  ```
- 結論: 證實了 `新增或修改, 皆是新增`, 就這樣而已

### git commit
- `git commit -m "modify README.md"`
  - [ ] dotgit/COMMIT_EDITMSG
  - [ ] dotgit/index
  - [ ] dotgit/logs/HEAD
  - [ ] dotgit/logs/refs/heads/master
  - [x] dotgit/objects/23/fc51430fc1e98b69f49fe5a090bfbab1e346ca
  - [x] dotgit/objects/d0/65898c72be17d5131ac48f90acd5d308744725
  - [x] dotgit/refs/heads/master

- 篩選後:
  - [dotgit/objects/23/fc51430fc1e98b69f49fe5a090bfbab1e346ca](https://github.com/ChaoLiou/HowGitWorks/commit/7d0d72ca79944ecb8fb3951d6a44b13fc1e675b2#diff-5acdeec3dfd35d9fa2f0a0365ca15fc6) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t 23fc` 輸出 `tree`
    - `git cat-file -p 23fc` 輸出
  ```
  100644 blob 346472775c16595419d093c716dbc1d7a85468f7 README.md
  ```
  - [dotgit/objects/d0/65898c72be17d5131ac48f90acd5d308744725](https://github.com/ChaoLiou/HowGitWorks/commit/7d0d72ca79944ecb8fb3951d6a44b13fc1e675b2#diff-ba96872db56fe490226ff61bdff883c3) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t cd23` 輸出 `commit`
    - `git cat-file -p cd23` 輸出
  ```
  tree 23fc51430fc1e98b69f49fe5a090bfbab1e346ca
  parent cd232ec441e272951b5fc8e6e834b9413a9bdefe
  author ChaoLiou <ucnsaythtagn@gmail.com> 1527832179 +0800
  committer ChaoLiou <ucnsaythtagn@gmail.com> 1527832179 +0800
  
  modify README.md
  ```
  - [dotgit/refs/heads/master](https://github.com/ChaoLiou/HowGitWorks/commit/7d0d72ca79944ecb8fb3951d6a44b13fc1e675b2#diff-c5cb6fc4892afc15a5fe9864cc122b94)
```
d065898c72be17d5131ac48f90acd5d308744725
```
- 結論:
  - 與之前的 `git commit` 結果有點不同, 在 `commit object` 中多出了一行 parent, 而後面接的是第一次 `git commit` 產出的 `commit object`, 代表前一個 `commit object`, 透過一個 `commit object` 串一個 `commit object` 就能將完整歷史串起來
  - (commit1:[-> tree1 -> blob1]) <- (commit2:[-> tree2 -> blob2]) <- (commit3:[-> tree3 -> blob3])

### create a branch
- `git branch checkout -b branch1` 再修改 README.md, 加上一行 `modification on branch`
  - [X] dotgit/HEAD
  - [ ] dotgit/logs/HEAD
  - [ ] dotgit/logs/refs/heads/branch1
  - [X] dotgit/refs/heads/branch1
- 篩選後:
  - [dotgit/HEAD](https://github.com/ChaoLiou/HowGitWorks/commit/e0162ae53384e154341bb5c46c6d1a4ba10082c3#diff-a812495751449f2e9862a7d9bcdf2020)
```
refs/heads/branch1
```
- - - 將 `master` 改為 `branch1`, 代表目前 repo 是指向 `branch1`
  - [dotgit/refs/heads/branch1](https://github.com/ChaoLiou/HowGitWorks/commit/e0162ae53384e154341bb5c46c6d1a4ba10082c3#diff-9eb7f16dd007ca0277bd1a7fd195a3e9)
```
d065898c72be17d5131ac48f90acd5d308744725
```
- - - 與 `dotgit/refs/heads/master` 的內容相同很合理, 因為 branch1 是以 master 當作基底分支出來, 而 master 最後是指向 `d065` 這個 `commit object`

### modify README.md on branch and git add .
- 修改 README.md 再 `git add .`
  - [ ] dotgit/index
  - [x] dotgit/objects/93/51f9f390b0671c7fe35a79aa7279b9753389a5
- 篩選後:
  - [dotgit/objects/93/51f9f390b0671c7fe35a79aa7279b9753389a5](https://github.com/ChaoLiou/HowGitWorks/commit/976ffed3293e22b627e47c38ae3265436ad6ac46#diff-c513515c6d0e4246e2ae179b8efa7f2a) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t 9351` 輸出 `blob`
    - `git cat-file -p 9351` 輸出
```
  add README.md
  first modification
  modification on branch
```

### git commit
- `git commit -m "modification on branch"`
  - [ ] dotgit/COMMIT_EDITMSG
  - [ ] dotgit/index
  - [ ] dotgit/logs/HEAD
  - [ ] dotgit/logs/refs/heads/branch1
  - [x] dotgit/objects/61/5d769144fbf51077f8879ff4fc0043b1c45251
  - [x] dotgit/objects/ba/4bcb03215c9ea8a25fa70a90b344a6e9ce1f37
  - [x] dotgit/refs/heads/branch1
- 篩選後:
  - [dotgit/objects/ba/4bcb03215c9ea8a25fa70a90b344a6e9ce1f37](https://github.com/ChaoLiou/HowGitWorks/commit/9a669dfd15daa55fe7e080f6c5b5634fd459d351#diff-4b1b30944f69764c60c2bd3e5100409f) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t ba4b` 輸出 `tree`
    - `git cat-file -p ba4b` 輸出
  ```
  100644 blob 9351f9f390b0671c7fe35a79aa7279b9753389a5 README.md
  ```
  - [dotgit/objects/61/5d769144fbf51077f8879ff4fc0043b1c45251](https://github.com/ChaoLiou/HowGitWorks/commit/9a669dfd15daa55fe7e080f6c5b5634fd459d351#diff-f333f370c66c47fb8880c7733feb52f3) 是 `object`, 若透過 git 指令觀察:
    - `git cat-file -t cd23` 輸出 `commit`
    - `git cat-file -p cd23` 輸出
  ```
  tree ba4bcb03215c9ea8a25fa70a90b344a6e9ce1f37
  parent d065898c72be17d5131ac48f90acd5d308744725
  author ChaoLiou <ucnsaythtagn@gmail.com> 1527832179 +0800
  committer ChaoLiou <ucnsaythtagn@gmail.com> 1527832179 +0800
  
  modify README.md
  ```
  - [dotgit/refs/heads/branch1](https://github.com/ChaoLiou/HowGitWorks/commit/9a669dfd15daa55fe7e080f6c5b5634fd459d351#diff-9eb7f16dd007ca0277bd1a7fd195a3e9)
```
615d769144fbf51077f8879ff4fc0043b1c45251
```

# END