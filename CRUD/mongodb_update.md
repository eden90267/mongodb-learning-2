# Mongo DB 修改指令 #

## 修改指令結構 ##

    db.collection.update(
       < 限制條件JSON >,
       < 更新的值JSON >,
       {
         upsert: < 如果條件不存在是否新增 True or False >,
         multi: < 一次更新多筆 True or False >,
         writeConcern: < 設定JSON >
       }
    )
    
## 修改指令範例 ##

### 範例一 ###

    db.people.update(
      {name: "Andy"},
      {name: "Andy", rating: 1, score: 1}
    )
    
- 姓名為Andy的資料
    
    - 直接轉換為修改的項目
    
```

{
  name: "Andy"
}

{
  name: "Andy",
  rating: 5,
  score: 3
}

{
  name: "Andy",
  rating: 5,
  score: 3,
  count: 5
}

```
            
->

```

{
  name: "Andy",
  rating: 1,
  score: 1
}

```
            
### 範例二 ###

    db.people.update(
      {name: "Andy"},
      $set: {rating: 1, score: 1}
    )
    
- 姓名為Andy的資料

    - 設定修改的項目
    - **非物件整個取代掉, 而是要做參數的設定**

(1)

```

{
  name: "Andy"
}

```

->

```

{
   name: "Andy",
   rating: 1,
   score: 1
}

```

(2)

```

{
  name: "Andy",
  rating: 5,
  score: 3
}

```

->

```

{
  name: "Andy",
  rating: 1,
  score: 1
}

```

(3)

```

{
  name: "Andy",
  rating: 5,
  score: 3,
  count: 5
}

```

->

```

{
  name: "Andy",
  rating: 1,
  score: 1,
  count: 5
}

```

這樣對一般Update的印象比較符合！

## upsert ##

- **Up**date + In**sert**
- 檢查是否有符合條件的資料

    - 有 -> 執行修改動作(只有一筆也算)
    
        原data：
        
            {count:100}
            {count:102}
        
        更新
        
            Update(
              {count:101},
              {count:103},
              upsert:true
            )
        
        後data：
        
            {count:100}
            {count:102}
            {count:103}
    
    - 沒有 -> 將修改動作當成新增執行(找不到任何一筆)
    
        原data：
        
            {count:100}
            {count:102}
        
        更新
        
            Update(
              {count:101},
              {count:103},
              upsert:false
            )
        
        後data：
        
            {count:100}
            {count:102}
            
## multi ##

- 是否同時更新多筆資料

    - False：更新一筆符合條件的資料 **(預設！！！)**
    
        原data：
        
            {count:100}
            {count:102}
        
        更新
        
            Update(
              {count:{$gt:100}},
              {count:103},
              multi:false
            )
        
        後data：
        
            {count:103}
            {count:102}
            
     - True：更新所有符合條件的資料
     
        原data：
        
            {count:100}
            {count:102}
        
        更新
        
            Update(
              {count:{$gt:100}},
              {count:103},
              multi:true
            )
        
        後data：
        
            {count:103}
            {count:103}
            
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