---
title: "RESTful API 設計建議"
date: 2018-10-08T09:20:08+08:00
draft: fasle
---

今天想分享一些基於 Http 的 RESTful API 設計上的一些實用建議，讓我們的 Restful 能夠有著一致性的設計概念，client 在使用上也能夠有更好的理解

## HTTP request & response

當訪問一個頁面或是呼叫一個基於 HTTP 的 API 的時候，一定會包含 request 及 response，而它們也有相關的格式定義，底下說明這些格式以及包含了些什麼

### Request

本篇不會對每個設定都做詳細的說明，只是分享一個概念

一個 HTTP request 包含了兩個部分 head & body，首先我們先來看到 head 的部分

Head 的第一行表示一個動作、訪問的位置以及 HTTP 的版本，接著的下面幾行表示著多個附帶訊息，如請求的資源格式、內容的長度等，以 key:value 的型式做表達

接著是 body 的部分，在 head 最後一行留一個空白行(這是必要的)，空白行之後所表示的就是這個 request 的 body，它可以是任何資料，這裡的例子用的是 json 格式的字串

```

POST /employees/1 HTTP/1.1
Content-Type: application/json 
Content-Length: xxxx 

{"name", "Eric"}
```

### Response

與 request 是相對的，也有 head & body

Head 的第一行表示了 HTTP的版本、回應狀態碼以及狀態碼代表文字，與 request 一樣，接著的下面幾行表示著多個附帶訊息，同樣的，空白行之後為 body 的部分，用於提供給 client 端更多的資訊

```

HTTP/1.1 201 Created 
Content-Type: application/json 
Content-Length: 0 
Location: /employess/1

{"name", "Eric"}
```

## URI 格式

根據 https://www.ietf.org/rfc/rfc3986.txt
一個 URI 的格式會是以下這個樣子

> [scheme]://[authority]/[path]?[query]#[fregment]

### Scheme
對於本篇所要談論的範圍，scheme 在這裡指的是 http: 或者 https:

### Authority

RFC3986 內提到 authority 為以下格式

> authority = [ userinfo "@" ] host [ ":" port ]

本篇不會對前面的 userinfo@ 多做著墨，所以這邊我們通常指的是 host:port

### Path

用來表示資源(以本篇來說就是 API 的資源)所在的位置，以 / 來表示資源間的階層關係，舉例來說要表示某個部門底下的員工們，可以用以下格式來表示

> 表示部門編號1底下的所有員工

> /depts/1/employees 

### Query

用於表示沒有階層關係的資源，在 API 通常會用來表示篩選或排序資料

> 表示部門編號1底下所有男性員工

> /depts/1/employees?gender=male 

### Fregment

有個值得注意的地方，# 後的內容並不會送到 server 端，它一般是 client 端用來表示資源的特定位置或是顯示方式，有興趣可以看看這篇 https://blog.httpwatch.com/2011/03/01/6-things-you-should-know-about-fragment-urls

## URI 設計方式

### Documents

這裡指的是一個個的資料，通常以 json 物件來表示一個資料, 以下是用陣列包含兩個物件(documents) 的表示

```json

[
    {
        id: 1,
        name: "Eric",
        salary: 20000,
        gender: "male"
    },
    {
        id: 2,
        name: "Joanne",
        salary: 30000
        gender: "female"
    }
]
```

### Collections

代表的是一個集合，比如前面提到的員工集合，設計 API 的時候用複數表示

> GET /employees -> 回傳所有員工

> GET /employees/1 -> 回傳員編1號的員工資料

### HTTP verbs

有發現我們在上面的例子開始在前方加上一個單字 GET 嗎？我們在這裡繼續的詳細說明關於 Http verbs 在 RESTful API 的應用方式，在設計 API 時，一般會用 HTTP verbs 來表示對資源的操作方式，對比到資料庫操作的話就是所謂的 CRUD(增查修刪)，常見 verbs 有以下幾種

#### GET

用於查詢資源，查詢成功則一般回傳 200 OK HTTP status code，相關資訊放在 HTTP body 裡

以下為查詢特定單號的例子

* Request

```
GET /orders/A12345 HTTP/1.1
```

* Response

```
HTTP/1.1 200 OK 
Content-Type: application/json 
Content-Length: xxxx 

{"order_id": "A12345", "type": "credit_card", "create_time": "2018-10-10T16:00:00Z"} 
```

#### POST

新增一個資源到一個集合內或者執行一個動作(後面會提到)，新增完成時一般回應一個 201 Created 的狀態給 client，可以在 body 中增加一些額外的訊息，一個不錯的做法是在 head 的 Location 的參數給出一個查詢的 API 位置，以下為一個新增成功的例子

* Request

```
POST /employees HTTP/1.1

{"name": "Eric", "age": 28}
```

* Response

```
HTTP/1.1 201 Created 
Content-Type: application/json 
Content-Length: 0 
Location: /employees/1
```

#### PUT

用於更新一個可更新的資源，若無該資源就新增一筆資源，同樣的 request 內容，其回應的內容應該要是相同的，舉例來說

我們使用 POST 請求新增一筆資料 {"name": "Eric", "age": 28}，而後 server 會將該筆新增資料的 endpoint帶在 HTTP head 如 Location: /employees/1，用同樣的資料再請求一次新增，回應的 endpoint 會變成 Location: /employees/2

PUT 則是不會每次都新增一筆資料，例如第一次請求的內容為 {"name":"Eric", "age": 28} (假設我們的資料是以姓名判斷是否新增或更新)，因為該資料不存在，所以新增一筆資料，回應 /employees/1 第二次請求內容為 {"name":"Eric", "age": 30}，因為是同一個姓名所以只修改了 age 欄位，回應 /employees/1，第三次請求內容為 {"name":"Eric", "age": 30}，資料與原先一樣不做任何事情，但還是回應 /employees/1 

* Request

```
PUT /employees HTTP/1.1 
Content-Type: application/json 
Content-Length: xxxx 

{"name": "Eric", "age": 28}
```

* Response

```
HTTP/1.1 201 Created 
Content-Type: application/json 
Content-Length: 0
Location: /employees/1
```

#### PATCH

一般用於部分更新，比如想要對特定編號的員工做更改資料(姓名、薪資等)，一般更新成功回應 status code 會用 200 或 204，204 代表成功但沒有 body 內容，200 則可能帶有 body 內容

* Request

```
PATCH /employees/1 HTTP/1.1 
Content-Type: application/json 
Content-Length: xxxx 

{"salary": 30000} 
```

* Response

```
HTTP/1.1 204 No Body 
Content-Type: application/json 
Content-Length: 0
```

#### DELETE

用於刪除資源，回應代碼一般用 204 表示

* Request

```
DELETE /employees/1 HTTP/1.1 
Content-Type: application/json 
Content-Length: 0 
```

* Response

```
HTTP/1.1 204 No Body 
Content-Type: application/json 
Content-Length: 0 
```

#### HEAD

當 client 端想要像 GET 取得資源，但又不想要知道回應的 body 內容，比如只想知道資源是否存在或是只對該資源的 metadata 有興趣時，可以使用 HEAD verb 來做查詢的動作，下面的例子回應的就只有資源的長度跟最後修改時間，這樣可以減少一些傳輸的資源

* Request

```
HEAD /employees/1 HTTP/1.1 
Content-Type: application/json 
Content-Length: 0 
```

* Response

```
HTTP/1.1 200 OK 
Content-Type: application/json 
Last-Modified: 2018-10-10T20:30:01Z
Content-Length: 30
```

#### OPTION

對 endpoint 查詢可用 HTTP verbs

* Request

```
OPTIONS /employees/1 HTTP/1.1 
Content-Length: 0 
```

* Response

```
HTTP/1.1 200 OK 
Content-Length: 0 
Allow: GET, PUT, DELETE
```

### Action

這裡指的是對一個 collection 做出一個動作，比如對員工發獎金，action 的後面不應該再接其他的 collection，一般後面接著的是 query string

> 發給員編1號的員工 5000元 獎金

> POST /employees/1/reward?amount=5000

### HTTP query design

通常用在一些篩選的動作，比如 filter、sort、pagging等

> GET /employees?sort=age -> 查詢所有員工並依照年齡排序

> GET /employees?page=1&dept=mis -> 查詢 MIS 部門所有員工，只顯示第一頁

## Response codes

這個部分我只列出幾個很常用的 HTTP status code 給大家做參考，更詳細的可以參考這裡 https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html

### 2xx 成功

一般來說，我們以 2xx 開頭的回應狀態碼表示請求成功

#### 200 OK

最常見的成功回應代碼，用來表示這個請求成功

#### 201 Created

用於表示成功的建立了一個新的資源

#### 204 No Content

特別表示這個請求成功且沒有任何的 body 內容，比如請求一個 DELETE 則回覆此狀態碼

### 3xx 轉址相關

這個狀態碼比較常用在瀏覽器請求 server 後需要做導頁或是告訢 client 端請求回應的內容沒有做修改與上次一樣，前面提到通常用在瀏覽器請求是因為瀏覽器都有實作收到該狀態碼之後相應的動作，所以若是我們在 API server 端有這樣的設計的話(比如換 endpoint 或更新 endpoint 版本)，這將會需要請求端的配合實作轉址，現實上來說這在 API 設計上比較不實用

#### 301 Moved Permanently

告知 client 該請求 endpoint 已經不存在改由其他 endpoint 提供服務，HTTP 回應 head 會帶一個 Location 的參數，提供 client 做轉址動作

#### 302 Found

如同上面提到的 301 轉址，302 則表示的是一個暫時性的轉址

#### 304 Not Modified

常用於在 CDN 或是 caching server 用來告知 client 端資料沒有修改並不會回傳 body 內容給 client 端節省資源，通常在 HTTP 回應的 head 回帶一個資料過期的時間

### 4xx Client端錯誤

#### 400 Bad Request

用來表示 client 請求的內容不合法，比如說電話格式給錯之類

#### 401 Unauthorized

表示該請求是沒有經過授權的，比如該 API 是需要帶一個授權過的 token 才可以使用的，有點類似要登入才能使用的概念

#### 403 Forbidden

表示該請求經過授權但權限不足，比如登入一般用戶不可使用管理者的請求 endpoint，如果不想讓 client 知道太多被拒絕的原因，也可以使用下面講到的 404 Not Found 狀態碼來表示一個不存在的 endpoint

#### 404 Not Found

表示請求了一個不存在的 endpoint

#### 405 Method Not Allowed

表示請求特定 endpoint 不支援目前使用的 method，比如只能查詢不可新增

#### 408 Request Timeout

若我們設計的 API 希望客戶端在一定的時間內才能再次請求，可以回應此狀態碼，告訴 client 過一些時間再來請求

### 5xx Server端錯誤

所有不是客戶端或不可辯識的錯誤都以 5xx 為開頭表示

#### 500 Internal Server Error

這個狀態碼表示 server 目前有狀況，通常這都需要工程師或維運處理了

#### 503 Service Unavailable

這個狀態碼通常表示的是一個暫時性的 server 錯誤，比如說例行性的維護關機，或是 server 當時超載了

### Error handling

這裡將錯誤管理特別一個區塊說明是因為想要強調它的重要性，在設計 RESTful API 時，使用 HTTP status code 來表示請求的成功與否，意味著 client 端可以完全的靠這個 status code 來判斷該次請求是否成功，有些不好的 API 設計會造成 client 的誤解，以下提出不好與好的設計

#### 不好的設計

```
發送一個新增請求
POST /employees HTTP/1.1
{}

HTTP/1.1 200 OK 
{ 
  "status": 400, 
  "statusMessage": "Bad Request"
}
```

上面的設計會造成 client 搞不清楚請求是成功還是失敗

#### 好的設計

```
發送一個新增請求
POST /employees HTTP/1.1
{}

HTTP/1.1 400 OK 
{
    "error": {
        "code": 10001 // API 中自訂的更詳細的 error code
        "message": "請求內容不可為空白"
    }
}
```

Client 經由 HTTP status code 就可以知道請求是失敗的，若有需要知道更多的詳細錯誤原因，再去解析 body 的內容而得知確切問題所在

## 結語

以上是我對 RESTful API 的一些心得，希望能夠幫助到大家，若有任何不清楚或是需要指正的部分，歡迎隨時 E-Mail 給我做更進一步的討論


