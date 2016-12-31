# 指令介面CRUD操作 #

## 利用指令介面連線 ##

1. 打開指令介面(mongo.exe)
2. 切換資料庫

    - `use [資料庫名稱]`

3. 登入資料庫

    - `db.auth("test","password")`
    
## 新增指令 ##

- `db.test.insert([{_id:0,count:100},{_id:1,count:101},{_id:2,count:102}])`

    - 一次新增多筆資料
    
- `db.test.insert([{_id:2,count:102},{_id:3,count:103}])`

    - `ordered`預設為`true`，在此範例會新增失敗
    
- `db.test.insert([{_id:2,count:102},{_id:3,count:103}],{ordered:false})`

    - 將`ordered`設為`false`，`_id:3`的資料會新增成功
    
## 修改指令 ##

- `db.test.update({count:100},{sum:500})`

    - 將`count:100`變成`sum:500`
    
- `db.test.update({sum:500},{$set:{count:100}})`

    - 將`sum:500`新增上`count:100`資訊
    
- `db.test.update({sum:500},{$set:{numbers:{a:100,b:400}}})`

    - 將`sum:500`新增上子項目
    
## 查詢指令 ##

- `db.test.find()`
- `db.test.find({sum:500})`
- `db.test.find({"numbers.a":100})`

    - 查詢子項目numbers的a欄位
    
## 刪除指令 ##

- `db.test.remove({count:102})`

    - 刪除該筆資料
    
- `db.test.remove({count:{$gt:101}})`

    - 刪除count大於101的資料
    
- `db.test.remove({})`

    - 刪除所有資料