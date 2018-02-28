# Dapper - 结果多结果

## 描述

`QueryMultiple`是一个扩展方法，可以从IDbConnection类型的任意对象中调用。它可以在同一命令中执行多个查询并映射结果。

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
