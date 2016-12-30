# Mongo DB 查詢指令 #

## 查詢指令結構 ##

    db.collection.find(
      < 條件式JSON >,
      < 呈現方式設定JSON >
    )
    
- `< 條件式JSON >`：篩選資料的條件
- `< 呈現方式設定JSON >`：設定回傳或是不回傳的欄位

## 常用條件式 ##

| 指令                                              | 效果                                        |
|---------------------------------------------------|---------------------------------------------|
| {_id:5}                                           | 指定_id欄位要等於數字5                      |
| {score:{$gt:0,$lt:2}}                             | 指定score大於0，小於2                       |
| {_id:{$in:[5,6,7]}}                               | 指定_id欄位要落在集合裡                     |
| {"name.first":"Yukihiro","name.last":"Matsumoto"} | 欄位name的子欄位first以及last符合輸入的字串 |

常見查詢子項目錯誤：

data：

    {
        name:
          {
              first: "Yukihiro",
              aka: "Matz",
              last: "Matsumoto"
          }
    }
    
這樣子MongoDB會認為name屬性要長成

    {
        first: "Yukihiro",
        ast: "Matsumoto"
    }
    
才是符合條件，這是錯誤的，因實際資料中間還有`aka: "Matz"`，會沒有完全相符。所以要這樣撰寫：

    {
        "name.first": "Yukihiro",
        "name.last": "Matsumoto"
    }
    
才符合！

是對name的子物件，而非對name的內容做篩選。

## 呈現方式設定 ##

- `{item:1,qty:1}`

    只顯示item，qty以及_id欄位(**_id為預設顯示**)

- `{'name.first':0,birth:0'}`

    指定不顯示那些欄位

- `{name:1,contribs:1,_id:0}`

    限定欄位且強制關閉_id欄位
    
## 進階呈現設定 ##

- `db.collection.find().sort({amount:-1})`

    - 依照amount排序
    - -1：遞減
    - 1：遞增

- `db.collection.find().skip(100).limit(50)`

    - 略過前100筆後列出50筆
    - 分頁式查詢，避免OutOfMemory，記憶體不夠的現象