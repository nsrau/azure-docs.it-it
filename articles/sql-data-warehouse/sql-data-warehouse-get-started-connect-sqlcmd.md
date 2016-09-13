<properties
   pageTitle="Eseguire query in Azure SQL Data Warehouse (sqlcmd) | Microsoft Azure"
   description="Eseguire query in Azure SQL Data Warehouse con l'utilità della riga di comando sqlcmd."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>  

# Eseguire query in Azure SQL Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)

Questa procedura dettagliata usa l'utilità della riga di comando [sqlcmd][] per eseguire query in un'istanza di Azure SQL Data Warehouse.

## 1\. Connetti

Per iniziare a usare [sqlcmd][], aprire il prompt dei comandi e immettere **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione richiede i parametri seguenti:

+ **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
+ **Database (-d):** nome del database.
+ **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL Data Warehouse.

Per usare l'autenticazione di SQL Server è necessario aggiungere i parametri nome utente e password:

+ **Utente (-U):** utente del server nel formato `<`User`>`
+ **Password (-P):** password associata all'utente.

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Per usare l'autenticazione integrata di Azure Active Directory è necessario aggiungere i parametri di Azure Active Directory:

+ **Autenticazione di Azure Active Directory (-G):** consente di usare Azure Active Directory per l'autenticazione.

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Per eseguire l'autenticazione tramite Active Directory, è necessario [abilitare l'autenticazione di Azure Active Directory](sql-data-warehouse-authentication.md).

## 2\. Query

Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza. In questo esempio le query vengono inviate in modalità interattiva.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Questi esempi successivi illustrano come è possibile eseguire le query in modalità batch usando l'opzione -Q o inviando pipe di SQL a sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Passaggi successivi

Per altre informazioni sulle opzioni disponibili in sqlcmd, vedere [Utilità sqlcmd][sqlcmd].

<!--Image references-->  

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->  

<!---HONumber=AcomDC_0907_2016-->