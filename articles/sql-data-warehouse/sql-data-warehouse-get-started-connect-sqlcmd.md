---
title: Connettersi ad Azure SQL Data Warehouse con sqlcmd | Documentazione Microsoft
description: "Usare l&quot;utilità della riga di comando [sqlcmd][sqlcmd] per connettersi ed eseguire query in un&quot;istanza di Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6e2b69e5-4806-4e91-9ea1-e2b63bf28c46
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 77474214c6fafe7f591030d30f6a46c66fbc5c09
ms.openlocfilehash: 1cd3bd8cab4e74da820f844d2ba96243cc6ccdcd


---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Connettersi a SQL Data Warehouse con sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Usare l'utilità della riga di comando [sqlcmd][sqlcmd] per connettersi ed eseguire query in un'istanza di Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Connetti
Per iniziare a usare [sqlcmd][sqlcmd], aprire il prompt dei comandi e immettere **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione richiede i parametri seguenti:

* **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
* **Database (-d):** nome del database.
* **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL Data Warehouse.

Per usare l'autenticazione di SQL Server è necessario aggiungere i parametri nome utente e password:

* **Utente (-U):** utente del server nel formato `<`User`>`
* **Password (-P):** password associata all'utente.

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Per usare l'autenticazione integrata di Azure Active Directory è necessario aggiungere i parametri di Azure Active Directory:

* **Autenticazione di Azure Active Directory (-G):** consente di usare Azure Active Directory per l'autenticazione.

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Per eseguire l'autenticazione tramite Active Directory, è necessario [abilitare l'autenticazione di Azure Active Directory](sql-data-warehouse-authentication.md) .
> 
> 

## <a name="2-query"></a>2. Query
Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza.  In questo esempio le query vengono inviate in modalità interattiva.

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle opzioni disponibili in sqlcmd, vedere [Utilità sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Portale di Azure]: https://portal.azure.com

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


