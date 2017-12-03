# 整合第三方程式庫
- [Angular 與 JQuery 共舞：整合第三方套件的技巧、陷阱與解決方案](https://www.facebook.com/will.fans/videos/1718120871550383)，[（投影片）](https://www.slideshare.net/WillHuangTW/angular-jquery-3rd-party-js-library-tips-tricks)


### 整合 JQuery 到 angular 專案重點
- 安裝 `jquery @types/jquery`
- 在 typings.d.ts 中匯入 jquery 定義檔案。
  ```ts
  /// <reference path="../node_modules/@types/jquery/index.d.ts" />
  ```
- 進行撰寫程式碼和編譯時，應該就不會出現錯誤。