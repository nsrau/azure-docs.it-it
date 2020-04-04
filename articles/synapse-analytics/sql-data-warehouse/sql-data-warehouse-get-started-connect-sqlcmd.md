---
title: Connettersi con sqlcmdConnect with sqlcmd
description: Utilizzare l'utilità della riga di comando sqlcmd per connettersi a un pool SQL Synapse ed eseguire una query.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633370"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Connettersi al pool SQL Synapse con sqlcmdConnect to Synapse SQL pool with sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilizzare l'utilità della riga di comando [sqlcmd][sqlcmd] per connettersi a un pool SQL ed eseguire query su un pool SQL.  

## <a name="1-connect"></a>1. Connetti
Per iniziare a utilizzare [sqlcmd][sqlcmd], aprire il prompt dei comandi e immettere **sqlcmd** seguito dalla stringa di connessione per il database del pool SQL. La stringa di connessione richiede i parametri seguenti:

* **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
* **Database (-d):** nome del database.
* **Abilita identificatori quotati (-I):** Gli identificatori tra virgolette devono essere abilitati per connettersi a un'istanza del pool SQL.

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

## <a name="2-query"></a>2. Interrogazione
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
Per ulteriori informazioni sulle opzioni disponibili in sqlcmd, vedere [la documentazione](https://msdn.microsoft.com/library/ms162773.aspx) di sqlcmd .
