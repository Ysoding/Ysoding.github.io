#postgresql

# Postgresql

## Install

[PostgreSQL: Downloads](https://www.postgresql.org/download/)

### 手动编译

[Installing PostgreSQL from Source — Ubuntu EC2 | by D Wheatley | Level Up Coding](https://levelup.gitconnected.com/installing-postgresql-from-source-ubuntu-ec2-420a3612119b)

```sql
sudo apt install build-essential zlib1g-dev libreadline-dev -y

wget https://ftp.postgresql.org/pub/source/v10.6/postgresql-10.6.tar.gz

tar xvfz postgresql-10.6.tar.gz

cd postgresql-10.6
./configure
make
sudo make install
```

## PersonName 类型值的 BNF

[BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)（巴科斯范式，Backus-Naur Form）是一种用于描述编程语言语法的正式语法。

```bnf
PersonName ::= Family','Given | Family', 'Given

Family     ::= NameList
Given      ::= NameList

NameList   ::= Name | Name' 'NameList

Name       ::= Upper Letters

Letter     ::= Upper | Lower | Punc

Letters    ::= Letter | Letter Letters

Upper      ::= 'A' | 'B' | ... | 'Z'
Lower      ::= 'a' | 'b' | ... | 'z'
Punc       ::= '-' | "'"
```

## 编写

这里拿 Complex[postgres/src/tutorial at master · postgres/postgres · GitHub](https://github.com/postgres/postgres/tree/master/src/tutorial)举例

安装依赖

```sh
sudo apt-get install postgresql-server-dev-all
```

编译运行生成 complex.so/complex.sql

```sh
make
```

将.so 文件 copy 到 postgresql 共享库目录下

```sh
sudo cp complex.so /usr/lib/postgresql/14/lib/
```

然后连接到 postgresql

```sh
psql -h localhost -U postgres -d postgres
```

运行 complex.sql 内容，即可使用。

## 自定义 PersonName 类型效果

[Private Code](https://github.com/cs-learning-every-day/business-hw/tree/main/pg-personname)

```sql
LINE 1: INSERT INTO person (name) VALUES ('Jesus');
                                          ^
postgres=# INSERT INTO person (name) VALUES ('Smith  ,  Harold');
ERROR:  invalid input syntax for type PersonName: "Smith  ,  Harold"
LINE 1: INSERT INTO person (name) VALUES ('Smith  ,  Harold');
                                          ^
postgres=# INSERT INTO person (name) VALUES ('Gates, William H., III');
ERROR:  invalid input syntax for type PersonName: "Gates, William H., III"
LINE 1: INSERT INTO person (name) VALUES ('Gates, William H., III');
                                          ^
postgres=# INSERT INTO person (name) VALUES ('A,B C');
ERROR:  invalid input syntax for type PersonName: "A,B C"
LINE 1: INSERT INTO person (name) VALUES ('A,B C');
                                          ^
postgres=# INSERT INTO person (name) VALUES ('Smith, john');
ERROR:  invalid input syntax for type PersonName: "Smith, john"
LINE 1: INSERT INTO person (name) VALUES ('Smith, john');

postgres=# SELECT family('Jonh,Data'::PersonName);
 family
--------
 Jonh
(1 row)

postgres=# SELECT given('Jonh,Data'::PersonName);
 given
-------
 Data
(1 row)

postgres=# SELECT show('Jonh,Data'::PersonName);
   show
-----------
 Data Jonh
(1 row)


postgres=# INSERT INTO person (name) VALUES ('Smith, John'), ('Smith, John'), ('O''Brien, Patrick Sean'), ('Mahagedara Patabendige, Minosha Mitsuaki Senakasiri'), ('I-Sun, Chen Wang'), ('Clifton-Everest, Charles Edward');
INSERT 0 6

postgres=# CREATE INDEX idx_person_name_hash ON person USING hash (name pname_hash_ops);
CREATE INDEX

```

## 参考

- [Extending PostgreSQL: Complex Number Data Type - Java Code Geeks](https://www.javacodegeeks.com/2015/08/extending-postgresql-complex-number-data-type.html)
- [PostgreSQL: Documentation: 9.3: User-defined Types](https://www.postgresql.org/docs/9.3/xtypes.html)
- [Postgresql 编写自定义 C 函数 | 学习笔记](https://zhmin.github.io/posts/postgresql-c-function/)
- [c - Postgres User Defined Types and allocating memory correctly - Stack Overflow](https://stackoverflow.com/questions/54173021/postgres-user-defined-types-and-allocating-memory-correctly)
- [38.13 User-defined Types](https://www.postgresql.org/docs/16/xtypes.html)
- [38.10 C-Language Functions](https://www.postgresql.org/docs/16/xfunc-c.html)
- [38.14 User-defined Operators](https://www.postgresql.org/docs/16/xoper.html)
- [SQL: CREATE TYPE](https://www.postgresql.org/docs/16/sql-createtype.html)
- [SQL: CREATE OPERATOR](https://www.postgresql.org/docs/16/sql-createoperator.html)
- [SQL: CREATE OPERATOR CLASS](https://www.postgresql.org/docs/16/sql-createopclass.html)
- [COMP9315 24T1 - Assignment 1](https://cgi.cse.unsw.edu.au/%7Ecs9315/24T1/assignments/ass1/index.php)
