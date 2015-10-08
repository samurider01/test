# DBカラム上でフラグ管理する場合のTips

数値型のカラムを使ってフラグを管理するためのTipsです。

## 前提
- テスト用テーブルとデータを準備します。
```
mydb=# create table t_setting (type int primary key, value int);
CREATE TABLE
mydb=# insert t_setting into values (1,0);
# select * from t_setting ;
 type | value
------+-------
    1 |     0
    2 |     0
(2 rows)
```

## 数値型テーブルを2進数表示します。
```
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000000000000
(1 row)
```

## 4ビット目と8ビット目のフラグを立てます。
```
update t_setting set value = value | cast(B'00000000000000000000000010001000' as int) where type = 1;

mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010001000
(1 row)
```

## 4ビット目のフラグを落とします。(XOR)
- '#'を使います
```
mydb=# update t_setting set value = value # cast(B'00000000000000000000000000001000' as int) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010000000
(1 row)
```

- 同じUPDATE文を実行すると、4ビット目のフラグが立ちます。
```
mydb=# update t_setting set value = value # cast(B'00000000000000000000000000001000' as int) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010001000
(1 row)
```

## 16ビット目のフラグを立てます。
```
mydb=# update t_setting set value = value | (1 << 15) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000001000000010001000
(1 row)
```


