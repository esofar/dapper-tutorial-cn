# Dapper - 列表类型参数

## 描述
Dapper允许您使用列表在IN子句中指定多个参数。

```
var sql = "SELECT * FROM Invoice WHERE Kind IN @Kind;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.Query<Invoice>(sql, new {Kind = new[] {InvoiceKind.StoreInvoice, InvoiceKind.WebInvoice}}).ToList();
}
```