# Dapper - QuerySingle

## 描述
`QuerySingle`是一个可以从`IDbConnection`类型的任意对象调用的扩展方法，它可以执行查询并映射第一个结果，如果序列中没有元素则会引发异常。

结果可以映射到：

* [匿名类型](#anonymous)
* [强类型](#strongly-typed)

### 参数
下表显示了`QuerySingle`方法的不同参数。

名称 | 描述
---|---
sql | 要执行的查询。
param | 查询参数（默认为`null`）。
transaction | 需要使用的事务（默认为`null`）。
commandTimeout | 命令执行超时时间（默认为`null`）。
commandType | 命令类型（默认为`null`）。

### First, Single & Default
注意使用正确的方法。`First`和`Single`的方法是非常不同的。

结果 | 没有项 | 有一项 | 有多项
---|---|---|---
First           | 抛异常 | 当前项 | 第一项
Single          | 抛异常 | 当前项 | 抛异常
FirstOrDefault  | 默认值 | 当前项 | 第一项
SingleOrDefault | 默认值 | 当前项 | 抛异常


<h2 id="anonymous">案例 - 查询匿名类型</h2>

执行查询并将第一个结果映射到动态类型列表，如果序列中没有元素则会引发异常。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QuerySingle(sql, new {InvoiceID = 1});
}
```

<h2 id="strongly-typed">案例 - 查询强类型</h2>

执行查询并将第一个结果映射到强类型列表，如果序列中没有元素则会引发异常。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QuerySingle<Invoice>(sql, new {InvoiceID = 1});
}
```