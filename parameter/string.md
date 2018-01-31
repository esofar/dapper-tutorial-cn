# Dapper - 字符串类型

## 描述

```
var sql = "SELECT * FROM Invoice WHERE Code = @Code;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.Query<Invoice>(sql, new {Code = new DbString {Value = "Invoice_1", IsFixedLength = false, Length = 9, IsAnsi = true}}).ToList();

	My.Result.Show(invoices);
}
```