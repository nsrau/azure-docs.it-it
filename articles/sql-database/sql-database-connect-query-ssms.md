---
title: Connettersi a un database SQL - SQL Server Management Studio | Documentazione Microsoft
description: Informazioni su come connettersi a un database SQL in Azure tramite SQL Server Management Studio (SSMS). Eseguire quindi una query di esempio usando Transact-SQL (T-SQL).
metacanonical: 
keywords: connettersi al database sql,sql server management studio
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: a6b147521525fad343376db0454f786a77b55c42


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 

Questo articolo illustra come connettersi a un database SQL di Azure usando SQL Server Management Studio (SSMS). Dopo aver stabilito la connessione verrà eseguita una semplice query Transact-SQL (T-SQL) per verificare la comunicazione con il database.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. Scaricare e installare la versione più recente di SSMS se non è già stato fatto, vedere [Scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Per mantenersi aggiornata, la versione più recente di SSMS chiede di scaricare la nuova versione quando è disponibile.

2. Dopo l'installazione, digitare **Microsoft SQL Server Management Studio** nella casella di ricerca di Windows e fare clic su **INVIO** per aprire SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Nella casella di dialogo Connetti al server immettere le informazioni necessarie per connettersi al server SQL usando Autenticazione di SQL Server.

    ![connetti al server](./media/sql-database-get-started/connect-to-server.png)
4. Fare clic su **Connect**.

    ![connesso al server](./media/sql-database-get-started/connected-to-server.png)
5. In Esplora oggetti espandere **Database** e quindi qualsiasi database per visualizzarne gli oggetti.

    ![nuovi oggetti del database di esempio con ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. Fare clic con il pulsante destro del mouse sul database e quindi scegliere **Nuova query**.

    ![nuova query del database di esempio con ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. Nella finestra della query digitare la query seguente:

   ```select * from sys.objects```
   
8.  Sulla barra degli strumenti fare clic su **Execute** (Esegui) per restituire un elenco degli oggetti di sistema nel database di esempio.

    ![nuovi oggetti del sistema per l'esecuzione di query sul database di esempio con ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> Per un'esercitazione, vedere [Esercitazione sul database SQL: creare un server, una regola del firewall a livello di server, un database di esempio, una regola del firewall a livello di database ed eseguire la connessione ad SQL Server](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Passaggi successivi

- È possibile usare istruzioni T-SQL per creare e gestire i database in Azure in modo analogo a SQL Server. Se si ha familiarità con l'uso di T-SQL con SQL Server, vedere [Informazioni su Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md) per un riepilogo delle differenze.
- Se non si ha familiarità con T-SQL, vedere [Esercitazione: Scrittura di istruzioni Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e [Guida di riferimento a Transact-SQL (Motore di database)](https://msdn.microsoft.com/library/bb510741.aspx).
- Per iniziare a creare gli utenti del database e gli amministratori degli utenti del database, vedere [Esercitazione sul database SQL: Creare account utente del database SQL per accedere a un database e gestirlo](sql-database-control-access-sql-authentication-get-started.md)
- Per altre informazioni su SSMS, vedere [Usare SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Jan17_HO3-->


