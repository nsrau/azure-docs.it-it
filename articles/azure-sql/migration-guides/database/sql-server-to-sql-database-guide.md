---
title: Guida alla migrazione SQL Server al database SQL
description: Seguire questa guida per eseguire la migrazione dei database di SQL Server al database SQL di Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 55ce3747aaf105c7e2cbb830b1175769a658fd72
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496764"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>Guida alla migrazione: SQL Server al database SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Questa guida consente di eseguire la migrazione dell'istanza di SQL Server al database SQL di Azure. 

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in: 

- SQL Server in macchine virtuali  
- Amazon Web Services (AWS) EC2 
- Servizio database relazionale di Amazon (AWS RDS) 
- Motore di calcolo (Google Cloud Platform-GCP)  
- SQL cloud per SQL Server (Google Cloud Platform-GCP) 

Per ulteriori informazioni sulla migrazione, vedere [Cenni preliminari sulla migrazione](sql-server-to-sql-database-overview.md). Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Flusso del processo di migrazione":::

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione del SQL Server al database SQL di Azure, verificare che siano soddisfatti i prerequisiti seguenti: 

- Metodo di [migrazione](sql-server-to-sql-database-overview.md#compare-migration-options) scelto e strumenti corrispondenti 
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installato in un computer in grado di connettersi all'origine SQL Server
- Un [database SQL di Azure](../../database/single-database-create-quickstart.md) di destinazione


## <a name="pre-migration"></a>Pre-migrazione

Dopo aver verificato che l'ambiente di origine è supportato, iniziare con la fase di pre-migrazione. Individuare tutte le origini dati esistenti, valutare la fattibilità della migrazione e identificare eventuali problemi di blocco che potrebbero impedire la migrazione. 

### <a name="discover"></a>Rilevazione

Nella fase di individuazione analizzare la rete per identificare tutte le istanze e le funzionalità di SQL Server utilizzate dall'organizzazione. 

Usare [Azure migrate](../../../migrate/migrate-services-overview.md) per valutare l'idoneità della migrazione dei server locali, eseguire il dimensionamento in base alle prestazioni e fornire stime dei costi per l'esecuzione in Azure. 

In alternativa, utilizzare [Microsoft Assessment and Planning Toolkit (il "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) per valutare l'infrastruttura IT corrente. Il Toolkit fornisce un potente strumento di inventario, valutazione e creazione di report per semplificare il processo di pianificazione della migrazione. 

Per ulteriori informazioni sugli strumenti disponibili per la fase di individuazione, vedere la pagina relativa ai [Servizi e agli strumenti disponibili per gli scenari di migrazione dei dati](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Valutare 

Dopo aver individuato le origini dati, valutare eventuali database SQL Server locali di cui è possibile eseguire la migrazione nel database SQL di Azure per identificare i blocchi di migrazione o i problemi di compatibilità. 

È possibile utilizzare il Data Migration Assistant (versione 4,1 e successive) per valutare i database da ottenere: 

- [Suggerimenti sulla destinazione di Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Consigli per SKU di Azure](/sql/dma/dma-sku-recommend-sql-db)

Per valutare l'ambiente mediante la valutazione della migrazione del database, attenersi alla procedura seguente: 

1. Aprire il [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selezionare **file** , quindi scegliere **nuova valutazione**. 
1. Specificare un nome di progetto, selezionare SQL Server come tipo di server di origine e quindi selezionare database SQL di Azure come tipo di server di destinazione. 
1. Selezionare i tipi di report di valutazione che si desidera generare. Ad esempio, compatibilità del database e parità di funzionalità. In base al tipo di valutazione, le autorizzazioni necessarie per la SQL Server di origine possono essere diverse.  DMA evidenzierà le autorizzazioni necessarie per l'advisor scelto prima di eseguire la valutazione.
    - La categoria di **parità delle funzionalità** offre un set completo di raccomandazioni, alternative disponibili in Azure e procedure di mitigazione per la pianificazione del progetto di migrazione. (autorizzazioni sysadmin obbligatorie)
    - La categoria **problemi di compatibilità** identifica i problemi di compatibilità delle funzionalità parzialmente supportati o non supportati che potrebbero bloccare la migrazione, nonché consigli per risolverli ( `CONNECT SQL` , `VIEW SERVER STATE` e le `VIEW ANY DEFINITION` autorizzazioni necessarie).
1. Specificare i dettagli della connessione di origine per il SQL Server e connettersi al database di origine.
1. Selezionare **Avvia valutazione**. 
1. Al termine del processo, selezionare ed esaminare i report di valutazione per i problemi di blocco della migrazione e di parità delle funzionalità. Il report di valutazione può anche essere esportato in un file che può essere condiviso con altri team o personale dell'organizzazione. 
1. Determinare il livello di compatibilità del database che riduce al minimo le attività post-migrazione.  
1. Identificare il migliore SKU del database SQL di Azure per il carico di lavoro locale. 

Per altre informazioni, vedere [eseguire una SQL Server assessment della migrazione con Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Se la valutazione rileva più blocchi per confermare che il database non è pronto per la migrazione di un database SQL di Azure, in alternativa è opportuno prendere in considerazione quanto segue:

- [Azure SQL istanza gestita](../managed-instance/sql-server-to-managed-instance-overview.md) se sono presenti più dipendenze con ambito istanza
- [SQL Server in macchine virtuali di Azure se il](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) database SQL e SQL istanza gestita non sono idonei per la destinazione. 



#### <a name="scaled-assessments-and-analysis"></a>Valutazioni e analisi con scalabilità
Data Migration Assistant supporta l'esecuzione di valutazioni ridimensionate e il consolidamento dei report di valutazione per l'analisi. 

Se si dispone di più server e database che devono essere valutati e analizzati su larga scala per offrire una visualizzazione più ampia del patrimonio di dati, vedere i collegamenti seguenti per altre informazioni:

- [Esecuzione di valutazioni ridimensionate con PowerShell](/sql/dma/dma-consolidatereports)
- [Analisi dei report di valutazione con Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> L'esecuzione di valutazioni su larga scala per più database, in particolare quelli di grandi dimensioni, può anche essere automatizzata tramite l' [utilità della riga di comando DMA](/sql/dma/dma-commandline) e caricata in [Azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) per un'ulteriore analisi e una maggiore disponibilità per la destinazione.

## <a name="migrate"></a>Migrate

Dopo aver completato le attività associate alla fase pre-migrazione, è possibile eseguire lo schema e la migrazione dei dati. 

Eseguire la migrazione dei dati usando il [metodo di migrazione](sql-server-to-sql-database-overview.md#compare-migration-options)scelto. 

Questa guida descrive le due opzioni più diffuse: Data Migration Assistant e il servizio migrazione del database di Azure. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Per eseguire la migrazione di un database da SQL Server al database SQL di Azure con DMA, seguire questa procedura: 

1. Scaricare e installare il [Database Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
1. Creare un nuovo progetto e selezionare **migrazione** come tipo di progetto.
1. Impostare il tipo di server di origine su **SQL Server** e il tipo di server di destinazione sul **database SQL di Azure** , selezionare l'ambito della migrazione come **schema e dati** e selezionare **Crea**.
1. Nel progetto di migrazione specificare i dettagli del server di origine, ad esempio il nome del server, le credenziali per la connessione al server e il database di origine di cui eseguire la migrazione.
1. Nei dettagli del server di destinazione specificare il nome del server del database SQL di Azure, le credenziali per la connessione al server e il database di destinazione per la migrazione.
1. Selezionare gli oggetti dello schema e distribuirli nel database SQL di Azure di destinazione.
1. Infine, selezionare **Avvia migrazione dati** e monitorare lo stato della migrazione.

Per un'esercitazione dettagliata, vedere [eseguire la migrazione di SQL Server locali o SQL Server in macchine virtuali di Azure al database SQL di Azure tramite il Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Ridimensionare il database a un livello di servizio e una dimensione di calcolo superiori durante il processo di importazione per ottimizzare la velocità di importazione fornendo più risorse. Al termine dell'importazione, sarà quindi possibile ridurre le caratteristiche.</br>
> - Il livello di compatibilità del database importato è basato sul livello di compatibilità del database di origine. 


### <a name="azure-database-migration-service-dms"></a>Servizio Migrazione del database di Azure

Per eseguire la migrazione dei database da SQL Server al database SQL di Azure tramite DMS, attenersi alla procedura seguente:

1. Se non è già stato fatto, registrare il provider di risorse **Microsoft. DataMigration** nella sottoscrizione. 
1. Creare un'istanza del servizio migrazione del database di Azure nella posizione desiderata preferita (preferibilmente nella stessa area del database SQL di Azure di destinazione). Selezionare una rete virtuale esistente o crearne una nuova per ospitare l'istanza di DMS.
1. Dopo aver creato l'istanza DMS, creare un nuovo progetto di migrazione e specificare il tipo di server di origine come **SQL Server** e il tipo di server di destinazione come **database SQL di Azure**. Scegliere **migrazione dati offline** come tipo di attività nel pannello di creazione del progetto di migrazione.
1. Specificare i dettagli del SQL Server di origine nella pagina Dettagli **origine migrazione** e i dettagli del database SQL di Azure di destinazione nella pagina Dettagli **destinazione migrazione** .
1. Eseguire il mapping dei database di origine e di destinazione per la migrazione e quindi selezionare le tabelle di cui si desidera eseguire la migrazione.
1. Esaminare il riepilogo della migrazione e selezionare **Esegui migrazione**. È quindi possibile monitorare l'attività di migrazione e verificare lo stato di avanzamento della migrazione del database.

Per un'esercitazione dettagliata, vedere [eseguire la migrazione di SQL Server a un database SQL di Azure con DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Sincronizzazione dati e cutover

Quando si usano opzioni di migrazione che replicano/sincronizzano continuamente le modifiche dei dati dall'origine alla destinazione, i dati e lo schema di origine possono cambiare e derivare dalla destinazione. Durante la sincronizzazione dei dati, assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione durante il processo di migrazione. 

Dopo aver verificato che i dati sono uguali sia nell'origine che nella destinazione, è possibile cutover dall'origine all'ambiente di destinazione. È importante pianificare il processo cutover con i team aziendali o dell'applicazione per garantire che l'interruzione minima durante cutover non influisca sulla continuità aziendale. 

> [!IMPORTANT]
> Per informazioni dettagliate sui passaggi specifici associati all'esecuzione di un cutover come parte delle migrazioni con DMS, vedere [esecuzione della migrazione cutover](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover).


## <a name="post-migration"></a>Post-migrazione

Dopo aver completato la fase di migrazione, eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente e in modo efficiente. 

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza, nonché per risolvere i problemi di prestazioni del carico di lavoro. 

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per eseguire questa operazione, in alcuni casi è necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database prevede le attività seguenti:

1. **Sviluppare i test di convalida** : per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare un ambiente di test** : l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida** : eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni** : eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

   > [!NOTE]
   > Per assistenza nello sviluppo e nell'esecuzione di test di convalida post-migrazione, prendere in considerazione la soluzione per la qualità dei dati offerta dal partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Sfruttare le funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte dal database SQL, ad esempio la [disponibilità elevata incorporata](../../database/high-availability-sla.md), il rilevamento delle [minacce](../../database/advanced-data-security.md)e il [monitoraggio e l'ottimizzazione del carico di lavoro](../../database/monitor-tune-overview.md). 

Alcune funzionalità SQL Server sono disponibili solo quando il [livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) viene impostato sul livello di compatibilità più recente (150). 

Per altre informazioni, vedere [gestione del database SQL di Azure dopo la migrazione](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul database SQL, vedere:
    - [Panoramica del database SQL di Azure](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
