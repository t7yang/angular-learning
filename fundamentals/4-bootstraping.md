# 啟動

導讀：https://angular.io/guide/bootstrapping

Angular 是有一群 Angular 模組所組成的應用程式，最少會有一個根模組，慣例上會命名
為 `AppModule`。根據 CLI 所產生的根模組內容如下：

```ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

模組是經由 `@NgModule` 修飾，裡面的項目包含三個所有模組都可使用的屬性
（declarations, imports, providers; exports 在這裡使用是因為根模組通常不會匯出東
西），還有一個是根模組才會使用的屬性（bootstrap ）。

### `declarations` 陣列

模組就好比一個容器，用於存放和管理內部的零件，Angular 的模組也類似。因此在模組內
的零件就必須在這裡進行「宣告」，讓容器的零件連結起來，互相知道彼此的存在。需要警
告宣告的東西分別是「component 、 directive、pipe 」。

### `providers` 陣列

Angular 跟其他前端架構比較大的不同是， Angular 運用了相依性注入的方式來注入服務
。`providers` 陣列就是為了用來「註冊」服務的。

### `imports` 陣列

就如同 JavaScript 的模組一樣，Angular 的模組也可以透過匯入的方式來匯入其他
Angular 模組。除了你自己寫的模組之外，Angular 的程式庫也是透過模組的方式進行開發
，譬如 `HttpClientModule`。匯入模組後，就可以在這個模組內使用它「註冊」的服務和
匯出的「component 、 directive、pipe 」

### `bootstrap` 陣列

應用程式在「啟動」根模組時會根據根模組內的（`bootstrap` 陣列）所指示的元件插入到
瀏覽器的 DOM 裡面。在 `bootstrap` 陣列內指派多個元件是合法的，但這並不是常見的做
法。

### 在 `main.ts` 中啟動

程式的啟動通常放在另一個檔案中，這是一個有 CLI 產生的 `main.ts`，負責啟動整個應
用程式。

```ts
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule);
```

這段程式碼建立一個瀏覽器平台用於動態編譯並啟動上述的 `AppModule`。啟動的過程包含
建立執行環境、從根模組中找到要被啟動的元件並建立元件的實體再插入到元件所定義的選
擇器名稱的元素標籤。

以 CLI 所產生的檔案為例，`AppModule` 的選擇器名稱定義為 `app-root` ，那程式啟動
時 Angular 就會找到 `index.html` 內的 `<app-root></app-root>`，並做相應的動作。

當你的專案逐漸長大後，根據功能進行劃分，建立新的功能模組並考慮以延遲載入的方式設
定，將大幅改善應用程式的效能。
