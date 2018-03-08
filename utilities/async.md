# Dapper - 异步

## 描述
Dapper还使用了Async（异步）方法扩展了`IDbConnection`接口：

- [ExecuteAsync](#execute)
- [QueryAsync](#query)
- [QueryFirstAsync](#query-first)
- [QueryFirstOrDefaultAsync](#query-first-or-default)
- [QuerySingleAsync](#query-single)
- [QuerySingleOrDefaultAsync](#query-single-or-default)
- [QueryMultipleAsync](#query-multiple)

我们只在本教程中添加了非异步版本，以便于阅读。

<h2 id="execute">ExecuteAsync</h2>

```
var sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var affectedRows = connection.ExecuteAsync(sql,
			new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
			commandType: CommandType.StoredProcedure)
		.Result;
}
```

<h2 id="query">QueryAsync</h2>

```
var sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.QueryAsync<Invoice>(sql).Result.ToList();
}
```

<h2 id="query-first">QueryFirstAsync</h2>

```
var sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoice = connection.QueryFirstAsync<Invoice>(sql, new {InvoiceID = 1}).Result;
}
```

<h2 id="query-first-or-default">QueryFirstOrDefaultAsync</h2>

```
var sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoice = connection.QueryFirstOrDefaultAsync<Invoice>(sql, new {InvoiceID = 1}).Result;
}
```

<h2 id="query-single">QuerySingleAsync</h2>

```
var sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoice = connection.QuerySingleAsync<Invoice>(sql, new {InvoiceID = 1}).Result;
}
```

<h2 id="query-single-or-default">QuerySingleOrDefaultAsync</h2>

```
var sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoice = connection.QuerySingleOrDefaultAsync<Invoice>(sql, new {InvoiceID = 1}).Result;
}
```

<h2 id="query-multiple">QueryMultipleAsync</h2>

```
var sql = "SELECT * FROM Invoice; SELECT * FROM InvoiceItem;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	using (var multi = connection.QueryMultipleAsync(sql, new { InvoiceID = 1 }).Result)
	{
		var invoice = multi.Read<Invoice>().First();
		var invoiceItems = multi.Read<InvoiceItem>().ToList();
	}
}
```