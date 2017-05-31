

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

get方法預期的參數有：`get(url: string|Request, options?: RequestOptionsArgs)`，第二個參數是一個`RequestOptionsArgs`物件，其中的`search`鍵內容會自動轉化成網址參數。利用`URLSearchParams`和`RequestOptionsArgs`會比你自己寫一長串的網址還要方便且更不容易出錯。
