
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparare la stringa di connessione
Nella tabella seguente vengono confrontate le stringhe di connessione necessarie per il programma C# per la connessione al server SQL locale del Database SQL di Azure nel cloud. Le differenze sono in grassetto.

| Stringa di connessione per <br/>database SQL di Azure | Stringa di connessione per <br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{nome_server}**.database.windows.net,1433**;<br/>User ID={nome_accesso}**@{your_serverName_here}**;<br/>Password = {your_password_here};<br/>**Database={nome_database};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={nome_server};<br/>User ID={nome_accesso};<br/>Password = {your_password_here}; |

Il **Database =** è facoltativo per SQL Server, ma è necessario per il Database SQL.

[Proprietà SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) -descrive in dettaglio tutti i parametri.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


