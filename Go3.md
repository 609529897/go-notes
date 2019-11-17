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

## MySQL + Go

数据库

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