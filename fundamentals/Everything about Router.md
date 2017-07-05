# 一切有關路由器的資訊（Router）

## 路由守衛（Router Guard）

### 守衛的回傳值
- 如果守衛回傳`true`則導向繼續處理。
- 如果守衛回傳`false`則導向處理會被停止且使用者會留在原處。

> 守衛還可以告訴路由器導向其他位置（譬如若在未登入的情況下，轉導向登入頁面）來有效取消目前的導向處理。


### 守衛應該回傳甚麼
上述提到了，就是`true`和`false`。但多數的情況通常需要等待向伺服器請求必要的資訊才能知道是否該回傳`true`或`false`。所以守衛的回傳結果可以是`Observable<boolean>`或`Promise<boolean>`。路由器會自動等待結果再導向處理應該往前或停止。


### 守衛的類型
1. `CanActivate`用於決定是否可以導向某個路由。
2. `CanActivateChild()`用於決定是否可以導向某個子路由。
3. `CanDeactivate`用於決定是否可以離開目前的所在的路由。
4. `Resolve`用於在路由正式被啟動前執行取得路由所須資料。
5. `CanLoad`用於決定是否可以導向某個以非同步載入的功能模組。

Guard|類型|回傳
--|---|--
CanActivate|Service|Observable / Promise / boolean
CanActivateChild|Service|Observable / Promise / boolean
CanDeactivate||Observable / Promise / boolean
Resolve|   |Observable / Promise / boolean
CanLoad|   |Observable / Promise / boolean

### 守衛的行為模式
`http://localhost:3200/home/admin/dashboard/users`

<<========  從最深的子路開始檢查到最上層路由  ========

- `CanDeactivate()`
- `CanActivateChild()`

========  從最上層路由開始檢查到最深的子路  ========>>

- `CanActivate()`

當有非同步請求載入功能模組時，會檢查`CanLoad()`。如果任何一個守衛回傳了`false`，尚未處理的守衛會全部被取消，整個導向處理就會被取消。
