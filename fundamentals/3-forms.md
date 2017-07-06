## 表單／表格

表單是現代網站不可或缺的一個重要環節，尤其在 Web 2.0 之後強調使用者能夠參與，商業網站的交易行為都必須仰賴表單。


## HTML 表單
表單開發的基礎就是 HTML，下例是一個簡單範例：

```html
<form action="action.php" method="post">
  <label for="name">Name</label>
  <input type="text" name="username" id="name" value="default value">
</form>
```

`<form></form>`這個標籤就代表一個表單，在標籤內會寫入控制項，最常使用的控制項就是`<input>`，它又會根據`type`屬性內容的不同產生不同資料類型的輸入框。[這裡](https://www.w3schools.com/html/html_form_elements.asp)可以查閱其他的表單控制項。

## Angular 表單
上述的說明相信已經可以建立對表單的基礎認識，但 Angular 表單是另外一個世界。 Angular 為了讓開發者能夠從容應付「雙向繫結」「驗證與狀態追蹤」「錯誤訊息」等開發者會碰到的問題，因此建立起 Angular 自己的表單架構。

Angular 的表單基礎單元有三個，他們又分別繼承自`AbstractControl`這個抽象的控制項：
- AbstractControl
  - FormControl（控制項）
  - FormGroup（控制組）
  - FormArray（控制陣列）

控制項是裡面最小的單位，跟 HTML 裡面的控制項相仿。控制組和控制陣列則屬於容器型的項目，兩者都可以承裝任何繼承自 AbstractControl 的項目，而且沒有層數的限制。

一般上基於資料模型，最外層通常是一個控制組，內部在根據資料模型不斷組合。下二例分別是一個簡單和複雜的例子。

- FormGroup
  - FormControl
  - FormControl


- FormGroup
  - FormControl
  - FormGroup
    - FormControl
    - FormArray
      - FormControl
    - FormGroup
      - FormControl
  - FormArray
    - FormGroup
      - FormControl


## 模板驅動表單與反應式表單
在接下來導讀中會陸續介紹到這兩者，這裡先說明在這兩種迥異的 Angular 表單設計模式裡面，只有反應式表單中開發者才會跟上述的基礎單元短兵相接。模板驅動表單因為結構較為鬆散、自由，而且很多事情已經由 Angular 背後幫開發者接管了，所以就算沒有建立起基礎單元概念也無妨。但若要上手反應式表單開發，則是必要搞懂這些基本單位關係。
