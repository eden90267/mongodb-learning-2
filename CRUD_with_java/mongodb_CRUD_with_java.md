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

### 切換 資料庫/collection ###

- 切換資料庫

    - `getDB("[資料庫名稱]")`

- 切換collection

    - `getCollection("[collection名稱]")`
    
```

DB db = mongoClient.getDB("test");

DBCollection coll = db.getCollection("testCollection");

```

### 關閉連線 ###

- Mongo DB有最大連線數限制

    - 太多會當機

- 釋放資源

```

MongoClient mongoClient = new MongoClient("localhost", 27017);

mongoClient.close();

```

---

## DEMO ##

[mongo-example](https://github.com/eden90267/mongo-example)