# Go 第三部分

## 常用系统接口

- Stringer
- Reader / Writer

**接口组合**

```go
type ReadWriter interface {
    Reader
    Writer
}
```

---

## 数据库

- `SQLLite`
- `MySQL`
- `SQLServer`
- `postgreSQL`
- `Oracle`

MySQL 知识点

**SQL 语句**

```
DDL：操作数据库的
DML：表的增删改查
DCL：用户及权限
```

**存储引擎**

`MySQL` 支持插件式的存储引擎。

常见的存储引擎：`MyISAM` 和 `InnoDB`

`MyISAM`：

1. 查询速度快
2. 只支持表锁
3. 不支持事务

`InnoDB`：

1. 整体速度快
2. 支持表锁和行锁
3. 支持事务

事务：

把多个 SQL 操作当成一个整体

事务的特点：

ACID:

1. 原子性：事务要么成功要么失败，没有中间状态
2. 一致性：数据库的完整性没有被破坏
3. 隔离性：事务之间是互相隔离的
   + 隔离级别
     + 读未提交
     + 读提交
     + 可重复读
     + 串行化
4. 持久性：事务操作的结果是不会丢失的。

索引：

索引的原理：B 树和 B+ 树

索引的类型

索引的命中

分库分表

SQL 注入

SQL 慢查询优化

---

**关系型数据库**

用表来存一类数据

表结构设计的三大范式：《漫画数据库》

---

## MySQL + Go

Go 内置库：`database/sql` 泛接口

下载依赖

```shell
$ go get -u github.com/go-sql-driver/mysql
```

> go get 的默认保存在 `$GOPATH/src/github.com` 下

使用 MySQL 驱动

```go
func Open(driverName, dataSourceName string) (*DB, error)
```

Open 打开一个 dirverName 指定的数据库，dataSourceName 指定数据源，一般包含至少数据库文件名和（可能）连接信息，原生支持连接池，是并发安全的

**使用驱动**

```go
import (
	"database/sql"
    "fmt"

	_ "github.com/go-sql-driver/mysql"
)

func main() {
	dsn := "user:password@tcp(127.0.0.1:3306)/dbname"
    // 连接数据库
	db, err := sql.Open("mysql", dsn)
	if err != nil {
		panic(err)
	}
	defer db.Close()
    // 尝试连接数据库
    err = db.Ping()
    if err != nil {
        panic(err)
    }
    fmt.Println("连接数据成功...")
}
```

建库建表

```sql
CREATE DATABASE sql_test;
```

进入数据库

```sql
use sql_test;
```

执行以下命令创建一张用于测试的数据表：

```sql
CREATE TABLE `user` (
    `id` BIGNT(20)NOT NULL AUTO_INCREMENT,
    `name` VARCHAR(20)DEFAULT '',
    `age` INT(11) DEFAULT '0',
    PRIMARY KEY(`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4
```

---

**查询单行**

```go
func (db *DB) QueryRow(query string, args ...interface{}) *Row
```

```go
package main

import (
	"database/sql"
	"errors"
	"fmt"

	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB // 连接池对象

func main() {
	err := initDB()
	if err != nil {
		panic(err)
	}
	fmt.Println("数据库初始化成功...")
}

func initDB() (err error) {
	dsn := "user:password@tcp(127.0.0.1:3306)/dbname"
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return errors.New("Open error")
	}
	defer db.Close()
	err = db.Ping()
	if err != nil {
		return errors.New("Ping error")
	}
    db.SetMaxOpenConns(10) // 设置与数据库建立连接的最大数目
    db.SetMaxIdleConns(5)  // 设置连接池中最大闲置连接数
	fmt.Println("连接数据成功...")
	return nil
}

type user struct {
	id   int
	name string
	age  int
}

func queryRowDemo() {
	sqlStr := "select id, age from user where id=?"
	var u user
    // 从连接池里拿一个连接、去数据库查询单条记录。并放到 Scan 指定的地方、传指针
    // QueryRow(sqlStr, 1) 的第二个参数会传入 id=? 的 ? 占位符
	err := db.QueryRow(sqlStr, 1).Scan(&u.id, &u.name, &u.age)
	if err != nil {
		fmt.Printf("scan failed, err:%v\n", err)
		return
	}
	fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
}
```

> QueryRow 后必须调用 Scan 方法，Scan 方法会关闭数据库连接

---

**多行查询**

```go
func (db *DB) Query(query string, args ...instarface{}) (*Rows, error)
```

```go
type user struct {
	id   int
	name string
	age  int
}

func query(n int) {
	sqlStr := "select id, age from user where id > ?"
	rows, err := db.Query(sqlStr, n)
	if err != nil {
        fmt.Printf("query failed, err:%v\n", err)
		return
	}
    // 非常重要: 一定要关闭 rows 释放持有的数据库连接
    defer rows.Close()
    for rows.Next() {
        var u user
        err := rows.Scan(&u.id, &u.name, &u.age)
        if err != nil {
            fmt.Printf("scan failed, err:%v\n", err)
            return
        }
        fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
    }
}
```

---

**插入更新和删除**

插入

```go
func insert() {
	sqlStr := `insert into user(name, age) values("tom", 28)`
	ret, err := db.Exec(sqlStr)
	if err != nil {
		fmt.Printf("insert failed,err:%v\n", err)
	}
	// 如果是插入数据的操作、能够拿到插入数据的 id
	id, err := ret.LastInsertId()
	if err != nil {
		fmt.Printf("get id failed,err:%v\n", err)
	}
    // 返回插入项的 id
	fmt.Println("id:", id)
}
```

更新

```go
func update(newAge, id int) {
	sqlStr := `update user set age=? where id=?`
	ret, err := db.Exec(sqlStr, newAge, id)
	if err != nil {
		fmt.Printf("update failed,err:%v\n", err)
	}
	n, err := ret.RowsAffected() // 返回操作影响的行数
	if err != nil {
		fmt.Printf("get RowsAffected failed,err:%v\n", err)
	}
    fmt.Printf("update success, affected rows:%d\n", n)
}
```

删除

```go
func delRow(id int) {
    sqlStr := "delete from user where id=?"
    ret, err := db.Exec(sqlStr, id)
    if err != nil {
        fmt.Printf("delete failed, err:%v\n", err)
        return
    }
    n, err := ret.RowsAffected() // 返回操作影响的行数
    if err != nil {
        fmt.Printf("get RowsAffected failed, err:%v\n", err)
        return
    }
    fmt.Printf("delete success, affected rows:%d\n", n)
}
```

---

