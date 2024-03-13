## 【MYSQL】CASE文でNULLを条件に指定できない
```sql
-- NG
SELECT CASE name WHEN NULL 'NONAME' ELSE name END FROM hoge;

-- OK
SELECT CASE name IS NULL WHEN 1 'NONAME' ELSE name END FROM hoge;
```
- NGの書き方だとエラーは吐かないが意図した挙動にならない
- ので、`IS NULL`の結果が true になるかどうかで判定させると上手くいく

## NULL値を置換したい
```sql
-- mysql
IFNULL(name, 'NONAME')

-- postgres
COALESCE()

-- oracle
NVL()
```
- 第1引数に指定した値がNULLだったら第2引数に置き換える
