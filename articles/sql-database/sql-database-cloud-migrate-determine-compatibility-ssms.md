---
title: "SSMS - Compatibilità del database SQL Server di Azure | Documentazione Microsoft"
description: "In questo articolo viene illustrato come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio."
keywords: "Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, procedura guidata di esportazione dell’applicazione livello dati"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 82b76602133fa42db14e2095f29535b6002e9b2f
ms.openlocfilehash: efcf1179f744e1b3d1645321af62d2533677afd2


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Usare SQL Server Management Studio per determinare la compatibilità del database SQL prima di eseguire la migrazione al database SQL di Azure
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

In questo articolo viene illustrato come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Utilizzare SQL Server Management Studio
1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.
   
   > [!IMPORTANT]
   > È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività** e quindi fare clic su **Esporta l'applicazione livello dati**.
   
    ![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. Nell'esportazione guidata fare clic su **Avanti** e quindi nella scheda **Impostazioni** configurare l'esportazione per salvare il file BACPAC su un percorso disco locale o in un BLOB di Azure. Un file BACPAC viene salvato se non sono presenti problemi di compatibilità del database. Eventuali problemi di compatibilità vengono visualizzati nella console.
   
    ![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. Per evitare l'esportazione dei dati, fare clic sulla scheda **Avanzata** e deselezionare la casella di controllo **Seleziona tutto**. In questa fase l'obiettivo principale consiste nella verifica della compatibilità.
   
    ![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. Fare clic su **Avanti**, quindi su **Fine**. Eventuali problemi di compatibilità vengono visualizzati dopo la convalida dello schema da parte della procedura guidata.
   
    ![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. Se non vengono visualizzati errori, il database sarà compatibile e sarà possibile iniziare la migrazione. È necessario risolvere eventuali errori. Per visualizzare gli errori, fare clic su **Errore** per **Convalida schema**. 
    ![Impostazioni di esportazione](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. Se il file *.BACPAC è stato generato, il database è compatibile con il database SQL ed è possibile eseguire la migrazione.

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Risolvere i problemi di compatibilità della migrazione del database](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO2-->


