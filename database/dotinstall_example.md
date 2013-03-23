# dotinstall.com 

## 02 データベースの用語

データベース：excelで例えると
↓
テーブル：sheet
↓
フィールド・カラム：列
レコード：行
 
## 03 データベースを操作してみよう 

### ファイル名 dotinstall.sqlite3 のデータベースを作成
```
mashe$ sqlite3 dotinstall.sqlite3
```
### 正しく作成されると以下のメッセージがでる
```
>SQLite version 3.7.7 2011-06-25 16:35:41
>Enter ".help" for instructions
>Enter SQL statements terminated with a ";"
```

### テーブルを作成
```
sqlite> create table users(name);
sqlite> .exit # 一旦終了して
```
データベースの中にテーブルを作成した時点で
データベースファイルは作成されるので ls で見てみると
```
mashe$ ls
>Desktop    	Library			Public
>Documents		Movies			data.txt
>Downloads		Music			dotinstall.sqlite3 ←←できてる。
>FrostWire		Pictures		単回帰分析.RData
```
データベースの削除は、ファイルを消去すればいい。
バックアップも、ファイルをコピーすればいい。

## 04 テーブルを操作してみよう

### 新規でテーブルusersを作成
```
sqlite> create table users(name, email);
```
### データベースに保存されているtableを確認する
```
sqlite> .tables
users
```
### テーブルの構造を調べる
```
sqlite> .schema users
CREATE TABLE users (name, email);
```
### テーブルの削除 (例；テーブル名lessonsを削除する場合)
```
sqlite> drop table lessons
```
## 05 テーブルを変更してみよう

### テーブル名を変更する
```
sqlite> alter table users rename to dotinstall_users;
```
```
sqlite> .table
dotinstall_users
```
### カラムpwdを追加する
```
sqlite> alter table dotinstall_users add column pwd;
```
```
sqlite> .schema
CREATE TABLE "dotinstall_users"(name, email, pwd);
```
## 06 データ型を指定する

### データ型の指定

NULL
INTEGER 整数
REAL 浮動小数点
TEXT 文字列
BLOB (Binary Large OBject)

* **データ型は指定できるけど必須ではない**

* **データ型を指定するとそれになるように努力するけど確約しない**


## 07 テーブル作成時のオプション(1)
```
sqlite> create table users(id     integer primary key autoincrement, #主キーの設定 
                           name   text    not null, # 空白ならエラーを返す
                           email  text    unique,   # 重複不可
                           age    integer default 20); # デフォルトは20
```

## 08 テーブル作成時のオプション(2)
```
sqlite> create table lessons (title, 
                              count_lessons check(count_lessons>0)); # count_lessonsは０以上のチェックを入れる
```
```
sqlite> .schema
CREATE TABLE lessons (title, count_lessons check(count_lessons>0));
CREATE TABLE users(id     integer primary key autoincrement, 
                   name   text    not null, 
                   email  text    unique, 
                   age    integer default 20);
```
### カラムにインデックスをつける
```
sqlite> create index age on users (age); # カラムageにインデックスをつける
```
```
sqlite> .schema
CREATE TABLE lessons (title, count_lessons check(count_lessons>0));
CREATE TABLE users(id     integer primary key autoincrement, 
                   name   text    not null, 
                   email  text    unique, 
                   age    integer default 20);
CREATE INDEX age on users (age);
```

## 09 データを挿入してみよう

### 文字列を挿入する時はシングルコーテーション'で囲む
```
sqlite> insert into users (name, email, age) values ('taguchi','taguchi@kcn.jp',20);
```
### シングルコーテーション'自体を文字列をして挿入するときは'' とする
```
sqlite> insert into users (name, email, age) values ('It''s a pen.','taguchi@kcn.jp',20);
```
### null もそのまま入れられる
```
sqlite> insert into users (name, email, age) values ('It''s a pen.','taguchi@kcn.jp',null);
```

## 10 データを抽出してみよう

### 新規にテーブルを作成
```
sqlite> create table users(name, score);
sqlite> .schema
CREATE TABLE users(name, score);
```
### データを挿入する
```
sqlite> insert into users (name, score) values('taguchi', 200);
sqlite> insert into users (name, score) values('sasaki', 100);
sqlite> insert into users (name, score) values('hiroshi', 400);
sqlite> insert into users (name, score) values('hirano', 800);
```
### すべてのデータを参照する
```
sqlite> select * from users;
taguchi|200
sasaki|100
hiroshi|400
hirano|800
```
### scoreだけ参照するときは
```
sqlite> select score from users;
200
100
400
800
```
### 並び替え score順に。昇順
```
sqlite> select score from users order by score;
100
200
400
800
```
### 降順
```
sqlite> select score from users order by score desc;
800
400
200
100
```
### 上位３位まで
```
sqlite> select score from users order by score desc limit 3;
800
400
200
```
### 条件式 whereをいれて
* 200点以上のscoreを抽出

```
sqlite> select score from users where score >= 200;
200
400
800
```
* 200点以上のnameを抽出

```
sqlite> select name from users where score >=200;
taguchi
hiroshi
hirano
```
### taguchi以外のデータを抽出 <>は、ノットイコールを表す
```
sqlite> select * from users where name <> 'taguchi';
sasaki|100
hiroshi|400
hirano|800
```
### taguchiだけは、イコールにして
```
sqlite> select * from users where name = 'taguchi';
taguchi|200
```
### 任意の文字列の例
tagから始まる名前の人を抽出。 %は任意の文字列を表す
```
sqlite> select * from users where name like 'tag%';

taguchi|200
```

## 11 組み込み関数を使おう

### 件数を調べる
```
sqlite> select count(*) from users;
4
```
### score の最大値を調べる
```
sqlite> select max(score) from users;
800
```
### 乱数もつかえる
```
sqlite> select random();
923154177291333564
```
### 無作為に一人抽出するときは
```
sqlite> select * from users order by random() limit 1;
hiroshi|400
```
### 文字列の長さをしらべる
```
sqlite> select name, length(name) from users;
taguchi|7
sasaki|6
hiroshi|7
hirano|6
```
### nameの型をしらべる
```
sqlite> select name, typeof(name) from users;
taguchi|text
sasaki|text
hiroshi|text
hirano|text
```
**関数の一覧は本家HPにて参照できる**

## 12 データを集計してみよう

### また新たにテーブルを作成
```
sqlite> create table users(name, score, team text);
```
### データを挿入する
```
sqlite> insert into users(name, score, team) values('taguchi', 200, 'A');
sqlite> insert into users(name, score, team) values('sasaki', 100, 'B');
sqlite> insert into users(name, score, team) values('hiroshi', 400, 'B');
sqlite> insert into users(name, score, team) values('hirano', 800, 'A');
```
```
sqlite> select * from users;
taguchi|200|A
sasaki|100|B
hiroshi|400|B
hirano|800|A
```
### どんなteamがあるのか調べる。 distinct
```
sqlite> select distinct team from users;
A
B
```
### チームごとのscoreの合計点を調べる
```
sqlite> select team, sum(score) from users group by team;
A|1000
B|500
```
## 13 日付・時刻を扱ってみよう

### 現在時刻
```
sqlite> select datetime(current_time,'localtime');
02:23:59

> select time('now', 'localtime');
```
### 日付
```
sqlite> select datetime(current_date,'localtime');
2013-02-21

> select date('now', 'localtime');
```
### 現在時刻と日付
```
sqlite> select datetime(current_timestamp, 'localtime');
2013-02-21 02:25:31

> select datetime('now', 'localtime');
```
### current_timeを日本語にしたいとき strftime(stringfile format time)
```
sqlite> select strftime('%H時%M分%S秒', datetime(current_time,'localtime'));
02時35分23秒
```
### current_dateを日本語にしたいとき
```
sqlite> select strftime('%Y年%m月%d日', datetime(current_date,'localtime'));
2013年02月21日
```

## 14 データを更新してみよう

### いま、次のようなテーブルがあるとする
```
sqlite> .schema
CREATE TABLE users(name, score, team text);
```
```
sqlite> select * from users;
taguchi|200|A
sasaki|100|B
hiroshi|400|B
hirano|800|A
```
### taguchi君の名前を変更したい時
```
sqlite> update users set name = 'dotinstall_taguchi' 
                     where name = 'taguchi';   #ここでwhere以下の条件式がないとname全体が変更されてしまうので注意
```
```
sqlite> select * from users;
dotinstall_taguchi|200|A
sasaki|100|B
hiroshi|400|B
hirano|800|A
```
### 一度にname と score を変更する nameをtaguchiにもどしてscoreを500に変更
```
sqlite> update users set name = 'taguchi', score = 500  where name = 'dotinstall_taguchi';
```
```
sqlite> select * from users;
taguchi|500|A
sasaki|100|B
hiroshi|400|B
hirano|800|A
```
## 15 データを削除してみよう

### 条件にあう人のデータを削除する
*100点以下を削除

```
sqlite> delete from users where score <= 100;
```
```
sqlite> select * from users;
taguchi|500|A
hiroshi|400|B
hirano|800|A
```
### ROWIDについて

*SQLiteは内部的に連番でデータを管理している。
```
sqlite> select ROWID, *from users;
1|taguchi|500|A
3|hiroshi|400|B  #ROWIDは上ですでに削除済
4|hirano|800|A
```
### 削除の対象となるデータがうまく検索できないときなどは、ROWIDをしていして削除する方法もある
* 例,ROWIDが4のデータを削除する場合

```
sqlite> delete from users where ROWID=4;
```
```
sqlite> select ROWID, * from users;
1|taguchi|500|A
3|hiroshi|400|B
```

## 16 複数のテーブルを扱ってみよう

データをあたらに作成
```
sqlite> create table games(user_id, score);
sqlite> create table users(id integer primary key autoincrement, name, team);

sqlite> insert into games(user_id, score) values(1,200);
sqlite> insert into games(user_id, score) values(1,300);
sqlite> insert into games(user_id, score) values(1,20);
sqlite> insert into games(user_id, score) values(1,220);
sqlite> insert into games(user_id, score) values(2,120);
sqlite> insert into games(user_id, score) values(2,50);
sqlite> insert into games(user_id, score) values(2,40);
sqlite> insert into games(user_id, score) values(3,40);
sqlite> insert into games(user_id, score) values(3,120);
sqlite> insert into games(user_id, score) values(3,140);
sqlite> insert into games(user_id, score) values(4,140);
sqlite> insert into games(user_id, score) values(4,10);
sqlite> insert into games(user_id, score) values(4,100);

sqlite> insert into users(id, name, team) values(1,'taguchi','A');
sqlite> insert into users(id, name, team) values(2,'sasaki','B');
sqlite> insert into users(id, name, team) values(2,'kimura','B');
Error: PRIMARY KEY must be unique
sqlite> insert into users(id, name, team) values(3,'kimura','B');
sqlite> insert into users(id, name, team) values(4,'yoshino','B')
```
```
sqlite> select * from games;
1|200
1|300
1|20
1|220
2|120
2|50
2|40
3|40
3|120
3|140
4|140
4|10
4|100
```
```
sqlite> select * from users;
1|taguchi|A
2|sasaki|B
3|kimura|B
4|yoshino|B
```
### ユーザーごとに合計点を計算する
```
sqlite> select id, name, team, sum(score) 
           from  users, games 
           where users.id=games.user_id 
           group by users.id;
1|taguchi|A|740
2|sasaki|B|210
3|kimura|B|300
4|yoshino|B|250
```

## 17 外部ファイルからデータを取り込む

取り込むデータをテキストファイルで用意する
ルートディレクトリに保存しておく
ファイル名users.txt
```
taguchi,200
sasaki,400
yamashita,120
koji,800
```
### 取り込むためのテーブルを用意する
```
sqlite> create table users(name, score);
```
```
sqlite> .schema
CREATE TABLE users(name, score);
```
### 区切り文字を確認
```
sqlite> .show
echo: off
explain: off
headers: off
mode: list
nullvalue: ""
output: stdout
separator: "|"  ← ←
stats: off
width: 
```
### 'カンマを区切り文字に指定
```
sqlite> .separator ,

sqlite> .show
echo: off
explain: off
headers: off
mode: list
nullvalue: ""
output: stdout
separator: ","   ← ←
stats: off
width: 
```
### 用意ができたので、外部データを取り込む
```
sqlite> .import users.txt users

sqlite> select * from users;
taguchi,200
sasaki,400
yamashita,120
koji,800
```
ドットコマンドも活用できる

### ドットコマンドのヘルプをみるには
```
sqlite> .help
```
### 例えば、.headerをつかってみると
```
sqlite> .header on
sqlite> select * from users;

name,score  ← header
taguchi,200
sasaki,400
yamashita,120
koji,800
```
## 18 データのバックアップ・復元

### .dumpは、データベースの内容をSQL分で出力するコマンド
```
sqlite> .dump users

PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE users(name, score);
INSERT INTO "users" VALUES('taguchi','200');
INSERT INTO "users" VALUES('sasaki','400');
INSERT INTO "users" VALUES('yamashita','120');
INSERT INTO "users" VALUES('koji','800');
```
### .dumpでの出力を外部ファイルに保存する
まず、現在の出力先を確認
```
sqlite> .show

echo: off
explain: off
headers: on
mode: list
nullvalue: ""
output: stdout   ← ← 今は標準出力（画面）
separator: ","
stats: off
width: 
```
### 出力先を外部ファイルusers.dumpに設定する
```
sqlite> .output users.dump
sqlite> .dump users
```
### 確認のため一旦sqliteを抜ける
```
sqlite> .exit

mashe$ ls
Desktop  		Movies			dotinstall.sqlite3
Documents		Music			  users.dump    ←←
Downloads		Pictures		users.txt
FrostWire		Public			単回帰分析.RData
```
### 内容を確認
```
mashe$ cat users.dump

PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE users(name, score);
INSERT INTO "users" VALUES('taguchi','200');
INSERT INTO "users" VALUES('sasaki','400');
INSERT INTO "users" VALUES('yamashita','120');
INSERT INTO "users" VALUES('koji','800');
COMMIT;
```
### 逆に、外部のdumpファイルを取り込むときは .readコマンドを使用
```
sqlite> .read users.dump

Error: near line 3: table users already exists
sqlite> drop table users;

sqlite> .read users.dump
sqlite> select * from users;
taguchi|200
sasaki|400
yamashita|120
koji|800
```
