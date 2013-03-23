### SQLセミナー
本書はmySQLを題材とするが、ここではその内容をSQLiteに適用して記述していく。

### 011 主キーを指定してテーブルを作成する
```
> create table items(sid    integer primary key, 
                     sname  text, 
                     sprice real);
```

### 012 複数の列に主キーを指定する
```
> create table details ( sid    integer,
                         subid  integer,
                         sname  text,
                         sprice real,
                         primary key(sid, subid));
```
### 013 列にユニーク制約を付けてテーブルを作成する
```
> CREATE TABLE c_orders (no   integer unique,
                         order_day  date,
                         sid  integer,
                         num  integer);
```
### 014 デフォルト値を指定してテーブルを作成する
```
> CREATE TABLE items2 (sid    integer primary key,
                       sname  text,
                       sprice real    default 320);

```
### 015 テーブルに列を追加する
```
> alter table items2 add column status text;
```
```
> .schema items2
CREATE TABLE items2 (sid integer primary key,
                     sname text,
                     sprice integer default 320, 
                     status text);
```
### 016 指定したテーブルから列を削除する
**注** SQLiteでは列を削除するコマンドがないので、別の方法で実現する。
[ここを参照](http://androidhacker.blog94.fc2.com/blog-entry-68.html)

### 017 既存の表の主キーの設定を変更する
**注** これもSQLiteでは別の方法で実現する。
[ここを参照](http://androidhacker.blog94.fc2.com/blog-entry-59.html)

### 018 主キーを削除する
これも同様。

### 019 テーブルにデータを追加する

```
> insert into items(sid, sname, sprice) values(1001, 'コーヒー', 450);
> insert into items values(1002, '紅茶', 450);
> insert into items values(1003, ' 緑 茶', 400);
> insert into items vals values(1004, 'ウーロン茶',380); 
```
```
> select * from items;
1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380
```

### 020 特定のレコードを更新する
```
> update items set sprice=500 where sid = 1002;

> select * from items;
1001|コーヒー|450
1002|紅茶|500   ← ← ←
1003|緑茶|400
1004|ウーロン茶|380
```
### 021 指定した列の値をすべて更新する
```
> update items set sprice = sprice + 30;

> select * from items;
1001|コーヒー|480
1002|紅茶|530
1003|緑茶|430
1004|ウーロン茶|410
```
### 022 特定のデータを削除する
```
> delete from items where sid = 1003;

> select * from items;
1001|コーヒー|480
1002|紅茶|530
1004|ウーロン茶|410
```
### 023 テーブルのデータをすべて削除する
```
> delete from items;

> select * from items;
> 
```
## 4.データの取得
### 024 テーブルから指定した列だけ取得する
データを再び入力して、
```
> select sid,sname from items;
1001|コーヒー
1002|紅茶
1003|緑茶
1004|ウーロン
```

### 025 テーブルのすべてのデータを取得する
```
> select * from items;
1001|コーヒー|450
1002|紅    茶|450
1003|緑    茶|400
1004|ウーロン|380
```
### 026,027 単価から税込み価格を求める
ヘッダーを表示するよう設定
```
> .show
     echo: off
  explain: off
  headers: off  ← ← ←
     mode: list
nullvalue: ""
   output: stdout
separator: "|"
    stats: off
    width: 
    
> .header on

```
```
> select * , sprice*1.05 as '税込'from items;
sid|sname|sprice|税込
1001|コーヒー|450|472.5
1002|紅    茶|450|472.5
1003|緑    茶|400|420.0
1004|ウーロン|380|399.0
```
### 028 テーブルから指定したデータを選択する
```
> select * from items where sprice = 400;

sid|sname|sprice
1003|緑茶|400
```
### 029 テーブルから指定した値以外のデータを選択する
```
> select * from items where sprice <> 400;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
1004|ウーロン|380
```
### 030 テーブルから指定した値より大きいデータを選択する
```
> select * from items where sprice > 400;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
```
### 031 指定した日付以降のデータを選択する
まずはここで使うデータを作成
```
CREATE TABLE employees (id integer,
                        name text,
                        birthday text,
                        enterday text,
                        sec_id text);
                        
INSERT INTO "employees" VALUES(1,'田中','1950-10-24','1975-04-01','S001');
INSERT INTO "employees" VALUES(2,'斉藤','1955-08-23','1975-04-01','S001');
INSERT INTO "employees" VALUES(3,'小林','1965-06-14','1987-03-21','B001');
INSERT INTO "employees" VALUES(4,'大谷','1967-05-15','1980-05-15','B002');
```
```
> select * from employees where birthday > '1960-01-01';

id|name|birthday|enterday|sec_id
3|小林|1965-06-14|1987-03-21|B001
4|大谷|1967-05-15|1980-05-15|B002
```
### 032 いずれか１つの条件に合うデータを選択する
```
> select * from employees where sec_id='S001' or sec_id = 'B002';

id|name|birthday|enterday|sec_id
1|田中|1950-10-24|1975-04-01|S001
2|斉藤|1955-08-23|1975-04-01|S001
4|大谷|1967-05-15|1980-05-15|B002
```
あるいは
```
> select * from employees where sec_id IN ('S001', 'B002');

id|name|birthday|enterday|sec_id
1|田中|1950-10-24|1975-04-01|S001
2|斉藤|1955-08-23|1975-04-01|S001
4|大谷|1967-05-15|1980-05-15|B002
```
### 033 指定した値のいずれにも当てはまらないデータを選択する
```
> select * from employees where sec_id NOT IN ('S001', 'B002');

id|name|birthday|enterday|sec_id
3|小林|1965-06-14|1987-03-21|B001
```

### 034 指定した期間のデータを選択する
```
> select * from employees where enterday > '1980-01-01' and enterday < '1989-12-31';

id|name|birthday|enterday|sec_id
3|小林|1965-06-14|1987-03-21|B001
4|大谷|1967-05-15|1980-05-15|B002
```
あるいは、
```
> select * from employees where enterday BETWEEN '1980-01-01' AND '1989-12-31';

id|name|birthday|enterday|sec_id
3|小林|1965-06-14|1987-03-21|B001
4|大谷|1967-05-15|1980-05-15|B002
```
### 035 指定した文字列で終わるデータを選択する
```
> select * from items where sname like '%茶';

1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380

```
### 036 １文字だけが異なるデータを選択する
```
> select * from items where sname like '_茶';

1002|紅茶|450
1003|緑茶|400
```

### 037 重複するデータを除いて選択する
その前に、c_ordersにデータを挿入
```
CREATE TABLE c_orders (no integer unique,
                       order_day date,
                       sid integer,
                       num integer);
INSERT INTO "c_orders" VALUES(1,'2006-06-01',1003,10);
INSERT INTO "c_orders" VALUES(2,'2006-06-01',1001,20);
INSERT INTO "c_orders" VALUES(3,'2006-06-02',1004,15);
INSERT INTO "c_orders" VALUES(4,'2006-06-03',1002,5);
INSERT INTO "c_orders" VALUES(5,'2006-06-04',1002,15);
INSERT INTO "c_orders" VALUES(6,'2006-06-04',1001,10);
```
```
> select distinct order_day from c_orders;

2006-06-01
2006-06-02
2006-06-03
2006-06-04
```
### 038 データを昇順に並べ替えて取得する
```
> select * from items;

1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380

> select * from items order by sprice;

1004|ウーロン茶|380
1003|緑茶|400
1001|コーヒー|450
1002|紅茶|450
```
### 039 データを降順に並べ替えて取得する
```
> select * from items order by sprice desc;

1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380
```
### 040 複数の列を基準にして並べ替える
```
select * from c_orders order by num desc, sid asc;

no|order_day|sid|num
2|2006-06-01|1001|20
5|2006-06-04|1002|15
3|2006-06-02|1004|15
6|2006-06-04|1001|10
1|2006-06-01|1003|10
4|2006-06-03|1002|5
```
### 041 指定した値より大きく、かつ、指定した文字列で終わるデータを選択する
```
> select * from items where sprice >= 400 and sname like '%茶';

sid|sname|sprice
1002|紅茶|450
1003|緑茶|400
```
## 5.関数
### 042 データの合計を求める
```
> select * from c_orders;

no|order_day|sid|num
1|2006-06-01|1003|10
2|2006-06-01|1001|20
3|2006-06-02|1004|15
4|2006-06-03|1002|5
5|2006-06-04|1002|15
6|2006-06-04|1001|10

> select sum(num) from c_orders;

sum(num)
75
```
### 043 データの平均値を求める
```
> select avg(num) from c_orders;

avg(num)
12.5
```
### 044 テーブルのレコードの数を求める
```
> select * from employees;

id|name|birthday|enterday|sec_id
1|田中|1950-10-24|1975-04-01|S001
2|斉藤|1955-08-23|1975-04-01|S001
3|小林|1965-06-14|1987-03-21|B001
4|大谷|1967-05-15|1980-05-15|B002

> select count(*) from employees;

count(*)
4
```
### 045 条件に合うレコードの件数を求める
```
> select count(*) from employees where birthday > '1967-01-10';
count(*)
1

```
### 046,047 データの最大値,最小値を求める
```
> select * from c_orders;

no|order_day|sid|num
1|2006-06-01|1003|10
2|2006-06-01|1001|20
3|2006-06-02|1004|15
4|2006-06-03|1002|5
5|2006-06-04|1002|15
6|2006-06-04|1001|10

> select max(num) from c_orders;

max(num)
20

> select min(num) from c_orders;

min(num)
5
```
### 048 項目をグループ化して集計する
```
> select sid, sum(num) from c_orders group by sid;

sid|sum(num)
1001|30
1002|20
1003|10
1004|15
```
### 049 グループ化したデータに条件を指定して絞り込む
```
> select sid, sum(num) from c_orders group by sid having sum(num) > 10;

sid|sum(num)
1001|30
1002|20
1004|15
```
### 050 現在の日付を求める
```
> insert into c_orders(no, order_day, sid, num) 
               values (7, date('now','localtime'), 1004, 5);
               
sqlite> select * from c_orders;
no|order_day|sid|num
1|2006-06-01|1003|10
2|2006-06-01|1001|20
3|2006-06-02|1004|15
4|2006-06-03|1002|5
5|2006-06-04|1002|15
6|2006-06-04|1001|10
7|2013-03-03|1004|5
```
## 6.テーブルの結合
### 051 テーブルを内部結合する
まず、d_ordersテーブルをつくる。
```
CREATE TABLE d_orders (id integer primary key,
                       order_day date,
                       sname text,
                       num integer);
INSERT INTO "d_orders" VALUES(1,'2006-08-31','コーヒー',11);
INSERT INTO "d_orders" VALUES(2,'2006-09-01','紅茶',5);
INSERT INTO "d_orders" VALUES(3,'2006-09-01','緑茶',3);
INSERT INTO "d_orders" VALUES(4,'2006-09-02','紅茶',6);
INSERT INTO "d_orders" VALUES(5,'2006-09-02','コーヒー',5);
INSERT INTO "d_orders" VALUES(6,'2006-09-03','ほうじ茶',3);
```
```
sqlite> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅    茶|450
1003|緑    茶|400
1004|ウーロン茶|380

sqlite> select * from d_orders;

id|order_day|sname|num
1|2006-08-31|コーヒー|11
2|2006-09-01|紅    茶|5
3|2006-09-01|緑    茶|3
4|2006-09-02|紅    茶|6
5|2006-09-02|コーヒー|5
6|2006-09-03|ほうじ茶|3
```
```
> select order_day, d_orders.sname, sprice, num from d_orders 
                inner join items on d_orders.sname = items.sname;

order_day|sname|sprice|num
2006-08-31|コーヒー|450|11
2006-09-01|紅茶|450|5
2006-09-01|緑茶|400|3
2006-09-02|紅茶|450|6
2006-09-02|コーヒー|450|5
```
*内部結合は、どちらか一方のテーブルにしか存在しないデータは結合されない。

### 052 テーブルを左外部結合する
その前に、null値を表示させるよう設定を変更
```
> .show
     echo: off
  explain: off
  headers: on
     mode: list
nullvalue: ""
   output: stdout
separator: "|"
    stats: off
    width: 
sqlite> .nullvalue null
```
```
> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380

> select * from d_orders;

id|order_day|sname|num
1|2006-08-31|コーヒー|11
2|2006-09-01|紅茶|5
3|2006-09-01|緑茶|3
4|2006-09-02|紅茶|6
5|2006-09-02|コーヒー|5
6|2006-09-03|ほうじ茶|3

> select * from items left outer join d_orders on items.sname= d_orders.sname;

sid|sname|sprice|id|order_day|sname|num
1001|コーヒー  |450|1|2006-08-31|コーヒー|11
1001|コーヒー  |450|5|2006-09-02|コーヒー|5
1002|紅    茶  |450|2|2006-09-01|紅    茶|5
1002|紅    茶  |450|4|2006-09-02|紅    茶|6
1003|緑    茶  |400|3|2006-09-01|緑    茶|3
1004|ウーロン茶|380|null|  null |  null  |null
```
整理すると見やすくなる
```
> select items.sname, sprice, order_day, num from items 
                 left outer join d_orders on items.sname = d_orders.sname;
                 
sname|sprice|order_day|num
コーヒー|450|2006-08-31|11
コーヒー|450|2006-09-02|5
紅    茶|450|2006-09-01|5
紅    茶|450|2006-09-02|6
緑    茶|400|2006-09-01|3
ウーロン茶|380|null    |null
```
### 053 テーブルを右外部結合する
*SQLiteでは右外部結合はサポートされていない

### 054 テーブルを結合し、毎日の売上高を計算する
```
select order_day, sum(sprice*num) from d_orders 
          left outer join items on d_orders.sname = items.sname 
          group by order_day;
          
order_day|sum(sprice*num)
2006-08-31|4950
2006-09-01|3450
2006-09-02|4950
2006-09-03|null
```
### 055 2つの表からビュー表を作成する
```
> create view v_details as 
         select no, order_day, c_orders.sid, sname, num, sprice from items 
                 inner join c_orders on items.sid = c_orders.sid;
```
### 056 計算結果をビュー表に表示する
```
> select order_day, sid, sprice*num from v_details;

order_day|sid|sprice*num
2006-06-01|1003|4000
2006-06-01|1001|9000
2006-06-02|1004|5700
2006-06-03|1002|2250
2006-06-04|1002|6750
2006-06-04|1001|4500

> select order_day, sid, sprice*num from v_details order by sid;

order_day|sid|sprice*num
2006-06-01|1001|9000
2006-06-04|1001|4500
2006-06-03|1002|2250
2006-06-04|1002|6750
2006-06-01|1003|4000
2006-06-02|1004|5700
```
### 057 ビュー表を削除する
```
.table
c_orders   d_orders   details    employees  items      items2     v_details

> drop view v_details;

> .table
c_orders   d_orders   details    employees  items      items2
```
## 7.副問い合わせ
### 058 問い合わせ結果を条件としてデータを取得する
```
> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380

sqlite> select * from c_orders;

no|order_day|sid|num
1|2006-06-01|1003|10
2|2006-06-01|1001|20
3|2006-06-02|1004|15
4|2006-06-03|1002|5
5|2006-06-04|1002|15
6|2006-06-04|1001|10
```
```
> select * from c_orders where sid = (select sid from items where sname = '紅茶');

no|order_day|sid|num
4|2006-06-03|1002|5
5|2006-06-04|1002|15
```
### 059 複数の抽出結果を条件としてデータを取得する
まず、データを用意
```
> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
1003|緑茶|400
1004|ウーロン茶|380

> CREATE TABLE details( no integer primary key, sid integer, num integer);

> select * from details;
no|sid|num
1|1004|10
2|1001|20
3|1004|15
4|1002|5
5|1002|15
6|1001|10
```
注文のない商品リストを取得する
```
> select * from items where sid not in (select distinct sid from details);

sid|sname|sprice
1003|緑茶|400
```
### 060 複数の抽出結果を条件としてデータを取得する(EXISTS)
```
> select * from items where exists (select * from details where items.sid = details.sid);

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450
1004|ウーロン茶|380
```
## 8.トランザクション
### 061 更新されたデータを元のデータに復元する
```
> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450    ← ← ←
1003|緑茶|400
1004|ウーロン茶|380
```
```
> begin transaction;

> update items set sprice = 500 where sid = 1002;

sqlite> select * from items;

sid|sname|sprice
1001|コーヒー|450
1002|紅茶|500    ← ← ←
1003|緑茶|400
1004|ウーロン茶|380

> rollback;

> select * from items;
sid|sname|sprice
1001|コーヒー|450
1002|紅茶|450    ← ← ←
1003|緑茶|400
1004|ウーロン茶|380
```
### 062 更新されたデータを確定する
```
> begin transaction;

> insert into items values(1005, 'ココア', 480);

> commit;
```
