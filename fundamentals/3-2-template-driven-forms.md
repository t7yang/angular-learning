# 模板驅動表單
導讀：https://angular.io/guide/forms

前面說過，web 2.0  出現讓表單地位變得更為重要，尤其對很多商用網站來說，消費者在訂購商品時勢必得要面對填寫資料的這一步。在 AngularJS 時期，透過雙向繫結和大量內建的表單 Directive 來試圖達到更方便建立表單的目的。同樣的在 Angular 中也保留了下來，且被命名為模板驅動表單（Template Driven Forms）。

開發表單是需要一定程度的設計技巧，在配合架構的協助才能事半功倍，那架構需要提供那些協助呢？以下這些是 Angular 所提供開發表單核心功能。這些核心功能將決定你是否能夠得心應手的開發表單。

- 雙向繫結
- 狀態追蹤與驗證
- 錯誤訊息與停用

上述這樣核心功能也正是「模板驅動表單」與「反應式表單」在開發表單上的差異之處。顧名思義，模板驅動表單也就是在核心功能的運用上會更多放在模板。


## 模板驅動表單
如果你熟悉 AngularJS 那你應該不會對 TDF （Template-Driven Forms）感到陌生。顧名思義， TDF 就是從模板下後，去開發表單，上述所提到的架構所提供的協助也都直接體現在模板中，因此 TDF 會把表單大部分的焦點都放在模板中。相對的，後續會提到另一個種表單的開發模式為反應式（Model-Driven）。然而不論是模型還是模板驅動，表單開發大致都需要下列這些東西：

- 表單的模型。
- 根據模型建立好模板。
- 繫結模型與模板中的表單控制項（Form Control）。
- 當使用者輸入時，驗證控制項的內容是否符合要求。
- 當不符要求時，顯示易懂的錯誤訊息。
- 當表單不符合要求，應鎖定「送出」按鈕。
- 設定好送出事件的回呼函數。

> #### 表單模組
> 不論是模板驅動還是反應式的表單，都必須正確的匯入相對的模組。
> 所有有關表單的 Angular 模組都被歸納到 `@angular/forms` 這個 js 模組內。
> 要順利使用模板驅動表單，必須先匯入：
> ```js
> import { FormsModule }   from '@angular/forms';
> @NgModule({
> imports: [ FormsModule ]
> ```


## 模型
表單所要填寫的內容通常是固定的，每一個表單都有自己的資料模型，下例就是一個針對超級英雄的資料模型：

```js
export class Hero {

  constructor(
    public id: number,
    public name: string,
    public power: string,
    public alterEgo?: string
  ) {  }

}
```

透過 class 定義好模型，就可以清楚的知道模型的資料內容有哪些。


## 元件
有了模型之後，就可以在元件中針對模型做操作：

```js
import { Component } from '@angular/core';
import { Hero } from './hero';

@Component({
  selector: 'hero-form',
  templateUrl: './hero-form.component.html'
})
export class HeroFormComponent {

  powers = ['Really Smart', 'Super Flexible',
            'Super Hot', 'Weather Changer'];

  model = new Hero(18, 'Dr IQ', this.powers[0], 'Chuck Overstreet');

  submitted = false;

  onSubmit() { this.submitted = true; }

  newHero() {
    this.model = new Hero(42, '', '');
  }

  // TODO: Remove this when we're done
  get diagnostic() { return JSON.stringify(this.model); }
}
```

- powers：為了限制使用者只能從下拉選單中選取預設的選項。
- models：作為新增超級英雄時雙向繫結的目標。
- submitted：作為控制送出事件尚未結束的依據。
- onSubmit：送出事件觸發的回呼函數。
- get diagnostic：是一種常見偵錯方法，藉由直接在介面顯示當下模型的值來檢視表單的開發是否如預期般理想。


## 模板
下例的模板，有許多有關模板驅動表單的重要觀念，待一一解釋。

```html
<form (ngSubmit)="onSubmit()" #heroForm="ngForm">
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name"
           required
           [(ngModel)]="model.name" name="name"
           #name="ngModel">
    <div [hidden]="name.valid || name.pristine"
         class="alert alert-danger">
      Name is required
    </div>
  </div>

  <div class="form-group">
    <label for="alterEgo">Alter Ego</label>
    <input type="text" class="form-control" id="alterEgo"
           [(ngModel)]="model.alterEgo" name="alterEgo">
  </div>

  <div class="form-group">
    <label for="power">Hero Power</label>
    <select class="form-control" id="power"
            required
            [(ngModel)]="model.power" name="power"
            #power="ngModel">
      <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
    </select>
    <div [hidden]="power.valid || power.pristine" class="alert alert-danger">
      Power is required
    </div>
  </div>

  <button type="submit" class="btn btn-success" [disabled]="!heroForm.form.valid">Submit</button>
  <button type="button" class="btn btn-default" (click)="newHero(); heroForm.reset()">New Hero</button>
</form>
```

### `ngForm` & `formControl`
當你在 Angular 模組中匯入 FormsModule 時，其下元件的模板一旦出現`<form></form>`， Angular 就會很雞婆加上一個隱藏的 Directive —— `ngForm`。不只如此，該表單的控制項也會一併接管（譬如 input select）。

```html
<form #heroForm="ngForm">
```

透過上述模板變數作法，就可以指向這個`ngForm`。接下來會透過一開始說的核心功能來一一剖析。

### 雙向繫結
雙向繫結指的是元件（ts 檔）所建立的模型實體與模板的表單控制項之間的連動，既任一方有異動時，另一方也會**立 即 跟 著 異 動**。

在元件中，`this.models`已經建立好一個新的表單模型實體。接下來要做的就是把模型繫結到模板中。下列可以看到，透過 Angular 提供的雙向繫結`[(ngModel)]`語法，可以很輕易做到模型與模板之間的繫結。

> #### `[(ngModel)]`
> 雙向繫結的寫法乍看相當特殊，但也正如其字面所代表的，
> 正是 `[property binding]` + `(event binding)` 所結合的一種便利型式（語法糖）。
> 使用者可以做到不須關心底層如何實現，只要會用就好。
> 但要特別注意的是，控制項一定要加上`name="something"`屬性。
> 因為在模板驅動表單的情境下， Angular 會依據`name`屬性的值來接管控制項。

### 狀態追蹤與驗證、錯誤訊息與停用
控制項每分每秒都有可能隨著使用者的輸入產生異動，其狀態也自然不斷地在改變。與其等使用者千辛萬苦填完整份表單後才一次過的顯示所有錯誤訊息，針對每一個控制項即時地告訴使用者狀態是否符合要求更能有好的使用經驗。

上面說過，在模板驅動表單的情況下，Angular 接管控制項的方式是透過`name`屬性。因此，決定錯誤訊息是否要顯示也必須依賴`name`屬性的值。

```html
<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
  Name is required
</div>
```

上述的條件表示，在控制項符合要求（`name.valid`），或控制項尚未被碰觸（`name.pristine`）的情況下隱藏錯誤訊息。當然，你也可以用`*ngIf`搭配`name.invalid && name.touched`，換一種形式來顯示。


除此之外，開發者經常會做的一個防呆機制就是避免使用者在表單不符合條件的情況下送出表單。

```html
<button type="submit" class="btn btn-success" [disabled]="!heroForm.form.valid">Submit</button>
```

透過模板變數指派給`ngForm`後，即可以取得模板中的表單實體，同樣透過表單當下的狀態來決定［送出］按鈕是否停用。

> #### `ngForm`
> `ngForm`會很智慧逐一檢查表單內的所有控制項，並即時改變每個狀態布林值。
> 我們只需要會用，剩下的就交給 Angular 的黑魔法。


### 表單送出事件`(ngSubmit)`
最後要提的是表單的送出事件。MDN 文件上有清楚載明`submit`事件，但為何 Angular 還要自訂一個`ngSubmit`事件呢？大致是原生的`submit`事件的機制並非 Angular 所期待的，因此建議開發者們使用`ngSubmit`而非原生的`submit`事件。

```html
<form (ngSubmit)="onSubmit()">
```

```js
onSubmit() { this.submitted = true; }
```

跟一般事件繫結的實作方式如出一轍，當事件被觸發後，就會執行預設好的回呼函數（通常是呼叫服務的方法來透過 API 把表單的資料送到伺服器。


### 整理
- 要使用模板驅動表單開發，必須匯入`FormsModule`。
- Angular 會接管表單和表單內的控制項：
  - 要指派表單實體給模板變數需要透過`ngForm`。
  - 控制項則是透過控制項的`name`屬性。
- 透過雙向繫結來連動元件的變數和控制項是模板驅動表單必不可少的技巧。
- 表單和控制項的實體都自帶很多的狀態，讓開發者用來顯示錯誤訊息及停用某些控制項。
