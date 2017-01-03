---
title: Eseguire la migrazione al database SQL tramite la replica transazionale | Documentazione Microsoft
description: Database SQL di Microsoft Azure, migrazione del database, importazione del database, replica transazionale
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 891c10b2f8e560a3c97f93198c742f657a92e927


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Eseguire la migrazione del database di SQL Server al database SQL di Azure tramite la replica transazionale
> [!div class="op_single_selector"]
> * [Migrazione guidata in SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Esportare in un file BACPAC.](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importare da file BACPAC.](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Questo articolo illustra come eseguire la migrazione di un database di SQL Server compatibile al database SQL di Azure riducendo al minimo il tempo di inattività grazie alla replica transazionale di SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Comprendere l'architettura della replica transazionale
Quando non è possibile rimuovere il database SQL Server dalla produzione durante la migrazione, è possibile usare la replica transazionale di SQL Server come soluzione di migrazione. Per usare questa soluzione, è necessario configurare il database SQL di Azure come sottoscrittore dell'istanza di SQL Server locale di cui si desidera eseguire la migrazione. Il server di distribuzione locale della replica transazionale sincronizza i dati dal database locale da sincronizzare, ovvero il server di pubblicazione, mentre continua l'esecuzione di transazioni. 

È inoltre possibile usare la replica transazionale per eseguire la migrazione di un subset del database locale. La pubblicazione di cui si esegue la replica nel database SQL di Azure può essere limitata a un subset delle tabelle nel database replicato. Per ogni tabella replicata, è possibile limitare i dati a un subset di righe e/o di colonne.

Con la replica transazionale, tutte le modifiche ai dati o allo schema vengono visualizzate nel database SQL di Azure. Quando la sincronizzazione è completata e si è pronti a eseguire la migrazione, è sufficiente modificare la stringa di connessione delle applicazioni in modo che puntino al database SQL di Azure. Una volta che la replica transazionale completa le eventuali modifiche rimaste nel database locale e tutte le applicazioni puntano al database di Azure, è possibile disinstallare la replica transazionale. Il database SQL di Azure è ora il sistema di produzione.

 ![Diagramma di SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisiti della replica transazionale
La replica transazionale è una tecnologia integrata in SQL Server a partire dalla versione 6.5. Si tratta di una tecnologia sperimentata e consolidata che la maggior parte degli amministratori di database conosce e con cui ha esperienza. Con [SQL Server 2016](https://www.microsoft.com/sql-server/sql-server-2016) è ora possibile configurare il database SQL di Azure come [sottoscrittore di replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx) per la pubblicazione locale. L'esperienza che si ottiene impostando il database da Management Studio è identica alla configurazione di un sottoscrittore di replica transazionale in un server locale. Questo scenario è supportato quando i server di pubblicazione e di distribuzione sono almeno una delle seguenti versioni di SQL Server:

* SQL Server 2016 e versioni successive 
* SQL Server 2014 SP1 CU3 e versioni successive
* SQL Server 2014 RTM CU10 e versioni successive
* SQL Server 2012 SP2 CU8 e versioni successive
* SQL Server 2012 SP3 e versioni successive

> [!IMPORTANT]
> Usare sempre la versione più aggiornata di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx)
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


