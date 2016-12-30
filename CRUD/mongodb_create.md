# Mongo DB 新增指令 #

## 新增指令結構 ##

    db.collection.insert(
        < JSON 或 JSON 組成的陣列 >,
        {
            writeConcern: < 設定JSON >,
            ordered: < True 或 False >
        }
    )
    
- `< JSON 或 JSON 組成的陣列 >`：要新增的一筆或多筆資料
- `writeConcern: <設定JSON>, ordered: < True 或 False >`：新增時的參數設定(Option)

ex：

    db.collection.insert(
        {item: "card", qty: 15},
        {
            writeConcern: < Document >,
            ordered: < True 或 False >
        }
    )

    db.collection.insert(
        [{item: "card", qty: 15},{item:"card",qty:15}],
        {
            writeConcern: < Document >,
            ordered: < True 或 False >
        }
    )
    
## writeConcern ##

writeConcern: {w:0,wtimeout:5000}

- w：writeConcern的類型

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
    
- wtimeout：等待回報的最大毫秒數，超過就會回報timeout錯誤。5000毫秒是預設值

## Ordered ##

ordered：是否強制照順序新增資料(**當資料為陣列時**)

- True：遇到錯誤資料**立即停止，而且不新增之後的資料(default)**
- False：跳過錯誤的資料，而且不照順序新增
    
