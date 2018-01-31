# Dapper - Query

## 描述
`Query`是一个可以从`IDbConnection`类型的任意对象调用的扩展方法，它可以执行查询并映射结果。

结果可以映射到：

* [匿名类型](#anonymous)
* [强类型](#strongly-typed)
* [多映射（一对一）](#multi-mapping-one-to-one)
* [多映射（一对多）](#multi-mapping-one-to-many)
* [多类型](#multi-type)

### 参数
下表显示了`Query`方法的不同参数。

名称 | 描述
---|---
sql | 要执行的查询。
param | 查询参数（默认为`null`）。
transaction | 需要使用的事务（默认为`null`）。
buffered | 是否从缓冲读取查询结果（默认为`true`）。
commandTimeout | 命令执行超时时间（默认为`null`）。
commandType | 命令类型（默认为`null`）。

<h2 id="anonymous">案例 - 查询匿名类型</h2>

原生SQL查询可以使用`Query`方法执行，并将结果映射到动态类型列表。
```
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query(sql).ToList();
    
    My.Result.Show(invoices);
    
    //输出：3 anonymous entity returned
}
```

<h2 id="strongly-typed">案例 - 查询强类型</h2>

原生SQL查询可以使用`Query`方法执行，并将结果映射到强类型列表。
```
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query<Invoice>(sql).ToList();
    
    My.Result.Show(invoices);
    
    //输出：3 invoice(s) returned
}
```

<h2 id="multi-mapping-one-to-one">案例 - 查询多映射（一对一）</h2>

原生SQL查询可以使用`Query`方法执行，并将结果映射到具有一对一关系的强类型列表。
```
string sql = "SELECT * FROM Invoice AS A INNER JOIN InvoiceDetail AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query<Invoice, InvoiceDetail, Invoice>(
            sql,
            (invoice, invoiceDetail) =>
            {
                invoice.InvoiceDetail = invoiceDetail;
                return invoice;
            },
            splitOn: "InvoiceID")
        .Distinct()
        .ToList();
        
    My.Result.Show(invoices);
    
    //输出：3 invoice(s) returned (Including InvoiceDetail information) 
}
```

<h2 id="multi-mapping-one-to-many">案例 - 查询多映射（一对多）</h2>

原生SQL查询可以使用`Query`方法执行，并将结果映射到具有一对多关系的强类型列表。
```
string sql = "SELECT * FROM Invoice AS A INNER JOIN InvoiceItem AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoiceDictionary = new Dictionary<int, Invoice>();

    var invoices = connection.Query<Invoice, InvoiceItem, Invoice>(
            sql,
            (invoice, invoiceItem) =>
            {
                Invoice invoiceEntry;
                
                if (!invoiceDictionary.TryGetValue(invoice.InvoiceID, out invoiceEntry))
                {
                    invoiceEntry = invoice;
                    invoiceEntry.Items = new List<InvoiceItem>();
                    invoiceDictionary.Add(invoiceEntry.InvoiceID, invoiceEntry);
                }

                invoiceEntry.Items.Add(invoiceItem);
                return invoiceEntry;
            },
            splitOn: "InvoiceID")
        .Distinct()
        .ToList();
        
    My.Result.Show(invoices);
    
    //输出：3 invoice(s) returned (Including 6 InvoiceItem) 
}
```

<h2 id="multi-type">案例 - 查询多类型</h2>

原生SQL查询可以使用`Query`方法执行，并将结果映射到不同类型的列表。
```
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = new List<Invoice>();

    using (var reader = connection.ExecuteReader(sql))
    {
        var storeInvoiceParser = reader.GetRowParser<StoreInvoice>();
        var webInvoiceParser = reader.GetRowParser<WebInvoice>();

        while (reader.Read())
        {
            Invoice invoice;

            switch ((InvoiceKind) reader.GetInt32(reader.GetOrdinal("Kind")))
            {
                case InvoiceKind.StoreInvoice:
                    invoice = storeInvoiceParser(reader);
                    break;
                case InvoiceKind.WebInvoice:
                    invoice = webInvoiceParser(reader);
                    break;
                default:
                    throw new Exception(ExceptionMessage.GeneralException);
            }

            invoices.Add(invoice);
        }
    }
    
    My.Result.Show(invoices); 
    
    //输出：3 invoice(s) returned (StoreInvoice:1, WebInvoice:2) 
}
```
