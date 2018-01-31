# Dapper - QueryMultiple

## 描述
`QueryMultiple`是一个可以从`IDbConnection`类型的任意对象调用的扩展方法，它可以在相同的命令和映射结果中执行多个查询。

```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID; SELECT * FROM InvoiceItem WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    using (var multi = connection.QueryMultiple(sql, new {InvoiceID = 1}))
    {
        var invoice = multi.Read<Invoice>().First();
        var invoiceItems = multi.Read<InvoiceItem>().ToList();
    }
}
```

### 参数
下表显示了`QueryMultiple`方法的不同参数。

名称 | 描述
---|---
sql | 要执行的查询。
param | 查询参数（默认为`null`）。
transaction | 需要使用的事务（默认为`null`）。
commandTimeout | 命令执行超时时间（默认为`null`）。
commandType | 命令类型（默认为`null`）。
