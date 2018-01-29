# Dapper

## 什么是Dapper

Dapper是一个简单的.NET对象映射器，在速度方面具有"**King of Micro ORM**"的头衔，几乎与使用原始的ADO.NET数据读取器一样快。ORM是一个对象关系映射器，它负责数据库和编程语言之间的映射。

Dapper通过扩展`IDbConnection`提供一些有用的扩展方法去查询您的数据库。

## Dapper是如何工作的

它可以分为三个步骤：
* 创建一个`IDbConnection`接口对象；
* 编写一个查询SQL来执行CRUD操作；
* 将查询SQL作为`Execute`方法的参数传递。

## 安装

Dapper通过NuGet安装：[https://www.nuget.org/packages/Dapper](https://www.nuget.org/packages/Dapper)

```
PM> Install-Package Dapper
```
## 要求

Dapper可以与任何数据库提供者一起工作，因为没有数据库特定的实现。

## 方法

Dapper会用以下几个方法扩展您的`IDbConnection`接口：

* [Execute](methods-execute.md)
* [Query](methods-query.md)
* [QueryFirst](methods-query-first.md)
* [QueryFirstOrDefault](methods-query-first-or-default.md)
* [QuerySingle](methods-query-single.md)
* [QuerySingleOrDefault](methods-query-single-or-default.md)
* [QueryMultiple](#)

```
string sqlInvoices = "SELECT * FROM Invoice;";
string sqlInvoice = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID;";
string sp = "EXEC Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
        // 执行普通SQL
	var invoices = connection.Query<Invoice>(sqlInvoices).ToList();
	// 执行带参数的SQL
	var invoice = connection.QueryFirstOrDefault(sqlInvoice, new {InvoiceID = 1});
	// 执行存储过程 
	var affectedRows = connection.Execute(sp, new { Param1 = "Single_Insert_1" }, commandType: CommandType.StoredProcedure);
}
```

## 参数

执行和查询方法可以用以下几种不同的方式使用参数：

* [Anonymous](#) 
* [Dynamic](#) 
* [List](#) 
* [String](#)

```
// Anonymous
var affectedRows = connection.Execute(sql,
                    new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
                    commandType: CommandType.StoredProcedure);

// Dynamic
DynamicParameters parameter = new DynamicParameters();

parameter.Add("@Kind", InvoiceKind.WebInvoice, DbType.Int32, ParameterDirection.Input);
parameter.Add("@Code", "Many_Insert_0", DbType.String, ParameterDirection.Input);
parameter.Add("@RowCount", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

connection.Execute(sql,
	new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
	commandType: CommandType.StoredProcedure);

// List
connection.Query<Invoice>(sql, new {Kind = new[] {InvoiceKind.StoreInvoice, InvoiceKind.WebInvoice}}).ToList();

// String
connection.Query<Invoice>(sql, new {Code = new DbString {Value = "Invoice_1", IsFixedLength = false, Length = 9, IsAnsi = true}}).ToList();
```

## 结果

查询方法返回的结果可以映射到以下几种类型：

* [Anonymous](#)
* [Strongly Typed](#)
* [Multi-Mapping](#)
* [Multi-Result](#)
* [Multi-Type](#)

```
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var anonymousList = connection.Query(sql).ToList();
    var invoices = connection.Query<Invoice>(sql).ToList();
}
```

## 工具

* [Async](#) 
* [Buffered](#) 
* [Transaction](#)
* [Stored Procedure](#) 

```
// Async
connection.QueryAsync<Invoice>(sql)

// Buffered
connection.Query<Invoice>(sql, buffered: false)

// Transaction
using (var transaction = connection.BeginTransaction())
{
	var affectedRows = connection.Execute(sql,
		new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
		commandType: CommandType.StoredProcedure,
		transaction: transaction);

	transaction.Commit();
}

// Stored Procedure
var affectedRows = connection.Execute(sql,
	new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
	commandType: CommandType.StoredProcedure);
```