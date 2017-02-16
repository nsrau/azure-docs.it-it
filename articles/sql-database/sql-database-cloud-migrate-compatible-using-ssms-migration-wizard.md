---
title: SSMS - Migrare un database SQL Server nel database SQL di Azure | Documentazione Microsoft
description: Questo articolo illustra come usare la procedura guidata Distribuire il database nel database di Microsoft Azure in SQL Server Management Studio per eseguire la migrazione di un database SQL Server compatibile direttamente nel server di database SQL di Azure.
keywords: Database SQL di Microsoft Azure, migrazione del database, creazione guidata database di Microsoft Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 91d3fd5e-d035-4c55-b1ea-a7ccc8e0f543
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f4cbfc1a277cbe80edbfbb4dc651adb1972f41b4


---
# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure
> [!div class="op_single_selector"]
> * [Migrazione guidata in SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Esportare in un file BACPAC.](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importare da file BACPAC.](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Questo articolo illustra come usare la procedura guidata Distribuire il database nel database di Microsoft Azure in SQL Server Management Studio per eseguire la migrazione di un [database SQL Server compatibile](sql-database-cloud-migrate.md) direttamente nel server di database SQL di Azure.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Usare la procedura guidata Distribuire il database nel database di Microsoft Azure
> [!NOTE]
> La procedura seguente presuppone la presenza di un [server del database SQL con provisioning](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).
> 
> 

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.
   
   > [!IMPORTANT]
   > È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Aprire Management Studio e connettersi al database del server SQL da migrare in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Distribuisci il database nel database SQL di Microsoft Azure**
   
    ![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)
4. Nella distribuzione guidata fare clic su **Avanti** e quindi su **Connetti** per configurare la connessione al server del database SQL.
   
   ![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)
5. Nella finestra di dialogo Connessione al server, immettere le informazioni di connessione per connettersi al server del database SQL. Per ottenere il nome del server per un server esistente, vedere [View and update an SQL SErver Database server and its settings](sql-database-view-update-server-settings.md) (Visualizzare e aggiornare un server di database SQL di Azure e le relative impostazioni).
   
    ![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)
6. Fornire le seguenti operazioni per il file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) creato dalla procedura guidata durante la migrazione:
   
   * Il **nuovo nome del database** 
   * L' **edizione del database SQL di Microsoft Azure** ([livello di servizio](sql-database-service-tiers.md))
   * Le **dimensioni massime del database**
   * L' **obiettivo del servizio** (livello di prestazioni)
   * Il **nome file temporaneo**  
   
   ![Esportazione impostazioni](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)
7. Completare la procedura guidata. A seconda delle dimensioni e della complessità del database la distribuzione può richiedere da pochi minuti a diverse ore. Se questa procedura guidata rileva i problemi di compatibilità, gli errori vengono visualizzati sullo schermo e la migrazione non continua. Per indicazioni su come risolvere i problemi di compatibilità del database, passare a [Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues.md).
8. Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.
9. Usando il portale di Azure visualizzare il database e le relative proprietà.

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


