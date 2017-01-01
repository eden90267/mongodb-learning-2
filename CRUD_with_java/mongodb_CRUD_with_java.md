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










---

## DEMO ##

[mongo-example](https://github.com/eden90267/mongo-example)