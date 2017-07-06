# 反應式表單
導讀：https://angular.io/guide/reactive-forms

反應式表單相對於模板驅動表單，是另一個完全迥異的開發表單的模式，其更強調即時地反應、對模型的要求也更高（因此筆者也稱其為「模型驅動表單」）。下文的導讀會儘量以對照模板驅動表單的方式，逐一以核心功能進行（雙向繫結、狀態追蹤與驗證、錯誤訊息與停用）說明。


## 反應式表單
- 需要匯入`ReactiveFormsModule`。
- 讓開發可以在資料模型與介面導向表單模型之間更輕鬆的管理流向。
- 更容易測試、驗證。
- 資料模型可以轉換到反應式表單的基本單位，並且 **即 時** 觀察狀態和異動。
- 資料模型與表單模型徹底分開，可以輕易保留原本資料。
- 反應式表單是同步的，模板驅動表單是非同步，也因此在測試時需要跳過一個 tick 後才能確實拿到模板的資料。


看完上述的整理後，相信孰優孰劣已經有答案。但 Angular 的結論如是告訴我們兩者的設計哲學不同、架構不同，各有優缺利弊並沒有誰比誰更強的說法。但這種官腔的說法顯然滿足不了各位。因此，從刪去法著手分析：
- 如果重視測試與驗證：反應式
- 資料模型越複雜：反應式
- 越需要動態新增控制項：反應式
- 程式碼寫得少又好維護：反應式
- ……
- 啊你直接告訴我都用反應式表單不就好了！
- 資料模型越簡單、越不須在意驗證的，用模板驅動反而比較好處理啦。


為了能夠讓讀者比較容易理解兩種表單的開發差異，範例將以模板驅動表單的資料為主並加以部分擴充來涵蓋控制陣列的介紹。

## 模型
資料模型大致與模板驅動表單的模型相仿，唯獨新增了一個`public features: string[]`作為控制陣列的演示用。

```js
export class Hero {

  constructor(
    public id: number,
    public name: string,
    public power: string,
    public alterEgo?: string,
    public features: string[]
  ) {  }

}
```


## 元件與模板
有別於模板驅動表單，本文的元件和模板會以拆解的方式來講解。

### 匯入`ReactiveFormsModule`
```js
...
import { ReactiveFormsModule } from '@angular/forms';
...

@NgModule({
  imports: [
    ...
    ReactiveFormsModule
    ...
  ],
  ...
})
export class AppModule { }
```


### 建立表單模型和它的好朋友`FormBuilder`
表單的模型其實就是資料模型用基本單位轉換為表單模型，接著把產生的表單模型存在一個變數內。

```js
import { Component } from '@angular/core';
import { FormGroup, FormControl, FormArray  } from '@angular/forms'

@Component({
  selector: 'hero-form',
  templateUrl: './hero-form.component.html'
})
export class HeroFormComponent {
  // 通常會把資料模型用 FormGroup 當作容器包起來
  form = new FormGroup({
    id: new FormControl(null), // 針對每一個子項目建立對應的基本單位
    name: new FormControl(null),
    power: new FormControl(null),
    alterEgo: new FormControl(null),
    features: new FormArray([])
  });
}
```

看到這裡或許有些人已經有點卻步了。反應式表單要求開發者一開始就要根據資料結構建立一個對照的表單結構，而且所有項目都必須轉換成 Angular 表單的基本單位，寫法也相當地繁瑣和冗長。但是別擔心， Angular 都幫我們想好了。看看下例，是否覺得一切突然都變得簡單多了。

```js
import { Component } from '@angular/core';
import { FormGroup, FormControl, FormArray, FormBuilder  } from '@angular/forms'

@Component({
  selector: 'hero-form',
  templateUrl: './hero-form.component.html'
})
export class HeroFormComponent implements OnInit {

  // 別忘了， FormGroup 也是可以用來當作型別的。
  form: FormGroup;

  // FormBuilder 是一個可注入項目，所以要透過注入的寫法
  constructor(private fb: FormBuilder) {}

  // 在元件初始化時才賦值給 this.form
  ngOnInit() {
    // fb 的寫法和直接使用 class 產生實體的差異不大，但程式碼更簡潔
    this.form = this.fb.group({
      id: null,
      name: null,
      power: null,
      alterEgo: null,
      features: this.fb.array([]) // 所有的基本單位都可以用 fb 來產生
    });
  }

  addFeature(newFeature: string) {
    (this.form.get('features') as FormArray)
      .push(new FormControl(newFeature));
  }

  get features() {
    return this.form.get('features') as FormArray;
  }
}
```


### 模板上的雙向繫結
Angular 針對每一種基本單位提供了一個用於雙向繫結的 Directive ，來看看：

```html
<!-- 不依賴 ngForm 而是用 [formGroup] 作為屬性繫結 -->
<form (ngSubmit)="onSubmit()" [formGroup]="form" novalidate>
  <div class="form-group">
    <label for="name">Name</label>
    <!-- 雙向繫結直接透過 Directive ，這裡的屬性值是建立 FormGroup 時物件的 key -->
    <input type="text" class="form-control" id="name" formControlName="name">
  </div>

  <div class="form-group">
    <label for="alterEgo">Alter Ego</label>
    <input type="text" class="form-control" id="alterEgo" formControlName="alterEgo">
  </div>

  <div class="form-group">
    <label for="power">Hero Power</label>
    <select class="form-control" formControlName="power">
      <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
    </select>
  </div>

  <!-- 實作一個可以新增 features 的控制項 -->
  <div class="input-group">
    <!-- 透過模板變數取得控制項 -->
    <input type="text" class="form-control" placeholder="add new feature" #newFeature>
    <span class="input-group-btn">
      <!-- 發生事件時把控制項的值傳遞給回呼函數並清空控制項的值 -->
      <button class="btn btn-default" type="button"
              (click)="addFeature(newFeature.value); newFeature.value = ''">
              Add
      </button>
    </span>
  </div>

  <div formArrayName="features">
    <ul>
      <!-- 注意：這裡 features 是從元件取得的 -->
      <!-- FormArray.controls 會回傳表單基本單位，這裡取得的是 FormControl -->
      <!-- 因為對照 addFeature() ， push 進去的就是 FormControl -->
      <li *ngFor="let feature of features.controls">
        <!-- 所有的基本單位都有 .value 這個成員用來取得原始型別的資料 -->
        {{ feature.value }}
      </li>
    </ul>
  </div>
</form>
```

> #### novalidate
> 反應式表單的已經有自己的一套開發表單的完整生態系統，
> 不需要額外借助原生 HTML 的驗證機制。
> 因此，通常建議幫 HTML 表單加上 `novalidate` 停用原生的驗證機制


### 狀態追蹤與驗證、錯誤訊息與停用
接下來逐步擴充表單模型，幫它加上驗證的條件，這樣才能讓控制項有狀態的變化。反應式表單的驗證必須靠 **Validators** ，它的功能非常強大，使用也很方便。

```js
import { Component } from '@angular/core';
// 新增了 Validators
import { FormGroup, FormControl, FormArray, FormBuilder, Validators  } from '@angular/forms'

@Component({
  selector: 'hero-form',
  templateUrl: './hero-form.component.html'
})
export class HeroFormComponent {
  form: FormGroup;

  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      id: null,
      // 只針對需要的部分新增驗證條件，並轉換成陣列的型式
      name: [null, Validators.required],
      power: [null, Validators.required],
      alterEgo: null,
      // 當有多個驗證條件時須使用 Validators.compose([])
      features: this.fb.array([], Validators.compose([Validators.required, Validators.minLength(1)]))
    })
  }

  addFeature() {
    (this.form.get('features') as FormArray)
      .push(new FormControl(null, Validators.required));
  }

  get features() {
    return this.form.get('features') as FormArray;
  }
}
```


```html
<form (ngSubmit)="onSubmit()" [formGroup]="form" novalidate>
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name" formControlName="name">
    <!-- 一樣不需要透過模板變數就可以取得基本單位的狀態 -->
    <!-- 顧名思義 .get() 方法就是根據參數取得 FormGroup 底下的成員，回傳的也是基本單位 -->
    <!-- 用狀態來決定是否顯示錯誤訊息，所有東西都是即時且自動的 ， Angular 在背後掌控一切 -->
    <div [hidden]="form.get('name').valid || form.get('name').pristine"
         class="alert alert-danger">
      Name is required
    </div>
  </div>

  <div class="form-group">
    <label for="alterEgo">Alter Ego</label>
    <input type="text" class="form-control" id="alterEgo" formControlName="alterEgo">
  </div>

  <div class="form-group">
    <label for="power">Hero Power</label>
    <select class="form-control" formControlName="power">
      <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
    </select>
    <div [hidden]="form.get('power').valid || form.get('power').pristine" class="alert alert-danger">
      Power is required
    </div>
  </div>

  <ng-container formArrayName="features">
    <div class="form-group" *ngFor="let feature of features; index as i">
      <label for="{{i}}">{{ 'feature ' + i }}</label>
      <input type="text" class="form-control" id="{{i}}" formControlName="i">
      <div [hidden]="form.get('name').valid || form.get('name').pristine"
           class="alert alert-danger">
        feature is required
      </div>
    </div>
  </ng-container>
</form>
```


### 整理
- 要使用反應式表單開發，必須匯入`ReactiveFormsModule`。
- Angular 的表單基本單位：
  - FormControl：最小單元
  - FormGroup：物件型容，可裝載任何基本單位
  - FormArray：陣列型容器，可裝載任何基本單位
- 通常會直接把最外層的 FormGroup 容器透過 [property binding] 給 `<form></form>`：
  ```html
    <form [formGroup]="form"></form>
  ```
- 驗證條件應善加利用 Validators，絕對可以事半功倍。
- 雙向繫結全都依賴反應式表單獨有的 Directive：
  - formControlName
  - formGroupName
  - formArrayName
- 所有的基本單位都繼承自 AbstractControl ，意味著取得基本單位的實體就可以呼叫它的狀態。
