---
title: Eseguire la migrazione al database SQL tramite la replica transazionale | Documentazione Microsoft
description: Database SQL di Microsoft Azure, migrazione del database, importazione del database, replica transazionale
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


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

## <a name="how-transactional-replication-works"></a>Funzionamento della replica transazionale

La replica transazionale è costituita da tre componenti principali: il server di pubblicazione, il distributore e il server di sottoscrizione. Insieme, questi tre componenti eseguono la replica. Il distributore è responsabile del controllo dei processi che consentono lo spostamento dei dati tra i server. Quando si configura la distribuzione, SQL creerà un database di distribuzione. Ogni server di pubblicazione deve essere associato a un database di distribuzione. Il database di distribuzione contiene i metadati per ogni pubblicazione associata e i dati sullo stato di avanzamento di ogni replica. Nel caso della replica transazionale, conterrà tutte le transazioni che devono essere eseguite nel server di sottoscrizione.

Il server di pubblicazione è il database in cui hanno origine tutti i dati per la migrazione. All'interno del server di pubblicazione possono essere presenti più pubblicazioni. Queste pubblicazioni contengono articoli mappati a tutte le tabelle e i dati che devono essere replicati. A seconda di come si definiscono la pubblicazione e gli articoli, è possibile replicare l'intero database o parte di esso. 

Nella replica il server di sottoscrizione è il server che riceve tutti i dati e le transazioni dalla pubblicazione. Ogni pubblicazione può avere più repliche.

## <a name="transactional-replication-requirements"></a>Requisiti della replica transazionale
[Fare clic su questo link per un elenco aggiornato dei requisiti.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Usare sempre la versione più aggiornata di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrazione al database SQL tramite il flusso di lavoro della replica transazionale

1. Configurare la distribuzione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Creare la pubblicazione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Creare la sottoscrizione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Suggerimenti e differenze per la migrazione al database SQL

1. Usare un server di distribuzione locale. 
   - Ciò causerà un impatto sulle prestazioni a livello del server. 
   - Se l'impatto sulle prestazioni non è accettabile, è possibile usare un altro server, ma aumenterà la complessità delle operazioni di gestione e amministrazione.
2. Quando si seleziona una cartella snapshot, accertarsi che la cartella selezionata sia sufficientemente grande da contenere un BCP di ogni tabella da replicare. 
3. Si noti che la creazione di snapshot bloccherà le tabelle associate fino al completamento dell'operazione; tenerlo presente durante la pianificazione dello snapshot. 
4. Nel database SQL di Azure sono supportate solo le sottoscrizioni push.
   - È possibile aggiungere solo server di sottoscrizione dal lato del database locale.

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Risorse aggiuntive
* Per altre informazioni sulla replica transazionale, vedere [Replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx).
* Per informazioni sul processo di migrazione generale e le relative opzioni, vedere [Migrazione di un database SQL Server al database SQL nel cloud](sql-database-cloud-migrate.md).



<!--HONumber=Dec16_HO2-->


