# Dapper - 结果多映射

## 描述
可以使用扩展方法执行查询并将结果映射到具有关系的强类型列表。

关系可以是：

- [一对一](#one-to-one)
- [一对多](#one-to-many)


这些扩展方法可以从IDbConnection类型的任意对象中调用。

<h2 id="one-to-one">案例 - 查询多映射（一对一）</h2>

`Query`方法可以执行查询并将结果映射到具有一对一关系的强类型列表。
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
}
```

<h2 id="one-to-many">案例 - 查询多映射（一对多）</h2>

`Query`方法可以执行查询并将结果映射到具有一对多关系的强类型列表。
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
}
```