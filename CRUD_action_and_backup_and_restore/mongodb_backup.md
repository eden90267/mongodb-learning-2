# 備份方式 #

- 指令形式

    - 直接複製檔案資料(啟動Mongo DB指定的`--dbpath`資料夾內容)
    - 使用mongodump(BSON)
    - 使用mongoexport(JSON)

- 圖形介面

    - mongoVUE

---

## 指令形式 ##

### 直接複製檔案資料 ###

### 使用mongodump(BSON) ###

- Mongo資料庫執行中

    - `mongodump --collection [collection_name] db [資料庫名稱] -u test -p password`

- Mongo資料庫沒有執行
    - `mongodump --dbpath /data/db -u test -p password`

### 使用mongoexport(JSON) ###

- Mongo資料庫執行中

    - `mongoexport -db [資料庫名稱]  --collection [collection 名稱] -u test -p password --out export.json`

- Mongo資料庫沒有執行

   - `mongoexport --dbpath /srv/mongodb/ -u test -p password`
   - 沒有指定輸出檔案會直接打印在視窗上

## 圖形介面 ##

### mongoVUE ###