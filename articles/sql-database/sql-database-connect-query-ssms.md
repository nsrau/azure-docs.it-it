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
ms.date: 02/01/2017
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: a5eaf43aa01e5d30171ea038db7ba985c9684fb7
ms.lasthandoff: 02/16/2017


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio

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
> Per un'esercitazione vedere[Esercitazione: Effettuare il provisioning e accedere a un database SQL di Azure usando il portale di Azure e SQL Server Management Studio](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Passaggi successivi

- È possibile usare istruzioni T-SQL per creare e gestire i database in Azure in modo analogo a SQL Server. Se si ha familiarità con l'uso di T-SQL con SQL Server, vedere [Informazioni su Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md) per un riepilogo delle differenze.
- Se non si ha familiarità con T-SQL, vedere [Esercitazione: Scrittura di istruzioni Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e [Guida di riferimento a Transact-SQL (Motore di database)](https://msdn.microsoft.com/library/bb510741.aspx).
- Per un'introduzione all'esercitazione sull'autenticazione di SQL Server, vedere [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md) (Autenticazione e autorizzazione di SQL)
- Per un'introduzione all'esercitazione sull'autenticazione di Azure Active Directory, vedere [Azure AD authentication and authorization](sql-database-control-access-aad-authentication-get-started.md) (Autenticazione e autorizzazione di Azure AD)
- Per altre informazioni su SSMS, vedere [Usare SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).


