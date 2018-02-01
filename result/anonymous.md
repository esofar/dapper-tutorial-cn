# Dapper - 匿名类型结果

## 描述
可以使用扩展方法执行查询并使用动态类型映射结果。

匿名类型结果可以从以下扩展方法映射：

- [Query](#query)
- [QueryFirst](#query-first)
- [QueryFirstOrDefault](#query-first-or-default)
- [QuerySingle](#query-single)
- [QuerySingleOrDefault](#query-single-or-default)

这些扩展方法可以从IDbConnection类型的任意对象中调用。

<h2 id="query">案例 - Query</h2>

`Query`方法可以执行查询并将结果映射到动态类型列表。
```
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query(sql).ToList();
}
```

<h2 id="query-first">案例 - QueryFirst</h2>

`QueryFirst`方法可以执行查询并将第一个结果映射到动态类型列表。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QueryFirst(sql, new {InvoiceID = 1});
}
```

<h2 id="query-first-or-default">案例 - QueryFirstOrDefault</h2>

`QueryFirstOrDefault`方法可以执行查询并将第一个结果映射到动态类型列表，如果序列不包含任何元素则为默认值。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QueryFirstOrDefault(sql, new {InvoiceID = 1});
}
```

<h2 id="query-single">案例 - QuerySingle</h2>

`QuerySingle`方法可以执行查询并将第一个结果映射到动态类型列表，如果序列中没有元素则会引发异常。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QuerySingle(sql, new {InvoiceID = 1});
}
```

<h2 id="query-single-or-default">案例 - QuerySingleOrDefault</h2>

`QuerySingleOrDefault`方法可以执行查询并将第一个结果映射到动态类型列表，如果序列为空则为默认值；如果序列中有多个元素，则此方法将引发异常。
```
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.QuerySingleOrDefault(sql, new {InvoiceID = 1});
}
```