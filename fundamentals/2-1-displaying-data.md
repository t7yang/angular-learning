# 顯示資料
導讀：https://angular.io/guide/displaying-data

跟傳統的靜態網頁開發不同，當代的架構（framework）都是以動態的方式進行開發和呈現， Angular 也不例外。所以學會如何把在元件中宣告的變數動態的顯示在模板上就是最基礎的馬步。

## 插補（interpolation）
插補是是模板語法中最基本的用法，用來在模板中插補動態的變數：

```js
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <!-- 在模板中繫結變數 -->
    <h1>{{title}}</h1>
    <h2>My favorite hero is: {{myHero}}</h2>
    `
})
export class AppComponent {
  // 元件內宣告的變數
  title = 'Tour of Heroes';
  myHero = 'Windstorm';
}
```

插補內可填入的運算式（expression），這表示可以填入任何可以被「執行」式子：

```html
<!-- 字串的結合 -->
<h1>{{title + '2017'}}</h1>
<!-- 數字計算合併字串 -->
<h2>My favorite hero is: {{myHero + (1 + 1)}}</h2>
```

> 最簡便的方式，請使用 `@angular/cli` 建立測試用專案
> `ng new angular-test`


## `*ngFor` 顯示一連串的內容
插補的方式非常便利的在模板中顯示動態的內容，但如果要顯示的不僅僅是一個變數，而是一連串的大量資料呢？這時`*ngFor`就可以派上用，它的語法非常類似於 ES6 的 `for ( of )`。

```js
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>{{title}}</h1>
    <h2>My favorite hero is: {{myHero}}</h2>
    <p>Heroes:</p>
    <ul>
      <!-- 透過 *ngFor 取得陣列中單一的項目 -->
      <li *ngFor="let hero of heroes">
        <!-- 在 *ngFor 的範疇內使用插補的方式顯示 -->
        {{ hero }}
      </li>
    </ul>
    `
})
export class AppComponent {
  title = 'Tour of Heroes';
  // 要顯示的大量資料
  heroes = ['Windstorm', 'Bombasto', 'Magneta', 'Tornado'];
  myHero = this.heroes[0];
}
```

> ### 可迭（iterable）
> `*ngFor`能夠處理的目標必須是任何具備可迭的變數。
> 所以一般的物件是無法透過`*ngFor`來顯示的。


## `*ngIf` 選擇性顯示
學會單一變數的插補和大量資料的顯示後，再加上根據條件來選擇性顯示內容，那一套基礎的 Angular 模板組合拳就算學會了。

`*ngIf`能夠接受布林值或最終可得到一個布林值的運算式（會套用 JavaScript 內建的自動轉型別機制），因此在使用上其實相當地靈活。

```html
<!-- 運算式 -->
<p *ngIf="heroes.length > 3">There are many heroes!</p>
<!-- 布林值 -->
<p *ngIf="true">There are many heroes!</p>
<!-- 0 會自動轉型別為 false -->
<p *ngIf="0">There are many heroes!</p>
```


## 整理
- `{{}}` 雙大括號用於單一變數的插補或顯示運算式結果。
- `*ngFor` 顯示大量的資料，變數必須是可迭的。
- `*ngIf` 根據條件選擇性顯示內容。
