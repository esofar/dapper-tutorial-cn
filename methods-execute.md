## 描述
`Execute`是一个可以从`IDbConnection`类型的任意对象调用的扩展方法，它可以执行一个或多个命令并返回受影响的行数。此方法通常用于执行：

* [存储过程](#sp)
* [INSERT语句](#insert)
* [UPDATE语句](#update)
* [DELETE语句](#delete)

### 参数
下表显示了`Execute`方法的不同参数。

名称 | 描述
---|---
sql | 要执行的命令文本。
param | 命令参数（默认为`null`）。
transaction | 需要使用的事务（默认为`null`）。
commandTimeout | 命令执行超时时间（默认为`null`）。
commandType | 命令类型（默认为`null`）。

<h2 id="sp">案例 - 执行存储过程</h2>

### 单次
执行一次存储过程。
```
string sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
        new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
        commandType: CommandType.StoredProcedure);

    My.Result.Show(affectedRows);
}
```

### 多次
执行多次存储过程，为参数数组列表中的每个对象执行一次。
```
string sql = "Invoice_Insert";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
        new[]
        {
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_1"},
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_2"},
            new {Kind = InvoiceKind.StoreInvoice, Code = "Many_Insert_3"}
        },
        commandType: CommandType.StoredProcedure
    );

    My.Result.Show(affectedRows);
}
```

<h2 id="insert">案例 - 执行INSERT语句</h2>

### 单次
执行一次`INSERT`语句。
```
string sql = "INSERT INTO Invoice (Code) Values (@Code);";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql, new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"});

    My.Result.Show(affectedRows);
}
```

### 多次
执行`INSERT`语句，为参数数组列表中的每个对象执行一次。
```
string sql = "INSERT INTO Invoice (Code) Values (@Code);";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
        new[]
        {
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_1"},
            new {Kind = InvoiceKind.WebInvoice, Code = "Many_Insert_2"},
            new {Kind = InvoiceKind.StoreInvoice, Code = "Many_Insert_3"}
        }
    );

    My.Result.Show(affectedRows);
}
```
<h2 id="update">案例 - 执行UPDATE语句</h2>

### 单次
执行一次`UPDATE`语句。
```
string sql = "INSERT INTO Invoice (Code) Values (@Code);";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql, new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"});

    My.Result.Show(affectedRows);
}
```

### 多次
执行`UPDATE`语句，为参数数组列表中的每个对象执行一次。
```
string sql = "UPDATE Invoice SET Code = @Code WHERE InvoiceID = @InvoiceID";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
        new[]
        {
            new {InvoiceID = 1, Code = "Many_Update_1"},
            new {InvoiceID = 2, Code = "Many_Update_2"},
            new {InvoiceID = 3, Code = "Many_Update_3"}
        });

    My.Result.Show(affectedRows);
}
```

<h2 id="delete">案例 - 执行DELETE语句</h2>

### 单次
执行一次`DELETE`语句。
```
string sql = "DELETE FROM Invoice WHERE InvoiceID = @InvoiceID";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql, new {InvoiceID = 1});

    My.Result.Show(affectedRows);
}
```

### 多次
执行`DELETE`语句，为参数数组列表中的每个对象执行一次。
```
string sql = "DELETE FROM Invoice WHERE InvoiceID = @InvoiceID";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var affectedRows = connection.Execute(sql,
        new[]
        {
            new {InvoiceID = 1},
            new {InvoiceID = 2},
            new {InvoiceID = 3}
        });
}
```