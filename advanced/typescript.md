

## `export interface`
需要注意的是，因為JS本身並沒有`interface`的概念，因此就算TS定義並匯出`interface`，編譯後的檔案是不會產生任何程式碼的。這也導致有時時候會發生編輯匯入匯出`interface`後程式編譯失敗。這時只需要`ctrl + c`停止然後`npm start / yarn start`重新啟動編譯即可。
