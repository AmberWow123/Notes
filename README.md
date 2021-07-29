# Spring Boot

Credit to [Vincent Zheng](https://chikuwa-tech-study.blogspot.com/)

## 1

### Definition
---
Spring Boot 是一種後端程式的框架(自帶第三方函式庫: JDBC, Mongo, Jackson)
Also, 內嵌伺服器軟體「Tomcat」，只要打包成 JAR 檔執行，即可直接啟動伺服器，讓我們更輕鬆地進行開發。

### Build a Spring Boot Project
---
[Spring Initializr](https://start.spring.io/): 快速產生全新的Spring Boot Project

## 2

### Design RESful API
---
前端程式如 App、Web，是由user親手去操作的，而後端程式則是被呼叫時才有所反應。

前端與後端之間的溝通方式，我們稱為「API」。
    
#### 1. API Definition

API as "Application Program Interface", 軟體系統不同部分的銜接約定

對前後端來說，就是雙方溝通的方式。
溝通 as request and response
> request: 前端發出的請求
> response: 後端給予的回應

> ex. 以生活化的例子來比喻，可以想像成客人在逛百貨公司的美食街，裡面有很多店家。決定好店家後，便填寫菜單，讓店家為自己服務。在這個例子中，百貨公司是系統、店家是 API，而菜單是資料。每個店家有不同的菜單，也就是接受不同的資料格式。

#### 2. How HTTP Works

當使用者發出請求，意味著要對某種東西(Data)進行操作(包含取得、編輯、新增、刪除等)

HTTP 請求方法便是以這些操作方式為基礎

1. GET：取得資源。
2. POST：新增資源。或是一個不容易歸類在其他方法的動作，例如登入。
3. PUT：覆寫資源。
4. PATCH：部份更新資源。
5. DELETE：刪除資源。

#### 3. RESTful API

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

#### 4. RESTful API in Spring Boot

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

## 3 - 在Controller實作API (1)

### Intro
---

後端程式的「Controller」，可視為前端傳送資料到達後端的「進入點」

在 Spring Boot 中，Controller 就類似櫃台，它能夠接收前端傳來的資料，也能將資料回應給前端。

[Source Code for Practice](https://github.com/ntub46010/SpringBootTutorial/tree/Ch1)

### 1. Build Controller and API
---

Controller 是以「class」的形式存在

> @RestController stands for a Controller

> @RequestMapping maps the return value to be JSON format
```java=
@RestController
@RequestMapping (produces = MediaType.APPLICATION_JSON_VALUE)
public class ProductController {

    // GET request API
    @GetMapping("/products/{id}")
    public Product getProduct(@PathVariable("id") String id) {
        Product product = new Product();
        product.setId(id);
        product.setName("Romantic Story");
        product.setPrice(200);
    
        return product;
    }

}
```

由於我們還沒有串接資料庫，因此範例程式中會產生模擬的 Product 資料，回應給 API 呼叫方

```java=
public class Product {
    private String id;
    private String name;
    private int price;
    
    public Product() {
    
    }
    
    public Product(String id, String name, int price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }
    
    // skip get() and set()
}
```

### 2. Send a GET request
---

Tool: [Postman Download](https://www.getpostman.com/downloads/)

開啟後，在畫面左上方可選擇 HTTP 請求方法，右邊欄位則填寫資源路徑。接下來我們要測試的 API 是剛剛的 GET /products/{id}，填寫完整路徑後，按下「Send」就能發出請求（request）。

![](https://i.imgur.com/xzFn7Ht.png)

這時畫面下方出現了後端伺服器的回應（response），包括 JSON 格式的資料及 HTTP 狀態碼200。

status code 200 stands for "success"

### 3. Design Response
---

實際上對於同一個 API，後端應視狀況給予不同回應。上網時最常見的莫過於「404 Not Found」了，其意義為資源不存在。

Spring Boot 提供了「回應實體」類別（**ResponseEntity**），讓開發者能將要回應的各種資訊，以一個物件來包裝。這節我們來改寫剛剛的程式。


```java=
@GetMapping("/products/{id}")
public Product getProduct(@PathVariable("id") String id) {
    Product product = new Product();
    product.setId(id);
    product.setName("Romantic Story");
    product.setPrice(200);
    
    return ResponseEntity.ok().body(product);
}
```

return **ResponseEntity** object時，可以用它的方法選擇常見的 HTTP 狀態，例如200（OK）、201（Created）、204（No Content）、404（Not Found）等，或者用 status 方法直接自行指定。最後將要回傳的產品資料放在回應主體（body）中。

### 4. 暫存資料
---

We don't connect with real database in this example. 

但為了後續的示範，接下來會以 Java 的 List 當作資料庫。因此本節先改寫 Controller 類別

```java=
@RestController
@RequestMapping(produces = MediaType.APPLICATION_JSON_VALUE)
public class ProductController2 {
    private final List<Product> productDB = new ArrayList<>();

    @PostConstruct
    private void initDB() {
        productDB.add(new Product("B0001", "Android Development (Java)", 380));
        productDB.add(new Product("B0002", "Android Development (Kotlin)", 420));
        productDB.add(new Product("B0003", "Data Structure (Java)", 250));
        productDB.add(new Product("B0004", "Finance Management", 450));
        productDB.add(new Product("B0005", "Human Resource Management", 330));
    }

    @GetMapping("/products/{id}")
    public ResponseEntity<Product> getProduct(@PathVariable("id") String id) {
        Optional<Product> productOp = productDB.stream()
                .filter(p -> p.getId().equals(id))
                .findFirst();

        if (productOp.isPresent()) {
            Product product = productOp.get();
            return ResponseEntity.ok().body(product);
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}
```

line 15 GET request:
> 會根據 id 在 productDB 這個 List 尋找產品。有找到就回傳資料（status code 200），otherwise return status code 404。

```initDB()```
> @PostConstruct: 這樣可以讓 Controller 被建立後，自動執行該方法，新增預設的產品資料。

Test:
> if id is found, then return 200. Otherwise, return 404

### 5. Send POST Request
---
#### Create new product and insert it into the List

```java=
public ResponseEntity<Product> createProduct(@RequestBody Product request) {
    // first check if id is duplicate or not
    boolean isIdDuplicated = productDB.stream()
            .anyMatch(p -> p.getId().equals(request.getId()));
    // if so, return status code 422
    if (isIdDuplicated) {
        return ResponseEntity.status(HttpStatus.UNPROCESSABLE_ENTITY).build();
    }
    
    Product product = new Product();
    product.setId(request.getId());
    product.setName(request.getName());
    product.setPrice(request.getPrice());
    productDB.add(product);
    
    URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(product.getId())
            .toUri();
            
    return ResponseEntity.created(location).body(product);
}
```

```@PostMapping``` -> set POST request API

```@RequestBody``` -> 來接收前端送來的請求主體（body）

Spring Boot 會將請求主體的 JSON 字串轉換為該資料型態的物件。

我們預期 API 呼叫方會傳送的資料包含產品的 id、name 與 price

新增資源的 API，一般會回應狀態碼201（Created），並附上指向這個新資源的 URI。這裡透過 ServletUriComponentsBuilder 來建立 URI。

1. ```fromCurrentRequest```：以目前呼叫的資源路徑為基礎來建立 URI，此處為「http://…/products」。
2. ```path```：以目前的資源路徑再做延伸，定義新的路徑格式，可加入佔位符，此處為「http://…/products/{id}」。
3. ```buildAndExpand```：將參數填入路徑中的佔位符，產生真實的資源路徑，此處為「http://…/products/實際產品編號」。

![](https://i.imgur.com/Ef72U5J.png)

這邊紀錄著「回應標頭」（response header）

其中「Location」欄位值就是產品的 URI，它會指向這次新增的資源。也就是說，對這個資源路徑發出 GET 請求，便能獲得該資源。


## 4－在 Controller 實作 API (2)

### Intro
---
Continue on PUT and DELETE request API, and introduce 網址上的參數（query string），並應用於 GET API

### Send PUT request
---

「覆蓋」掉特定的資源

```java=
@PutMapping("/{id}")
public ResponseEntity<Product> replaceProduct(
        @PathVariable("id") String id, @RequestBody Product request) {
    Optional<Product> productOp = productDB.stream()
            .filter(p -> p.getId().equals(id))
            .findFirst();

    if (productOp.isPresent()) {
        Product product = productOp.get();
        product.setName(request.getName());
        product.setPrice(request.getPrice());

        return ResponseEntity.ok().body(product);
    } else {
        return ResponseEntity.notFound().build();
    }
}
```

```@PutMapping```: set PUT request API

此處同時使用 ```@PathVariable``` 與 ```@RequestBody``` 標記，分別獲取資源路徑上的 id，以及請求主體經轉換後的資料。

if id is found, then update the corresponding info

程式寫好後，可用 Postman 發送請求，後端會回傳資源被覆蓋後的結果。若接續發送 GET 請求，也能印證資源確實有被更新。

![](https://i.imgur.com/a4xUATp.png)

### Send DELETE request
---

刪除指定的資源

實作上有時是真的把資料刪除（hard delete），有時是用一個欄位代表隱藏（soft delete），本節採用前者。

```java=
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteProduct(@PathVariable("id") String id) {
    boolean isRemoved = productDB.removeIf(p -> p.getId().equals(id));
    
    return isRemoved
            ? ResponseEntity.noContent().build()
            : ResponseEntity.notFound().build();
}
```

```@DeleteMapping```: set DELETE request API

```@PathVariable```: 標記獲取資源路徑上的 id


delete the info with the given id

if the id exists, then return 204 (no content), same as 200 (both mean succuss), but nothing in **ResponseEntity**

if not, return 404


### Query String (查詢字串)
---
#### Definition

網址後方有時會攜帶一些參數，由於常作為附加查詢條件的媒介

> 影音平台 YouTube 的網址是個簡單的例子，它的一個影片網址為
「https://www.youtube.com/watch?v=u6FcPuCs57o&t=80
」。查詢字串以「?」符號作為起始點。而參數間則以「&」符號區隔。以該網址來說，它附加了影片代號及點入連結後的影片開始秒數。

#### Practice

在 Controller 接收查詢字串，並應用於搜尋產品

```java=
@GetMapping("/products")
public ResponseEntity<List<Product>> getProducts(
        @RequestParam(value = "keyword", defaultValue = "") String name) {
    List<Product> products = productDB.stream()
            .filter(p -> p.getName().toUpperCase().contains(name.toUpperCase()))
            .collect(Collectors.toList());

    return ResponseEntity.ok().body(products);
}
```

```@RequestParam```: 可接收查詢字串，並將網址上名為「keyword」的參數賦值給 name 這個字串參數，作為搜尋關鍵字。該標記的 value 參數可指定要接收哪個查詢字串。若不指定，預設為方法的參數名稱 name。

而 defaultValue 參數則可在請求未帶上查詢字串時，給予 name 預設值。相對地，required 參數能規定是否必須帶上這個查詢字串，預設值為 true。若需要帶上卻沒帶，會得到狀態碼400（Bad Request）。

* 以下是沒帶查詢字串的結果
![](https://i.imgur.com/BRmNPOE.png)

* 以下是有帶查詢字串的結果
![](https://i.imgur.com/HWjW17Z.png)


### Receive Multiple Query Strings
---

> 在前一節的練習，@RequestParam 標記的參數太冗長了。若有多個查詢字串傳進來，這種寫法會使程式碼過多。我們可以自訂類別，並搭配 @ModelAttriture 標記，讓查詢字串的值自動賦值到該類別的物件中。


#### Practice
讓 Controller 接收額外的排序條件，藉此調整回傳結果

this class will receive multiple query strings
```java=
public class ProductQueryParameter {
    private String keyword;
    private String orderBy;
    private String sortRule;

    // 略過 get 與 set 方法
}
```

透過 ```@ModelAttribute``` 標記，讓 Spring Boot 將查詢字串的值賦予給 ```ProductQueryParameter``` 物件

Spring Boot 在透過標記賦予查詢字串的值時，是根據物件的 set 方法來執行。也就是說，若偵測到 keyword 的查詢字串，```setKeyword``` 方法就會自動被呼叫。

```java=
@GetMapping
public ResponseEntity<List<Product>> getProducts(@ModelAttribute ProductQueryParameter param) {
    String keyword = param.getKeyword();
    String orderBy = param.getOrderBy();
    String sortRule = param.getSortRule();
    Comparator<Product> comparator = genSortComparator(orderBy, sortRule);

    List<Product> products = productDB.stream()
            .filter(p -> p.getName().toUpperCase().contains(keyword.toUpperCase()))
            .sorted(comparator)
            .collect(Collectors.toList());

    return ResponseEntity.ok().body(products);
}

private Comparator<Product> genSortComparator(String orderBy, String sortRule) {
    Comparator<Product> comparator = (p1, p2) -> 0;
    if (Objects.isNull(orderBy) || Objects.isNull(sortRule)) {
        return comparator;
    }

    if (orderBy.equalsIgnoreCase("price")) {
        comparator = Comparator.comparing(Product::getPrice);
    } else if (orderBy.equalsIgnoreCase("name")) {
        comparator = Comparator.comparing(Product::getName);
    }

    return sortRule.equalsIgnoreCase("desc")
            ? comparator.reversed()
            : comparator;
}
```
首先取出各查詢字串

再透過 genSortComparator 方法，根據排序條件建立比較器（Comparator）

最後結合產品名稱關鍵字（keyword），篩選出符合條件的資料，經排序後回傳。

![](https://i.imgur.com/RDEdxez.png)



### API 路徑前綴
---

1. GET/products/{id}
2. GET/products
3. POST/products
4. PUT/products/{id}
5. DELETE/products/{id}

每個產品有關的 API，在標記中都要填寫 ```/products``` 開頭的資源路徑，似乎有點麻煩。其實我們可以在 Controller 類別使用 ```@RequestMapping``` 標記，並定義路徑前綴，之後其下的 API 資源路徑都會掛上它。

```java=
@RestController
@RequestMapping(value = "/products", produces = MediaType.APPLICATION_JSON_VALUE)
public class ProductController {
    // GET /products/{id}
    @GetMapping("/{id}")

    // GET /products
    @GetMapping

    // POST /products
    @PostMapping

    // PUT /products/{id}
    @PutMapping("/{id}")

    // DELETE /products/{id}
    @DeleteMapping("/{id}")
}
```

該標記還有另一種用法，是代替 ```@GetMapping``` 這種的 API 配置標記。由於 Spring Boot 並未提供所有請求方法的標記，因此其他種方法就得透過 ```@RequestMapping``` 標記設置。用法如下：

```java=
@RestController
@RequestMapping(value = "/products", produces = MediaType.APPLICATION_JSON_VALUE)
public class ProductController {
    // @GetMapping("/{id}")
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)

    // @PostMapping
    @RequestMapping(method = RequestMethod.POST)

    // @PatchMapping("/{id}")
    @RequestMapping(value = "/{id}", method = RequestMethod.PATCH)

    // Spring Boot 並未提供 HEAD 請求方法的配置標記
    @RequestMapping(method = RequestMethod.HEAD)
}
```

## 5.三層式架構

### Intro
---
我們在 Controller 撰寫了一些資料處理的程式碼，例如新增產品、查詢產品、篩選、排序等。但若全部都寫在 Controller，整個類別就會很龐大。適當地將程式碼抽離出來會對開發有幫助的。


### 分層的概念
---

後端程式分為三層
1. 表示層
    
    Controller, 負責接收前端的request, 並請Service處理, 最後將資料return(response)

2. 業務邏輯層

    Service (會被Controller呼叫), 負責根據請求來進行資料處理, 並return result
    
    也可能被其他Service呼叫
    
3. 資料持久層

    擔任與Database溝通的媒介, 會被 Service 呼叫
    
    常透過「資料存取物件」（data access object，DAO）來實現。

對不同層次賦予各自的職責，可以達到分工，而相同的程式碼也能方便地重複利用。當程式專案的規模變大後，便可感受到好處了。

根據物件導向的「封裝」特性，本文會將程式碼封裝到適當的類別，讓層與層之間透過方法這個「介面」來溝通。這麼做的好處，是當程式邏輯需要修改時，僅需調整該層的程式碼即可，不必動到更多檔案。

### 拋出 HTTP 例外
---

在前面文章的練習中，我們在 Controller 利用回應實體（ResponseEntity）直接回傳 HTTP 404（Not Found）、422（Unprocessable）的狀態，讓請求方知道有問題發生。

但等到 Controller 的程式碼被抽離後，就不適合這麼做了，畢竟 ResponseEntity 是專門回應給請求方的物件，不適合出現在擔任其他職責的類別。

####  HTTP 404
```java=
@ResponseStatus(HttpStatus.NOT_FOUND)
public class NotFoundException extends RuntimeException {

    public NotFoundException(String message) {
        super(message);
    }
}
```
```@ResponseStatus```: 定義拋出例外時回應給呼叫方的 HTTP status code

####  HTTP 422
```java=
@ResponseStatus(HttpStatus.UNPROCESSABLE_ENTITY)
public class UnprocessableEntityException extends RuntimeException {

    public UnprocessableEntityException(String message) {
        super(message);
    }
}
```

### 資料持久層 in practice
---
```@Repository```: 代表這是一個資料持久層

由於目前沒有串接真正的資料庫，所以這個類別一樣是用 productDB 這個 List 來模擬資料庫。

```java=
@Repository
public class MockProductDAO {
    private final List<Product> productDB = new ArrayList<>();

    @PostConstruct
    private void initDB() {
        productDB.add(new Product("B0001", "Android Development (Java)", 380));
        productDB.add(new Product("B0002", "Android Development (Kotlin)", 420));
        productDB.add(new Product("B0003", "Data Structure (Java)", 250));
        productDB.add(new Product("B0004", "Finance Management", 450));
        productDB.add(new Product("B0005", "Human Resource Management", 330));
    }
}
```

```insert(p)```:
```java=
public Product insert(Product product) {
    productDB.add(product);
    return product;
}
```


```replace(id, p)```:
```java=
public Product replace(String id, Product product) {
    Optional<Product> productOp = find(id);
    productOp.ifPresent(p -> {
        p.setName(product.getName());
        p.setPrice(product.getPrice());
    });

    return product;
}
```


```delete(id)```:
```java=
public void delete(String id) {
    productDB.removeIf(p -> p.getId().equals(id));
}
```


```find(id)```, ```find(param)```:
```java=
public Optional<Product> find(String id) {
    return productDB.stream()
            .filter(p -> p.getId().equals(id))
            .findFirst();
}

public List<Product> find(ProductQueryParameter param) {
    String keyword = Optional.ofNullable(param.getKeyword()).orElse("");
    String orderBy = param.getOrderBy();
    String sortRule = param.getSortRule();
    Comparator<Product> comparator = genSortComparator(orderBy, sortRule);

    return productDB.stream()
            .filter(p -> p.getName().contains(keyword))
            .sorted(comparator)
            .collect(Collectors.toList());
}
```


```genSortComparator(orderBy, sortRule)```:
```java=
private Comparator<Product> genSortComparator(String orderBy, String sortRule) {
    Comparator<Product> comparator = (p1, p2) -> 0;
    if (Objects.isNull(orderBy) || Objects.isNull(sortRule)) {
        return comparator;
    }

    if (orderBy.equalsIgnoreCase("price")) {
        comparator = Comparator.comparing(Product::getPrice);
    } else if (orderBy.equalsIgnoreCase("name")) {
        comparator = Comparator.comparing(Product::getName);
    }

    return sortRule.equalsIgnoreCase("desc")
            ? comparator.reversed()
            : comparator;
}
```


### 
---

## 6.

### Intro
---


## 7.

### Intro
---


## 8.

### Intro
---


## 9.

### Intro
---


## 10.

### Intro
---






###### tags: `Backend`
