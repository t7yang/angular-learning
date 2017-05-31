# 開發Angular專案的建議流程
來源：[如何在 Angular CLI 建立的 Angular 2 開發環境呼叫遠端 RESTful APIs](http://blog.miniasp.com/post/2017/02/05/Setup-proxy-to-backend-in-Angular-CLI.aspx)

1. 用@angular/cli建立一個新的專案，啟動它。
   ```
   $ ng new project-name
   $ cd project-name
   $ npm start 或是 ng serve
   ```
2. 設定Proxy Config，如果有必要向遠端伺服器或本機建立的`json-server`請求資源。
  - 在專案目錄下建立一個`proxy.config.json`檔案
    - `touch proxy.config.json`
  - 設定對應的請求資源url轉換，下例讓所有以`/api`開頭的請求都自動轉換成向`http://140.xxx.xxx.xxx/api`請求。
    ```json
    {
      "/api": {
        "target": "http://140.xxx.xxx.xxx",
        "secure": false
      }
    }
    ```
  - 修改`package.json`裡面的`start`指令，讓程式以proxy的方式啟動更方便。
    ```json
    "scripts": {
      "start": "ng serve --proxy-config proxy.config.json"
    }
    ```
    > **注意：**<br>雖然已使用代理的方式啟動，但你在瀏覽器console看到的訊息仍是向`http://localhost`請求，這並不是設定錯誤。

3. 設定虛擬的RESTful API伺服器。如果在本機端開發時就希望可以完全模擬好介接的遠端API伺服器。以下以`json-server`為例。
  - 全域安裝`json-server`。
    ```
    $ npm install -g json-server
    ```
  - 在專案目錄內或其他任意的地方設定好資料庫。
    - `touch db.json`
      ```json
      {
        "posts": [
          { "id": 1, "title": "javascript" },
          { "id": 1, "title": "typescript" },
          { "id": 1, "title": "actionscript" }
        ],
        "profile": { "name": "typicode" }
      }
      ```
  - 啟動`json-server`，預設埠號是3000，可以透過`--port`或`-p`自訂，避免衝突。還可以透過`--watch`即時觀察`db.json`有手動編輯檔案後，重新啟動伺服器。
    ```
    $ json-server ./db.json --port 3200 --watch
    ```
  - 自訂Routes。一般伺服器都以`/api`作為API的起始位置，若要最大程度的擬真，可以透過`json-server`的自訂Routes。
    - 在`db.json`相同目錄，或其他任意位置，建立一個routes.json設定檔。
      - `tounch routes.json`
    - 編輯`routes.json`，讓`/api`自動指向`/`。
      ```json
      {
        "/api": "/"
      }
      ```
    - 啟動`json-server`。
      ```
      $ json-server --watch ./db.json --routes ./routes.json --port 3200
      ```
    - 嘗試請求`http://localhost:3200/api/posts`，應該就可以拿到正確資料。
