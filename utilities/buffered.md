# Dapper - 缓冲

## 描述

* 默认值：True


缓冲查询一次返回整个读取器，这在大多数情况下是理想的。


非缓冲查询与流式传输等效，您只需按需加载对象，这对于一个非常大的查询来减少内存使用情况可能很有用。

```
string sqlInvoices = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
	var invoices = connection.Query<Invoice>(sqlInvoices, buffered: false).ToList();
}
```