---
title: Connettersi con sqlcmd
description: Usare l'utilità della riga di comando sqlcmd per connettersi ed eseguire query in un'istanza di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685745"
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

Utilizzare l'utilità della riga di comando [SQLCMD][sqlcmd] per connettersi ed eseguire query su un Azure SQL data warehouse.  

## <a name="1-connect"></a>1. connessione
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

## <a name="2-query"></a>2. query
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
Per ulteriori informazioni sulle opzioni disponibili in sqlcmd, vedere la [documentazione di sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
