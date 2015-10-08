# DB�J������Ńt���O�Ǘ�����ꍇ��Tips

���l�^�̃J�������g���ăt���O���Ǘ����邽�߂�Tips�ł��B

## �O��
- �e�X�g�p�e�[�u���ƃf�[�^���������܂��B
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

## ���l�^�e�[�u����2�i���\�����܂��B
```
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000000000000
(1 row)
```

## 4�r�b�g�ڂ�8�r�b�g�ڂ̃t���O�𗧂Ă܂��B
```
update t_setting set value = value | cast(B'00000000000000000000000010001000' as int) where type = 1;

mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010001000
(1 row)
```

## 4�r�b�g�ڂ̃t���O�𗎂Ƃ��܂��B(XOR)
- '#'���g���܂�
```
mydb=# update t_setting set value = value # cast(B'00000000000000000000000000001000' as int) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010000000
(1 row)
```

- ����UPDATE�������s����ƁA4�r�b�g�ڂ̃t���O�������܂��B
```
mydb=# update t_setting set value = value # cast(B'00000000000000000000000000001000' as int) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000000000000010001000
(1 row)
```

## 16�r�b�g�ڂ̃t���O�𗧂Ă܂��B
```
mydb=# update t_setting set value = value | (1 << 15) where type = 1;
UPDATE 1
mydb=# select type, cast(value as BIT(32)) from t_setting ;
 type |              value
------+----------------------------------
    1 | 00000000000000001000000010001000
(1 row)
```


