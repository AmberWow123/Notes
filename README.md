# Spring Boot

Credit to [Vincent Zheng](https://chikuwa-tech-study.blogspot.com/)


Definition
---
Spring Boot 是一種後端程式的框架(自帶第三方函式庫: JDBC, Mongo, Jackson)
Also, 內嵌伺服器軟體「Tomcat」，只要打包成 JAR 檔執行，即可直接啟動伺服器，讓我們更輕鬆地進行開發。

Build a Spring Boot Project
---
[Spring Initializr](https://start.spring.io/): 快速產生全新的Spring Boot Project

Design RESful API
---
前端程式如 App、Web，是由user親手去操作的，而後端程式則是被呼叫時才有所反應。

前端與後端之間的溝通方式，我們稱為「API」。
    
### 1. API Definition

API as "Application Program Interface", 軟體系統不同部分的銜接約定

對前後端來說，就是雙方溝通的方式。
溝通 as request and response
> request: 前端發出的請求
> response: 後端給予的回應

> ex. 以生活化的例子來比喻，可以想像成客人在逛百貨公司的美食街，裡面有很多店家。決定好店家後，便填寫菜單，讓店家為自己服務。在這個例子中，百貨公司是系統、店家是 API，而菜單是資料。每個店家有不同的菜單，也就是接受不同的資料格式。

### 2. How HTTP Works

當使用者發出請求，意味著要對某種東西(Data)進行操作(包含取得、編輯、新增、刪除等)

HTTP 請求方法便是以這些操作方式為基礎

1. GET：取得資源。
2. POST：新增資源。或是一個不容易歸類在其他方法的動作，例如登入。
3. PUT：覆寫資源。
4. PATCH：部份更新資源。
5. DELETE：刪除資源。

### 3. RESTful API

REST as "Representational State Transfer", 他是一種設計風格，將網路上的東西都是為資源，並有不同操作方式。

一個完整的**RESTful API**，包含請求方法與資源路徑。
> 用網路書店舉例，一個商品頁面的網址為「https://www.books.com.tw/products/E050035254」，這就是資源的所在路徑。
> 從網址上來看，可以理解成這個資源是編號「E050035254」的商品。
> 此時搭配各種 HTTP 請求方法，即可組合出 RESTful API，表現出不同的意義。
> 1. GET /products/E050035254：取得編號為 E050035254 的商品。
> 2. GET /products：取得所有商品。
> 3. POST /products：新增商品。
> 4. PUT /products/E050035254：覆蓋編號為 E050035254 的商品。
> 5. DELETE /products/E050035254：刪除編號為 E050035254 的商品。

 > note: POST 方法為新增資料的請求，代表該商品在伺服器還不存在，當然不會有編號。

一個良好的 API，應該能輕易理解其意義。

它通常是由前後端的開發人員協調好，並遵循這個「約定」各自進行開發。

前端知道透過某個 API 會取得什麼資料，後端則知道某個 API 被呼叫時要做什麼事情。

### 4. RESTful API in Spring Boot

```java=
// 取得一個產品
@GetMapping("/products/{id}")

// 取得全部產品
@GetMapping("/products")

// 新增產品
@PostMapping("/products")

// 編輯一個產品
@PutMapping("/products/{id}")

// 刪除一個產品
@DeleteMapping("/products/{id}")

// 對一個購物車做結帳
@PostMapping("/carts/{id}/checkout")
```


RESTful API Controller
---

admaslkdmaslkdmaslkd

###### tags: `Backend`
