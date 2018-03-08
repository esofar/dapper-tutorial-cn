# Dapper - 事务

## 描述

Dapper支持事务和范围事务。

## 事务

从连接开始一个新事务，并将其传递给事务可选参数。

```
var sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	using (var transaction = connection.BeginTransaction())
	{
		var affectedRows = connection.Execute(sql,
			new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
			commandType: CommandType.StoredProcedure,
			transaction: transaction);

		transaction.Commit();
	}
}
```

## 范围事务

在开始连接之前开始一个新的范围事务。

```
// using System.Transactions;

using (var transaction = new TransactionScope())
{
	var sql = "Invoice_Insert";

	using (var connection = My.ConnectionFactory())
	{
		connection.Open();

		var affectedRows = connection.Execute(sql,
			new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
			commandType: CommandType.StoredProcedure);
	}

	transaction.Complete();
}
```