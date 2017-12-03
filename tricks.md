# Angular 開發小技巧

## @angular/cli

#### `export interface`
需要注意的是，因為JS本身並沒有`interface`的概念，因此就算TS定義並匯出`interface`，編譯後的檔案是不會產生任何程式碼的。這也導致有時時候會發生編輯匯入匯出`interface`後程式編譯失敗。這時只需要`ctrl + c`停止然後`npm start / yarn start`重新啟動編譯即可。

#### `ng serve` 無法用 IP 連線
`angular/cli` 為了某些考量，在預設的情況並不允許透過 IP 的方式連線至 `ng serve` 所啟動的伺服器。在某些情況下，要稍微妥協一下的作法是幫 `ng serve` 加上 `--host` 選項。

```json
{
  "scripts": {
    "dev": "ng serve --host 0.0.0.0"
  }
}
```

**注意**：上述的 `0.0.0.0` 請填入電腦的 IP。

來源：https://github.com/angular/angular-cli/issues/2542


## debugging
- [Chrome Debugging with Angular CLI](https://github.com/Microsoft/vscode-recipes/tree/master/Angular-CLI)


## Component

#### 動態產生 Component
- [Dynamic component loading with Angular2: replace $compile](http://www.smartjava.org/content/dynamic-component-loading-angular2-replace-compile)
- 用一般的 innerHTML 注入 raw HTML，再用事件回呼的方式攔截點選連結事件。


## Route

#### title service
- 如何實作動態網頁標題 - [Dynamic page titles in Angular 2 with router events](https://toddmotto.com/dynamic-page-titles-angular-2-router-events)
