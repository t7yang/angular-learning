

## 網址參數
你可以手動的方式串聯好所有的網址參數再送出請求：
```js
const queryString = `?search=${term}&action=opensearch&format=json&callback=JSONP_CALL`;

return this.jsonp
           .get(wikiUrl + queryString)
           .map(response => <string[]> response.json()[1]);
```

或是你可以使用內建的`URLSearchParams`來建立網址參數：
```js
import { URLSearchParams } from '@angular/http';

const params = new URLSearchParams();
params.set('search', term);
params.set('action', 'opensearch');
params.set('format', 'json');
params.set('callback', 'JSONP_CALL');

return this.jsonp
           .get(wikiUrl, { search: params })
           .map(response => <string[]> response.json()[1]);
```

get方法預期的參數有：`get(url: string|Request, options?: RequestOptionsArgs)`，第二個參數是一個`RequestOptionsArgs`型別的物件，其中的`search`鍵內容會自動轉化成網址參數。利用`URLSearchParams`和`RequestOptionsArgs`會比你自己寫一長串的網址還要方便且更不容易出錯。


## `form-urlencoded` 的 Request Body
在某些形況下，可能會碰到後端無法接收（或開發者不習慣接收）`JSON.stringify`的 Request Payload。這時還是要送`application/x-www-form-urlencoded`的 Request Body，但每次都要用手轉一次嗎？這麼機械式的動作，其實可以用一個轉換函數來解決，然後在其他服務中匯入就好。

```js
// shared.ts
import { URLSearchParams } from '@angular/http';

export function GenerateBody() {
    return function generateBody(body: object): string {
      const result = new URLSearchParams();
      Object.keys(body).forEach(key => result.set(key, body[key]));
      return result.toString();
    };
}

// logger.service.ts
import { GenerateBody } from './shared';

export class LoggerService {

  private generateBody = GenerateBody();

  somethingPost(toSend: Type): Observable<SomeType> {
    const body = this.generateBody(toSend);

    return this.http.post(`url`, body, headers)
      .map(...)
      .catch(...);
  }
}
```
