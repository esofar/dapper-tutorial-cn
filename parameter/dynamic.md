# Dapper - 动态类型

## 描述
在Dapper方法中创建并使用参数。

### 单次

执行一次SQL命令。
```
var sql = "EXEC Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	DynamicParameters parameter = new DynamicParameters();

	parameter.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
	parameter.Add("@Code", "Many_Insert_0", DbType.String, ParameterDirection.Input);
	parameter.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

	connection.Execute(sql,
		parameter,
		commandType: CommandType.StoredProcedure);

	int rowCount = parameter.Get<int>("@RowCount");
}
```

### 多次

执行多次SQL命令。
```
var sql = "EXEC Invoice_Insert";

var parameters = new List<DynamicParameters>();

for (var i = 0; i < 3; i++)
{
	var p = new DynamicParameters();
	p.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
	p.Add("@Code", "Many_Insert_" + (i + 1), DbType.String, ParameterDirection.Input);
	p.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

	parameters.Add(p);
}

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	connection.Execute(sql,
		parameters,
		commandType: CommandType.StoredProcedure
	);

	var rowCount = parameters.Sum(x => x.Get<int>("@RowCount"));
}
```