# Mongo DB 刪除指令 #

## 刪除指令結構 ##

    db.collection.remove(
      < 刪除條件JSON >,
      {
        justOne: < 是否只刪除一筆 True or False >,
        writeConcern: < 設定JSON >
      }
    )
    
使用`update`，`insert`，`remove`等會修改到資料庫的指令時，可用`writeConcern`確認指令的執行情形

## 刪除指令範例 ##

- db.people.remove({count:{$gt:100}})
- 預設justOne為False

    原data：
    
        {count:100}
        {count:102}
    
    更新
    
        remove(
          {count:{$gt:100}}
        )
    
    後data：
    
    沒資料，但Collection會繼續存在
        
- 設定justOne為True

    原data：
    
        {count:100}
        {count:102}
    
    更新
    
        remove(
          {count:{$gt:100}},
          justOne:true
        )
    
    後data：
    
        {count:102}
        
## WriteConcern ##

`writeConcern: {w:0,wtimeout:5000}`

- `w`：writeConcern的類型

    發出請求 -> 網路傳送 -> 寫入journal log(不同DB作shard同步用) -> 寫入複製組

    - 0：回報網路傳送成功與否 
    
        (只到網路傳送後就回傳, 過程中是否有發生錯誤皆會回報)
    
    - 1：回報journal log寫入成功與否(包含0) **<- default**
    
        (到寫入journal log)
    
    - 2：回報至少一個複製組寫入成功與否(包含0,1)
    
        (至少要兩份資料寫入成功才會回報成功，否則就是回報失敗)
        
    - 大於2：回報至少n個複製組寫入成功與否，
    
        e.g. 設5代表至少寫入五份資料才會回報成功，否則回報失敗
        
    設置writeConcern愈謹慎，insert的時間就會愈久，速度就會愈慢
    
- `wtimeout`：等待回報的最大毫秒數，超過就會回報timeout錯誤。5000毫秒是預設值