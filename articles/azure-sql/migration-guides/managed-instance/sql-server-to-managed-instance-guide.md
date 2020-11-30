---
title: Guida alla migrazione da SQL Server a SQL Istanza gestita
description: Seguire questa guida per eseguire la migrazione dei database di SQL Server al Istanza gestita SQL di Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5d5404537ad107a54bd32110727e5a7d0f74ebea
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326897"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Guida alla migrazione: SQL Server a SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Questa guida consente di eseguire la migrazione dell'istanza di SQL Server al Istanza gestita SQL di Azure. 

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in: 

- SQL Server in macchine virtuali  
- Amazon Web Services (AWS) EC2 
- Servizio database relazionale di Amazon (AWS RDS) 
- Motore di calcolo (Google Cloud Platform-GCP)  
- SQL cloud per SQL Server (Google Cloud Platform-GCP) 

Per ulteriori informazioni sulla migrazione, vedere [Cenni preliminari sulla migrazione](sql-server-to-managed-instance-overview.md). Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Flusso del processo di migrazione":::

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione del SQL Server al Istanza gestita SQL di Azure, assicurarsi di esaminare i prerequisiti seguenti: 

- Scegliere un [metodo di migrazione](sql-server-to-managed-instance-overview.md#compare-migration-options) e gli strumenti corrispondenti necessari per il metodo scelto
- Installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) in un computer in grado di connettersi all'origine SQL Server


## <a name="pre-migration"></a>Pre-migrazione

Dopo aver verificato che l'ambiente di origine è supportato, iniziare con la fase di pre-migrazione. Individuare tutte le origini dati esistenti, valutare la fattibilità della migrazione e identificare eventuali problemi di blocco che potrebbero impedire la migrazione.  

### <a name="discover"></a>Rilevazione

Nella fase di individuazione analizzare la rete per identificare tutte le istanze e le funzionalità di SQL Server utilizzate dall'organizzazione. 

Usare [Azure migrate](../../../migrate/migrate-services-overview.md) per valutare l'idoneità della migrazione dei server locali, eseguire il dimensionamento in base alle prestazioni e fornire stime dei costi per l'esecuzione in Azure. 

In alternativa, utilizzare [Microsoft Assessment and Planning Toolkit (il "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) per valutare l'infrastruttura IT corrente. Il Toolkit fornisce un potente strumento di inventario, valutazione e creazione di report per semplificare il processo di pianificazione della migrazione. 

Per ulteriori informazioni sugli strumenti disponibili per la fase di individuazione, vedere la pagina relativa ai [Servizi e agli strumenti disponibili per gli scenari di migrazione dei dati](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Valutare 

Dopo aver individuato le origini dati, valutare eventuali istanze di SQL Server locali di cui è possibile eseguire la migrazione in Azure SQL Istanza gestita per identificare i blocchi di migrazione o i problemi di compatibilità. 

È possibile utilizzare il Data Migration Assistant (versione 4,1 e successive) per valutare i database da ottenere: 

- [Suggerimenti sulla destinazione di Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Consigli per SKU di Azure](/sql/dma/dma-sku-recommend-sql-db)

Per valutare l'ambiente mediante la valutazione della migrazione del database, attenersi alla procedura seguente: 

1. Aprire il [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selezionare **file** , quindi scegliere **nuova valutazione**. 
1. Specificare un nome di progetto, selezionare SQL Server come tipo di server di origine e quindi selezionare Istanza gestita SQL di Azure come tipo di server di destinazione. 
1. Selezionare i tipi di report di valutazione che si desidera generare. Ad esempio, compatibilità del database e parità di funzionalità. In base al tipo di valutazione, le autorizzazioni necessarie per la SQL Server di origine possono essere diverse.  DMA evidenzierà le autorizzazioni necessarie per l'advisor scelto prima di eseguire la valutazione.
    - La categoria di **parità delle funzionalità** offre un set completo di raccomandazioni, alternative disponibili in Azure e procedure di mitigazione per la pianificazione del progetto di migrazione. (autorizzazioni sysadmin obbligatorie)
    - La categoria **problemi di compatibilità** identifica i problemi di compatibilità delle funzionalità parzialmente supportati o non supportati che potrebbero bloccare la migrazione, nonché consigli per risolverli ( `CONNECT SQL` , `VIEW SERVER STATE` e le `VIEW ANY DEFINITION` autorizzazioni necessarie).
1. Specificare i dettagli della connessione di origine per il SQL Server e connettersi al database di origine.
1. Selezionare **Avvia valutazione**. 
1. Al termine del processo, selezionare ed esaminare i report di valutazione per i problemi di blocco della migrazione e di parità delle funzionalità. Il report di valutazione può anche essere esportato in un file che può essere condiviso con altri team o personale dell'organizzazione. 
1. Determinare il livello di compatibilità del database che riduce al minimo le attività post-migrazione.  
1. Identificare lo SKU di Azure SQL Istanza gestita migliore per il carico di lavoro locale. 

Per altre informazioni, vedere [eseguire una SQL Server assessment della migrazione con Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Se SQL Istanza gestita non è una destinazione adatta per il carico di lavoro, SQL Server in macchine virtuali di Azure potrebbe essere una destinazione alternativa valida per la propria azienda. 

#### <a name="scaled-assessments-and-analysis"></a>Valutazioni e analisi con scalabilità

Data Migration Assistant supporta l'esecuzione di valutazioni ridimensionate e il consolidamento dei report di valutazione per l'analisi. Se si dispone di più server e database che devono essere valutati e analizzati su larga scala per offrire una visualizzazione più ampia del patrimonio di dati, fare clic sui collegamenti seguenti per altre informazioni.

- [Esecuzione di valutazioni ridimensionate con PowerShell](/sql/dma/dma-consolidatereports)
- [Analisi dei report di valutazione con Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>L'esecuzione di valutazioni su larga scala per più database può essere automatizzata anche tramite l' [utilità della riga di comando DMA](/sql/dma/dma-commandline) , che consente anche di caricare i risultati in [Azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) per un'ulteriore analisi e la conformità alla destinazione.

### <a name="create-a-performance-baseline"></a>Creare una baseline per le prestazioni

Se è necessario confrontare le prestazioni del carico di lavoro in un Istanza gestita SQL con il carico di lavoro originale in esecuzione su SQL Server, creare una baseline delle prestazioni da usare per il confronto. Per altre informazioni, vedere [baseline delle prestazioni](sql-server-to-managed-instance-performance-baseline.md) . 

### <a name="create-sql-managed-instance"></a>Creare un'istanza gestita di SQL 

In base alle informazioni della fase di individuazione e valutazione, creare un Istanza gestita SQL di destinazione con dimensioni appropriate. È possibile farlo usando il [portale di Azure](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)o un [modello di Azure Resource Manager (ARM)](../../managed-instance/create-template-quickstart.md). 


## <a name="migrate"></a>Migrazione

Dopo aver completato le attività associate alla fase pre-migrazione, è possibile eseguire lo schema e la migrazione dei dati. 

Eseguire la migrazione dei dati usando il [metodo di migrazione](sql-server-to-managed-instance-overview.md#compare-migration-options)scelto. 

Questa guida descrive le due opzioni più diffuse: il servizio migrazione del database di Azure (DMS) e il backup e il ripristino nativi. 

### <a name="database-migration-service"></a>Servizio Migrazione del database

Per eseguire le migrazioni utilizzando DMS, attenersi alla procedura seguente:

1. Se questa operazione viene eseguita per la prima volta, registrare il provider di risorse **Microsoft. DataMigration** nella sottoscrizione.
1. Creare un'istanza del servizio migrazione del database di Azure in una posizione desiderata, preferibilmente nella stessa area del Istanza gestita SQL di Azure di destinazione, e selezionare una rete virtuale esistente o crearne una nuova per ospitare l'istanza di DMS.
1. Dopo aver creato l'istanza DMS, creare un nuovo progetto di migrazione e specificare il tipo di server di origine come **SQL Server** e il tipo di server di destinazione come **istanza gestita di database SQL di Azure**. Scegliere il tipo di attività nel pannello di creazione del progetto-migrazione dei dati online o offline. 
1.  Specificare i dettagli del SQL Server di origine nella pagina dei dettagli dell' **origine della migrazione** e nella pagina dei dettagli di Azure SQL istanza gestita nella pagina dei dettagli della **migrazione della destinazione** . Selezionare **Avanti**.
1. Scegliere il database di cui si desidera eseguire la migrazione. 
1. Fornire le impostazioni di configurazione per specificare la **condivisione di rete SMB** che contiene i file di backup del database. Usare le credenziali utente di Windows con DMS in grado di accedere alla condivisione di rete. Specificare i **Dettagli dell'account di archiviazione di Azure**. 
1. Esaminare il riepilogo della migrazione e scegliere **Esegui migrazione**. È quindi possibile monitorare l'attività di migrazione e verificare lo stato di avanzamento della migrazione del database.
1. Al termine del ripristino del database, scegliere **Avvia cutover**. Il processo di migrazione copia il backup della parte finale del log dopo averlo reso disponibile nella condivisione di rete SMB e ripristinarlo nella destinazione. 
1. Arrestare tutto il traffico in ingresso nel database di origine e aggiornare la stringa di connessione al nuovo database di Istanza gestita SQL di Azure. 

Per un'esercitazione dettagliata di questa opzione di migrazione, vedere [eseguire la migrazione di SQL Server a un istanza gestita SQL di Azure tramite DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Backup e ripristino 

Una delle funzionalità principali di Azure SQL Istanza gestita per consentire una migrazione di database rapida e semplice è il ripristino nativo dei file di backup del database ( `.bak` ) archiviati in [archiviazione di Azure](https://azure.microsoft.com/services/storage/). Il backup e il ripristino sono operazioni asincrone basate sulle dimensioni del database. 

Il diagramma seguente offre una panoramica di alto livello del processo:

![Il diagramma mostra SQL Server con una freccia con etichetta BACKUP/upload nell'URL che passa ad archiviazione di Azure e una seconda freccia con etichetta Ripristina da URL che passa da archiviazione di Azure a una Istanza gestita di SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Il tempo necessario per eseguire il backup, caricarlo in archiviazione di Azure ed eseguire un'operazione di ripristino nativa in Azure SQL Istanza gestita si basa sulle dimensioni del database. Fattorizzare un tempo di inattività sufficiente per gestire l'operazione per database di grandi dimensioni. 


Per eseguire la migrazione tramite backup e ripristino, attenersi alla procedura seguente: 

1. Eseguire il backup del database nell'archiviazione BLOB di Azure. Usare, ad esempio, [backup su URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Utilizzare lo [strumento Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) per supportare i database precedenti a SQL Server 2012 SP1 CU2. 
1. Connettersi al Istanza gestita SQL di Azure usando SQL Server Management Studio. 
1. Creare una credenziale usando una firma di accesso condiviso per accedere all'account di archiviazione BLOB di Azure con i backup del database. Ad esempio:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Ripristinare il backup dal contenitore BLOB di archiviazione di Azure. Ad esempio: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Al termine del ripristino, visualizzare il database in **Esplora oggetti** all'interno SQL Server Management Studio. 

Per altre informazioni su questa opzione di migrazione, vedere [ripristinare un database in Azure SQL istanza gestita con SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> L'operazione di ripristino del database è asincrona e ripetibile. È possibile che si verifichi un errore in SQL Server Management Studio se la connessione si interrompe o raggiunge il timeout. Il database SQL di Azure continuerà a tentare di ripristinare il database in background. È possibile tenere traccia dello stato di avanzamento del ripristino usando le visualizzazioni [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).



## <a name="data-sync-and-cutover"></a>Sincronizzazione dati e cutover

Quando si usano opzioni di migrazione che replicano/sincronizzano continuamente le modifiche dei dati dall'origine alla destinazione, i dati e lo schema di origine possono cambiare e derivare dalla destinazione. Durante la sincronizzazione dei dati, assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione durante il processo di migrazione. 

Dopo aver verificato che i dati sono gli stessi sia per l'origine che per la destinazione, è possibile cutover dall'origine all'ambiente di destinazione. È importante pianificare il processo cutover con i team aziendali o dell'applicazione per garantire che l'interruzione minima durante cutover non influisca sulla continuità aziendale. 

> [!IMPORTANT]
> Per informazioni dettagliate sui passaggi specifici associati all'esecuzione di un cutover come parte delle migrazioni con DMS, vedere [esecuzione della migrazione cutover](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Post-migrazione

Dopo aver completato la fase di migrazione, eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente e in modo efficiente. 

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza, nonché per risolvere i problemi di prestazioni del carico di lavoro. 

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per eseguire questa operazione, in alcuni casi è necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database prevede le attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

   > [!NOTE]
   > Per assistenza nello sviluppo e nell'esecuzione di test di convalida post-migrazione, prendere in considerazione la soluzione per la qualità dei dati offerta dal partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 



## <a name="leverage-advanced-features"></a>Sfruttare le funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte da SQL Istanza gestita, ad esempio la [disponibilità elevata incorporata](../../database/high-availability-sla.md), il rilevamento delle [minacce](../../database/azure-defender-for-sql.md)e il [monitoraggio e l'ottimizzazione del carico di lavoro](../../database/monitor-tune-overview.md). 

[Analisi SQL di Azure](../../../azure-monitor/insights/azure-sql.md) consente di monitorare un ampio set di istanze gestite in modo centralizzato.

Alcune funzionalità SQL Server sono disponibili solo quando il [livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) viene impostato sul livello di compatibilità più recente (150). 


## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni su Azure SQL Istanza gestita vedere:
   - [Livelli di servizio in Azure SQL Istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Differenze tra SQL Server e Azure SQL Istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).