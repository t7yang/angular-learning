

## 基本的使用的RxJS
```js
// 匯入要使用的RxJS的項目
import { Observable } from 'rxjs/observable';
import 'rxjs/add/operator/catch';
import 'rxjs/add/operator/map';

getHeroes(): Observable<Hero[]> {
  // 每個http的方法回傳的都是Observable<Response>物件
  return this.http.get(this.heroesUrl)
                  .map(this.extractData)
                  .catch(this.handleError);
}

// Response物件會自帶json()方法把body轉成JSON
private extractData(res: Response) {
  let body = res.json();
  return body.data || {};
}

private handleError(error: Response | any) {
  let errMsg: string;

  if (error instanceof Response) {
    const body = error.json() || '';
    const err = body.error || JSON.stringify(body);
    errMsg = `${error.status} - ${error.statusText || ''} ${err}`;
  }
  else {
    errMsg = error.message ? error.message : error.toString();
  }

  // 實務上會回傳到伺服器做錯誤的紀錄
  console.log(errMsg);
  // 強制回傳一個Observable的錯誤，subscrible的時候，用第二個參數接錯誤
  return Observable.throw(errMsg);
}
```


### Observable vs. Promise
如果你原本就熟悉Promise的運作方式，你會發現兩者方式有點類似，只是在Promise時你期待的是用`then()`的方法去接，而Observable本身是一個**事件流**，所以每次會吐一個事件給你，這時就要用類似`map()`的方式去接（而實際上每一個HTTP請求也只會回傳一個事件回來而已，就那麼一個 XD）。


在捕捉錯誤的部分，方法一樣用`catch()`，只是Observable的`catch()`並非原生被JS/TS支援，所以要另外匯入`import 'rxjs/add/operator/catch'`（Map也一樣）。


## 回退到Promise的實作方式
雖然Observable可以帶來不少好處（但如果不會用、不想用就等於沒用），但是你還是可以把Observable重新轉成Promise物件來處理。以上述的`getHeroes()`為例：
```js
import 'rxjs/add/topromise';

getHeroes(): Promise<Hero[]> {
  return this.http.get(this.heroesUrl)
                  .toPromise()
                  .then(this.extractData)
                  .catch(this.handleError);
}

private extractData(res: Response) {
  // 這裡拿到的還是Response物件，所以有json()方法
  let body = res.json();
  return body.data || {};
}

private handleError(error: Response | any) {
  let errMsg: string;
  if (error instanceof Response) {
    const body = error.json() || '';
    const err = body.error || JSON.stringify(body);
    errMsg = `${error.status} - ${error.statusText || ''} ${err}`;
  } else {
    errMsg = error.message ? error.message : error.toString();
  }
  console.error(errMsg);
  // 回傳一個Promise.reject強制丟出錯誤，後續一樣用catch()接。
  return Promise.reject(errMsg);
}
```
