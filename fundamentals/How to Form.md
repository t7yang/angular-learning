# Angular 表單和反應式表單（Reactive Form）範例

## 建立一個反應式表單和繫結在模板上
- 建立一個`FormGroup`的實體
  - 透過自行匯入`FormGroup`的`Class`後`new`一個實體。或
  - 透過`form = FormBuilder.group({})`產生一個新的`FormGroup`。
- 繫結到`<form></form>`中。
  - `<form [formGroup]="form"`
- `<form></form>`內部`Control`以`formControlName`，子`FormGroup`以`fromGroupName`繫結：
  - `<input formControlName="name">`
  - `<div formGroupName="address">`


```javascript
export class FormComponent {
  aFrom: FormGroup;

  constructor(private fb: FormBuilder) {
    this.aForm = this.fb.group({
      name: ['', Validators.required],
      address: this.fb.group(new Address()),
      address: this.fb.group({
        zip: ['', Validators.required]
      })
    });
  }
}
```

```html
<form [formGroup]="aFrom">
  <input type="text" formControlName="name">
  <div formGroupName="address">
    <input type="text" formControlName="zip">
  </div>
</form>
```


## 賦值給`FormGroup`項目
每個`FormGroup`的實體都一個兩個有關賦值的方法——`setValue({})`和`patchValue({})`（`FormControl`也有）。可以讓你隨需賦值給表單的項目，譬如注入變數有變化時，即時反應到表單內容，透過`ngOnChange`。

- `setValue()`：會檢查傳入參數是否符合表單的資料結構，否則會提示錯誤，也就是傳入的物件必須完全符合表單的資料結構。
- `patchValue()`：不會檢查表單的資料結構和提示錯誤，但可以隨意只針對某一項進行賦值。

```javascript
export class AComponet {
  @Inject() hero: Hero;
  aForm: FormGroup;

  constructor() {}

  ngOnChange() {
    this.aForm.setValue({
      name: this.hero.name,
      address: this.hero.addresses[0] || new Address()
    })
  }
}
```

## 重設表單
有時候會想提供重設表單的選項給使用者，尤其當表單太過複雜時。而且整個表單重設可以取消所有項目的狀態（state），讓依賴（pristine或touch）的錯誤提示訊息可以回到最初狀態。一樣可以搭配`ngOnChange`時進行重設。

```javascript
// 可以整個表單都重設
this.aForm.reset();

// 或是針對某些項目進行重設
this.aForm.reset({
  name: this.hero.name,
  address: this.hero.addresses[0] || new Address()
})
```


## 動態產生表單內容`FormArray`
