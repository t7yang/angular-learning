## Setup
導讀：[2.Setup](https://angular.io/docs/ts/latest/guide/setup.html)


### 快速開始新專案
Angular專案的結構龐大，需要組態相當多的東西，若再考量到打包（bundle）的問題，就更加複雜。
所以建議直接以`@angular/cli`專案自動管理系統來協助我們開發Angular專案。

### CLI快速啟動
確保你的環境有node.js和npm。開啟終端機（terminal），輸入下列指令，你會看到類似的結果：
```
$ node -v
$ v6.10.0
$ npm -v
$ 4.4.4
```

安裝@angular/cli非常簡單，利用下列指令，進行全域安裝：
```
$ npm install -g @angular/cli
```

建立和啟動伺服專案如下。建立的過程包含npm安裝，需要幾分鐘。
```
$ ng new my-app
$ ...
$ cd my-app
$ ng serve --open
```
> ``` --open ```是自動開啟瀏覽器選項，可以不輸入，專案成案被伺服後，你可以從終端機看到伺服的網址。
> 通常是 http://localhost:4200/。你也可以用``` ng serve --port=5000 ```來啟動不同埠號伺服環境。

> ``` ng --help ```讓你可以快速瀏覽@angular/cli指令提供了那些選項、標旗（flag）、參數。


### ``` src ```資料夾
你主要的開發內容會放在``` src ```資料夾內，透過``` ng generate xxx ```你可以快速在``` src ```資料夾中建立諸如Component、service、Module、Pipe等Angular項目。


### 你還需要知道的事
- 你知道yarn嗎？它被譽為效率更好的npm，透過設定@angular/cli的全域設定，你就可以讓新建立的專案使用yarn當作package管理器。
  ```
  $ ng set --global packageManager=yarn
- 更多有關@angular/cli產生的檔案結構，請參考導讀的連結。
- 因為以TypeScript開發的關係，所以建議使用Visual Studio Code作為開發編輯器，同時安裝好一下套件：
  - Angular Language Service
    - 在模板提供智慧提示（Intellisense）
  - AngularDoc for Visual Studio Code
    - 在側邊欄自動執行ng xxx 指令，`ng generate Component`
