# 用@angular/cli在本機開發時透過Proxy Config解決CORS的問題
來源：[如何在 Angular CLI 建立的 Angular 2 開發環境呼叫遠端 RESTful APIs](http://blog.miniasp.com/post/2017/02/05/Setup-proxy-to-backend-in-Angular-CLI.aspx)

1. 在專案目錄下建立一個proxy.config.json。
```json
{
  "/api": {
    "target": "http://140.xxx.xxx.xxx",
    "secure": false
  }
}
```
> - 實務上通常會設定為`/api`。
> - `target`的部分則是遠端的API主機位置
>
> 最終我們只需要在服務中以`/api/signin`請求，則Proxy會自動幫我們導向`http://140.xxx.xxx.xxx/api/signin`。

2. 修改`package.json`裡面的`npm start`或自訂一個本機開發用的指令。
```json
{
  "scripts": {
    "start": "ng serve --proxy-config proxy.config.json"
  }
}
```

3. 在服務中設定`basedUrl`，用剛剛設定`Proxy.config.json`中第一個KEY值做開頭。
```javascript
export class SomeService {
  private basedUrl = '/api';

  signin(): Observable<Type> {
    return this.http.post(`${basedUrl}/signin`, body, options).map().subscribe();
  }
}
```

4. 停止並重新啟動`npm start`，就可以看到效果了。
