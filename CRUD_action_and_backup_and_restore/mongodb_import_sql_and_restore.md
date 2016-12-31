# 匯入SQL及備份還原 #

- 指令形式

    - 資料夾形式
    - 使用`mongoimport` (JSON, CSV)
    - 使用`mongorestore` (BSON)

- 圖形介面

    - MongoVUE(匯入MySQL)

---

## 指令形式 ##

### 資料夾形式 ###

用`--dbpath`指定資料庫目錄

### 使用mongoimport (JSON, CSV) ###

- `mongoimport --db [資料庫名稱] --collection [collection 名稱] -u test -p password --file [JSON檔案]`

    - 不指定資料類型，表示使用JSON

- `mongoimport --db [資料庫名稱] --collection [collection 名稱] --type csv --headerline -u test -p password --file [CSV檔案]`

### 使用mongorestore (BSON) ###

- `mongorestore --collection [collection 名稱] --db [資料庫名稱] -u test -p password [BSON檔案]`
- 用於還原由`mongodump`產生的BSON檔案
- 兩個檔案都需要(`.bson & .metadaa.json`)

### 使用MongoVUE(匯入MySQL) ###