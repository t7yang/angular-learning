# 使用者輸入
導讀：https://angular.io/guide/user-input

## 繫結事件
表單所承載的一個任務就是讓使用者不只單向從網站取得訊息，也可以反映自己的想法（Web 2.0）。但是網站無法知道使用者何時想要送出他們的資料。因此我們需要定義好當使用者送出他們的資料時我們該怎麼做（的函數），在把這些函數繫結到使用者送出的事件（HTML 定義好的事件）。

好比，通常使用者會在填寫好資料後，會點選某個按鈕。又或者在下拉選單中選擇某個選項會自動對介面產生變化。這些都屬於 [DOM 事件](https://developer.mozilla.org/en-US/docs/Web/Events)，這些標準的事件很多， Angular 網站並不會一一列出，所以當你需要搜尋相關事件時，別忘了到上述的 MDN 查詢。

下列是一個簡單的事件繫結的範例：
```html
<!-- html -->
<button (click)="onClickMe()">Click me!</button>
```

```js
// Component
@Component({
  selector: 'click-me',
  template: `
    <button (click)="onClickMe()">Click me!</button>
    {{clickMessage}}`
})
export class ClickMeComponent {
  clickMessage = '';

  onClickMe() {
    this.clickMessage = 'You are my hero!';
  }
}
```

我們需要先在 Component 中纖定義好當事件發生時（這裡是 click 事件），需要處理的動作。然在模板中繫結到按鈕的「點選」事件，這樣當點選事件被觸發時，就會自動執行預設好的動作。


## 取得使用者輸入的 `$event` 物件
上面說過，事件是有 HTML 所定義，除了定義事件直接，每當 DOM 事件發生時都會攜帶一個載體（payload），這個載體就是很常看到的`$event`。`$event`所代表的是發生事件的那個 DOM 實體，既一個物件。

下例是一個當`(keyup)`（鍵盤按鈕往上）的事件繫結。

```js
template: `
  <input (keyup)="onKey($event)">
  <p>{{values}}</p>
`

export class KeyUpComponent_v1 {
  values = '';

  onKey(event: KeyboardEvent) {
    this.values += event.target.value + ' | ';
  }
}
```

- 當發生`keyup`事件時，會執行`onKey()`函數，同時函數還會得到一個載體。
- 在`onKey(event: KeyboardEvent)`定義好參數和其型別。
  - 如果你剛剛有點 MDN 的連結，你應該會發現事件還被細分為很多種，拜 Typescript 所賜，這些分門別類的事件都有其相對應的型別，指定正確的型別，就可以發揮智慧提示。
- 執行函數後，`this.values`的值被更新，同時也會反應在模板中的`{{ values }}`。


## 消除令人混淆的`$event`物件傳遞
上述的作法，或許已經相當不錯，但傳遞一個事件的載體總是讓人不易理解，對程設而言，傳遞原始型別更具有意義同時也更容易理解。

第一種作法是，可以直接把`$event.target.value`移動到模板中，這樣函數定義時就可以採取更有意義參數型別。

```js
template: `
  <input (keyup)="onKey($event.target.value)">
  <p>{{values}}</p>
`

export class KeyUpComponent_v1 {
  values = '';

  onKey(input: string) {
    this.values += input + ' | ';
  }
}
```

第二種作法更值得被推薦，利用模板變數進行傳遞，不只在函數中有很好理解的原始型別，就連在模板也可以直接消除`$event.target.value`這個冗長又不直觀的寫法。

```js
template: `
  <input (keyup)="onKey(box.value)" #box>
  <p>{{ box.value }}</p>
  <p>{{ values }}</p>
`

export class KeyUpComponent_v1 {
  values = '';

  onKey(input: string) {
    this.values += input + ' | ';
  }
}
```


## 以`key.enter`和`blur`事件來改善效能
以上述例子來看，每一次鍵盤按鍵往上時就會觸發回呼的函數，對效能的影像實在太大了而且使用者往往在打字時會有思考的中斷點，因此何不讓使用者自己決定準備好後再觸發事件。我們直接透過範例進行說明：

```js
@Component({
  selector: 'little-tour',
  template: `
    <input #newHero
      (keyup.enter)="addHero(newHero.value)"
      (blur)="addHero(newHero.value); newHero.value='' ">

    <button (click)="addHero(newHero.value)">Add</button>

    <ul><li *ngFor="let hero of heroes">{{hero}}</li></ul>
  `
})
export class LittleTourComponent {
  heroes = ['Windstorm', 'Bombasto', 'Magneta', 'Tornado'];
  addHero(newHero: string) {
    if (newHero) {
      this.heroes.push(newHero);
    }
  }
}
```

- 在同樣`input`標籤中，繫結了兩個事件：
  - `keyup.enter`： Angular 自訂事件，當`Enter`鍵往上時。
  - `blur`： DOM 的標準事件，字面的意思是模糊（focus的相對意思），實際的意思是當焦點離開了這個 DOM。

繫結兩個事件是為了確保使用者就算忘了按下`Enter`鍵就跳開這個輸入況時也會自動觸發回呼函數的貼心設計。這樣的技巧在實務上很常使用。


## 整理
- 儘量用模板函數來指向網頁的元素，好處是除了更有語意之外，模板變數可以被字元素和兄弟元素使用。
- 盡量傳遞實際所須的值給回呼函數而不是把整個事件載體傳遞過去。
- 儘量保持模板的陳述式簡單——上述的`blur`事件繫結同時處理了兩件事情，
