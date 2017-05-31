# 進階模板變數使用

## `as`暫存模板中的Async變數
[ngIf 跟他的新朋友 else 和 then](http://blog.kevinyang.net/2017/04/19/angular-ngIf-else/)
```html
<div *ngIf="user|async">
  {{ (user|async)?.name }}
</div>

<div *ngIf="user|async as _user">
  {{ _user.name }}
</div>
```

## `*ngIf else then`的使用
```js
@Component({
  selector: 'ng-if-then-else',
  template: `
    <button (click)="show = !show">{{show ? 'hide' : 'show'}}</button>
    <button (click)="switchPrimary()">Switch Primary</button>
    show = {{show}}
    <br>
    <div *ngIf="show; then thenBlock; else elseBlock">this is ignored</div>
    <ng-template #primaryBlock>Primary text to show</ng-template>
    <ng-template #secondaryBlock>Secondary text to show</ng-template>
    <ng-template #elseBlock>Alternate text while primary text is hidden</ng-template>
`
})
class NgIfThenElse implements OnInit {
  thenBlock: TemplateRef<any> = null;
  show: boolean = true;
  @ViewChild('primaryBlock')
  primaryBlock: TemplateRef<any> = null;
  @ViewChild('secondaryBlock')
  secondaryBlock: TemplateRef<any> = null;
  switchPrimary() {
    this.thenBlock = this.thenBlock === this.primaryBlock ? this.secondaryBlock : this.primaryBlock;
  }
  ngOnInit() { this.thenBlock = this.primaryBlock; }
}
```

## `ng-*`三兄弟
- `ng-template`
  - 如同上述的例子，多數的情況下會跟`*ngIf`搭配使用。
- `ng-content`
  - 自訂 Component 時做到 [transclude](https://toddmotto.com/transclusion-in-angular-2-with-ng-content) 的效果。
- `ng-container`
  - 是為了避免使用模板語法時必須放置在一個 HTML 標籤中，進而破壞了 CSS 的設計。
    ```html
      <span *ngIf="condition">value</span>
      <ng-container *ngIf="condition">value</ng-container>
    ```
