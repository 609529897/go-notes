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

**MySQL 预处理**

把命令部分发给 mysql 进行预处理，等待接收参数

优化重复执行 SQL 的方法，可以提升服务器的性能，提前让服务器编译，一次编译多次执行，节省后续编译的成本。避免 SQL 注入问题

```go
func (db *DB) Prepare(query string)(*Stmt, error)
```

会把 SQL 语句先发给服务端，返回一个准备好的状态用于之后的查询和命令。返回值可以同时执行多个查询和命令。

```go
var db *sql.DB

func prepareQuery() {
	sqlStr := "select id, name age from user where id > ?"
    // 预处理
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare failed:%v\n", err)
	}
	defer stmt.Close()
    rows, err := stmt.Query(0)
    ...
    defer rows.Close()
    for rows.Next() {
        ...
    }
}
```

---

**事务**

```go
// 开始事务
func (db *DB) Begin(*Tx, error)

// 提交事务
func (tx *Tx) Commit() error

// 回滚事务
func (tx *Tx) Rollback() error
```

```go
func transactionDemo() {
	tx, err := db.Begin() // 开启事务
	if err != nil {
		if tx != nil {
			tx.Rollback() // 回滚
		}
		fmt.Printf("begin trans failed, err:%v\n", err)
		return
	}
	sqlStr1 := "Update user set age=30 where id=?"
	_, err = tx.Exec(sqlStr1, 2)
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec sql1 failed, err:%v\n", err)
		return
	}
	sqlStr2 := "Update user set age=40 where id=?"
	_, err = tx.Exec(sqlStr2, 4)
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec sql2 failed, err:%v\n", err)
		return
	}
	err = tx.Commit() // 提交事务
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("commit failed, err:%v\n", err)
		return
	}
	fmt.Println("exec trans success!")
}
```

---

**sqlx 的使用**

第三方库 `sql` 能够简化操作，提高开发效率

```shell
$ go get github.com/jmoiron/sqlx
```

```go
// 连接
db, err = sqlx.Connect("mysql", dsn)

// 查询
err := db.Get(&u, sqlStr, 1)

// 多行查询
err := db.Select(&users, sqlStr, 0)

// 插入、更新和删一样
```

```go
// 事务
func transactionDemo() {
	tx, err := db.Beginx() // 开启事务
	if err != nil {
		if tx != nil {
			tx.Rollback()
		}
		fmt.Printf("begin trans failed, err:%v\n", err)
		return
	}
	sqlStr1 := "Update user set age=40 where id=?"
	tx.MustExec(sqlStr1, 2)
	sqlStr2 := "Update user set age=50 where id=?"
	tx.MustExec(sqlStr2, 4)
	err = tx.Commit() // 提交事务
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("commit failed, err:%v\n", err)
		return
	}
	fmt.Println("exec trans success!")
}
```

**占位符语法**

|   数据库   |  占位符语法  |
| :--------: | :----------: |
|   MySQL    |     `?`      |
| PostgreSQL | `$1`, `$2`等 |
|   SQLite   |  `?` 和`$1`  |
|   Oracle   |   `:name`    |

---

---

**SQL 注入**：**我们任何时候都不应该让客户拼接SQL语句！**

---

## Redis

KV 数据库，支持五种数据结构

Redis 应用场景：

- 缓存系统，减轻主数据库（MySQL）的压力。
- 计数场景，比如微博，抖音中的关注数和粉丝数。
- 热门排行榜，需要排序的场景特别适合使用 ZSET。
- 利用 LIST 可以实现队列的功能。

**Go 操作 Redis**

```shell
# 使用第三方包
$ go get -u githun.com/go-redis/redis
```

> 荐书：《Redis 实战》

```go
package main

import (
	"fmt"

	"github.com/go-redis/redis"
)

var redisDB *redis.Client

func main() {
	err := initRedis()
	if err != nil {
		fmt.Printf("connect redis failed, err:%v\n", err)
	}
	fmt.Println("连接数据库成功！")
}

func initRedis() (err error) {
	redisDB = redis.NewClient(&redis.Options{
		Addr:     "127.0.0.1:6379",
		Password: "",
		DB:       0,
	})
	_, err = redisDB.Ping().Result()
	return
}
```

**基本使用**

`set` / `get`

```go
func redisExample() {
	err := redisdb.Set("score", 100, 0).Err()
	if err != nil {
		fmt.Printf("set score failed, err:%v\n", err)
		return
	}

	val, err := redisdb.Get("score").Result()
	if err != nil {
		fmt.Printf("get score failed, err:%v\n", err)
		return
	}
	fmt.Println("score", val)

	val2, err := redisdb.Get("name").Result()
	if err == redis.Nil {
		fmt.Println("name does not exist")
	} else if err != nil {
		fmt.Printf("get name failed, err:%v\n", err)
		return
	} else {
		fmt.Println("name", val2)
	}
}
```

`zset`

```go
func redisExample2() {
	zsetKey := "language_rank"
	languages := []*redis.Z{
		&redis.Z{Score: 90.0, Member: "Golang"},
		&redis.Z{Score: 98.0, Member: "Java"},
		&redis.Z{Score: 95.0, Member: "Python"},
		&redis.Z{Score: 97.0, Member: "JavaScript"},
		&redis.Z{Score: 99.0, Member: "C/C++"},
	}
	// ZADD
	num, err := redisdb.ZAdd(zsetKey, languages...).Result()
	if err != nil {
		fmt.Printf("zadd failed, err:%v\n", err)
		return
	}
	fmt.Printf("zadd %d succ.\n", num)

	// 把Golang的分数加10
	newScore, err := redisdb.ZIncrBy(zsetKey, 10.0, "Golang").Result()
	if err != nil {
		fmt.Printf("zincrby failed, err:%v\n", err)
		return
	}
	fmt.Printf("Golang's score is %f now.\n", newScore)

	// 取分数最高的3个
	ret, err := redisdb.ZRevRangeWithScores(zsetKey, 0, 2).Result()
	if err != nil {
		fmt.Printf("zrevrange failed, err:%v\n", err)
		return
	}
	for _, z := range ret {
		fmt.Println(z.Member, z.Score)
	}

	// 取95~100分的
	op := &redis.ZRangeBy{
		Min: "95",
		Max: "100",
	}
	ret, err = redisdb.ZRangeByScoreWithScores(zsetKey, op).Result()
	if err != nil {
		fmt.Printf("zrangebyscore failed, err:%v\n", err)
		return
	}
	for _, z := range ret {
		fmt.Println(z.Member, z.Score)
	}
}
```

---

## NSQ

NSQ 是目前比较流行的一个分布式消息队列。

**介绍**：

1. NSQ 提倡分布式和分散的拓扑，没有单点故障，支持容器和高可用性，并提供可靠的消息交付保证。
2. NSQ 支持横向扩展，没有任何集中式管理。
3. NSQ 易于配置和部署，并且内置了管理界面。

**应用场景**：

异步处理：利用消息队列把业务流程中的非常关键流程异步化，从而显著降低业务请求的响应时间

应用解耦：通过使用消息队列将不同的业务逻辑解耦，降低系统间耦合。后续有其他业务要使用订单数据可直接订阅消息队列，提高系统的灵活性。

流量削峰：类似秒杀（大秒）等场景下，某一时刻可能会产生大量的请求，使用消息队列能够为后端处理提供一定的缓冲区，保证后端服务的稳定性。

> 消息队列是啥？

---

**NSQ 组件**

```bash
nsqdlookupd.exe
```

默认在本机的 `127.0.0.1:4160` 启动

```bash
nsqd.exe -broadcast-address=127.0.0.1 -lookupd-tcp-address=127.0.0.1:4160
```

**Go 操作 NSQ**

安装

```bash
$ go get -u github.com/nsqio/go-nsq
```

生产者

```go
// nsq_producer/main.go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strings"

	"github.com/nsqio/go-nsq"
)

// NSQ Producer Demo

var producer *nsq.Producer

// 初始化生产者
func initProducer(str string) (err error) {
	config := nsq.NewConfig()
	producer, err = nsq.NewProducer(str, config)
	if err != nil {
		fmt.Printf("create producer failed, err:%v\n", err)
		return err
	}
	return nil
}

func main() {
	nsqAddress := "127.0.0.1:4150"
	err := initProducer(nsqAddress)
	if err != nil {
		fmt.Printf("init producer failed, err:%v\n", err)
		return
	}

	reader := bufio.NewReader(os.Stdin) // 从标准输入读取
	for {
		data, err := reader.ReadString('\n')
		if err != nil {
			fmt.Printf("read string from stdin failed, err:%v\n", err)
			continue
		}
		data = strings.TrimSpace(data)
		if strings.ToUpper(data) == "Q" { // 输入Q退出
			break
		}
		// 向 'topic_demo' publish 数据
		err = producer.Publish("topic_demo", []byte(data))
		if err != nil {
			fmt.Printf("publish msg to nsq failed, err:%v\n", err)
			continue
		}
	}
}
```

消费者

```go
// nsq_consumer/main.go
package main

import (
	"fmt"
	"os"
	"os/signal"
	"syscall"
	"time"

	"github.com/nsqio/go-nsq"
)

// NSQ Consumer Demo

// MyHandler 是一个消费者类型
type MyHandler struct {
	Title string
}

// HandleMessage 是需要实现的处理消息的方法
func (m *MyHandler) HandleMessage(msg *nsq.Message) (err error) {
	fmt.Printf("%s recv from %v, msg:%v\n", m.Title, msg.NSQDAddress, string(msg.Body))
	return
}

// 初始化消费者
func initConsumer(topic string, channel string, address string) (err error) {
	config := nsq.NewConfig()
	config.LookupdPollInterval = 15 * time.Second
	c, err := nsq.NewConsumer(topic, channel, config)
	if err != nil {
		fmt.Printf("create consumer failed, err:%v\n", err)
		return
	}
	consumer := &MyHandler{
		Title: "沙河1号",
	}
	c.AddHandler(consumer)

	// if err := c.ConnectToNSQD(address); err != nil { // 直接连NSQD
	if err := c.ConnectToNSQLookupd(address); err != nil { // 通过lookupd查询
		return err
	}
	return nil

}

func main() {
	err := initConsumer("topic_demo", "first", "127.0.0.1:4161")
	if err != nil {
		fmt.Printf("init consumer failed, err:%v\n", err)
		return
	}
	c := make(chan os.Signal)        // 定义一个信号的通道
	signal.Notify(c, syscall.SIGINT) // 转发键盘中断信号到c
	<-c                              // 阻塞
}
```

---

