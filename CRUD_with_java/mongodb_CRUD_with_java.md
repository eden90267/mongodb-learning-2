# Mongo DB CRUD operation with Java #

## 將Mongo DB Java driver加入專案中 ##

1. 下載Java driver(找尋相對應版本下載)

    - [download jar file](http://central.maven.org/maven2/org/mongodb/mongo-java-driver/)
    - [maven dependency](https://mvnrepository.com/artifact/org.mongodb/mongo-java-driver)

2. 加入專案中(import library or add dependency with maven pom.xml file)

## 建立Java與Mongo DB連線 ##

### 無身份驗證 ###

- MongoClient物件

    - 功能相當於mongo.exe
    - 用於登入 Mongo DB
    
```

MongoClient mongoClient = new MongoClient("localhost", 27017);

```

### 有身份驗證 ###

- MongoCredential物件

    - 用於建立身份驗證
    
- 可同時輸入多個身份驗證

```

MongoCredential credentialOne = MongoCredential.createMongoCRCredential("user1", "first", "password1".toCharArray()); // 參數：(使用者名稱, 資料庫名稱, 使用者密碼)
MongoCredential credentialTwo = MongoCredential.createMongoCRCredential("user2", "second", "password2".toCharArray());
MongoClient mongoClient = new MongoClient(new ServerAddress("localhost", 27017), Arrays.asList(credentialOne, credentialTwo));

```

P.S. Mongo DB 3.0後，需換成 `createCredential` 的靜態方法創建 `MongoCredential` 。以下截自官網：

Starting in MongoDB 3.0, MongoDB changed the default authentication mechanism from MONGODB-CR to SCRAM-SHA-1.

To create a credential that will authenticate using the default authentication mechanism regardless of server version, create a credential using the `createCredential` static factory method:


```

String user; // the user name
String database; // the name of the database in which the user is defined
char[] password; // the password as a character array
// ...
MongoCredential credential = MongoCredential.createCredential(user, database, password);
MongoClient mongoClient = new MongoClient(new ServerAddress("host1", 27017),
                                         Arrays.asList(credential));

```

參考資料：

[Authentication官網](http://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/authentication/)

## 切換 資料庫/collection ##

- 切換資料庫

    - `getDB("[資料庫名稱]")`

- 切換collection

    - `getCollection("[collection名稱]")`
    
```

DB db = mongoClient.getDB("test");

DBCollection coll = db.getCollection("testCollection");

```

## 關閉連線 ##

- Mongo DB有最大連線數限制

    - 太多會當機

- 釋放資源

```

MongoClient mongoClient = new MongoClient("localhost", 27017);

mongoClient.close();

```

## 新增 ##

- 建立`BasicDBObject`

    - JSON字串建立法

        - 當JSON已經建立時快速方便
        
    - 物件建立法

        - 由物件組成，擴展性佳

```

{
  "name":"MongoDB",
  "type":"database",
  "count":1,
  "info":{
        x:203,
        y:102
       }
}

```

### JSON字串建立法 ###

```

String json = 
"{
  "name":"MongoDB",
  "type":"database",
  "count":1,
  "info":{
        x:203,
        y:102
       }
}"

BasicDBObject obj = (BasicDBObject)
JSON.parse(json);

```

### 物件建立法 ###

```

{
  "name":"MongoDB",
  "type":"database",
  "count":1,
  "info":{
        x:203,
        y:102
       }
}

```

->

```

BasicDBObject doc = new BasicDBObject();
doc.put("name","MongoDB");
doc.put("type","database");
doc.put("count",1);

BasicDBObject info = new BasicDBObject();
info.put("x",203);
info.put("y",102);
doc.put("info",info);

```

### 新增至collection ###

- 確認DBCollection物件已經建立
- 使用insert新增物件

    - 相當於指令介面的`db.collection.insert()`
    - 支援`List<DBObject>`一次新增多筆資料

## 查詢 ##

- 建立查詢`BasicDBObject`

    - 物件建立法

### 物件建立法 ###

```

db.things.find(
  {
    type: "database",
    count: {$gt:1}
  }
);

```

->

```

BasicDBObject query =
     new BasicDBObject("type", "database")
     .append("count", new BasicDBObject("$gt",1));

```

### 對collection查詢 ###

- 存放至`DBCursor`物件中
- 使用`hasnext`確認是否有下一筆資料
- 讀取完畢，關閉`DBCursor`

```

DBCursor cursor = coll.find(query);
try {
  while(cursor.hasNext()) {
    System.out.println(cursor.next());
  }
} finally {
  cursor.close();
}

```

## 修改 ##

- 建立BasicDBObject

    - 物件建立法

### 物件建立法 ###

```

db.test.update(
  {
    count:1
  },
  {
    $set:{
      "info.x":204
    }
  }
)

```

->

```

BasicDBObject query = new BasicDBObject();
query.put("count", 1);

```

```

BasicDBObject update = new BasicDBObject();
update.put("$set",
    new BasicDBObject("info.x",204));

```

### 修改至Collection ###

- 一次只修改一筆

    - Multi：False
    - `Coll.update(query,update)`

- 一次修改多筆

    - Multi：True
    - `Coll.updateMulti(query,update)`

### 修改特定一筆資料 ###

- `BasicDBObject obj = coll.findOne(query);`

    - 取得符合條件的特定一筆資料

- `obj.put("type","save_test");`

    - 設定要更新的值

- `coll.save(obj);`

    - 直接存回更新後的物件
    - 修改其中`type`欄位的方式，非整個取代

## 刪除 ##

### 刪除特定條件內容 ###

- `collection.remove(new BasicDBObject().append("count",3));`

    - 刪除所有`count:3`的內容

- `collection.remove(new BasicDBObject());`

    - 刪除條件為空值 -> 刪除所有內容但保留collection

- `collection.drop();`

    - 直接刪除整個collection

### 取得受影響資料筆數 ###

- `WriteResult result = collection.remove(query);`

    - 使用`WriteResult`物件接收刪除完的結果

- `result.getN()`

    - 取出其中的N, 確定受影響的筆數

---

## DEMO ##

[mongo-example](https://github.com/eden90267/mongo-example)