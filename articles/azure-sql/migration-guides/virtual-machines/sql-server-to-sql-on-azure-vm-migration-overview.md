---
title: SQL Server SQL Server nella macchina virtuale di Azure (panoramica della migrazione)
description: Informazioni sulle diverse strategie di migrazione quando si vuole eseguire la migrazione del SQL Server per SQL Server in macchine virtuali di Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 4979902853602073e6230ef7387d6c6596fe77da
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325920"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Panoramica della migrazione: SQL Server SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Informazioni sulle diverse strategie di migrazione per eseguire la migrazione dei SQL Server ai SQL Server in macchine virtuali di Azure. 

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in:

- SQL Server in macchine virtuali  
- Amazon Web Services (AWS) EC2 
- Servizio database relazionale di Amazon (AWS RDS) 
- Motore di calcolo (Google Cloud Platform-GCP)

Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Panoramica

Eseguire la migrazione a [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) quando si vuole usare l'ambiente familiare SQL Server con il controllo del sistema operativo e si desidera sfruttare le funzionalità fornite dal cloud, ad esempio la disponibilità elevata della VM incorporata, i [backup automatici](../../virtual-machines/windows/automated-backup.md)e l'applicazione [automatica delle patch](../../virtual-machines/windows/automated-patching.md). 

Puoi risparmiare sui costi grazie al modello di licenza [vantaggio Azure Hybrid](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) o Estendi il supporto per SQL Server 2008 e SQL Server 2008 R2 ottenendo [aggiornamenti della sicurezza gratuiti](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choosing-appropriate-target"></a>Scelta della destinazione appropriata

Le macchine virtuali di Azure vengono eseguite in molte aree diverse di Azure e offrono anche un'ampia gamma di [Opzioni di archiviazione](../../../virtual-machines/disks-types.md)e di dimensioni dei [computer](../../../virtual-machines/sizes.md) . Quando si determinano le dimensioni corrette della macchina virtuale e dello spazio di archiviazione per il carico di lavoro SQL Server, vedere le [linee guida sulle prestazioni per SQL Server in macchine virtuali di Azure.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) Per determinare le dimensioni della macchina virtuale e i requisiti di archiviazione per il carico di lavoro. si consiglia di ridimensionarle tramite una [valutazione Performance-Based Azure migrate](../../../migrate/concepts-assessment-calculation.md#types-of-assessments). Se questa opzione non è disponibile, vedere l'articolo seguente sulla creazione [di una baseline personalizzata per le prestazioni](https://azure.microsoft.com/services/virtual-machines/sql-server/).

È necessario considerare anche la corretta installazione e configurazione di SQL Server in una macchina virtuale. Si consiglia di usare la [raccolta di immagini di macchine virtuali di Azure SQL](../../virtual-machines/windows/create-sql-vm-portal.md) , in quanto consente di creare una SQL Server VM con la versione, l'edizione e il sistema operativo corretti. Questa operazione registrerà anche la macchina virtuale di Azure con il [provider di risorse](../../virtual-machines/windows/create-sql-vm-portal.md) SQL Server automaticamente, abilitando funzionalità come i backup automatizzati e l'applicazione automatica di patch.

## <a name="migration-strategies"></a>Strategie di migrazione

Esistono due strategie di migrazione per eseguire la migrazione dei database utente a un'istanza di SQL Server in macchine virtuali di Azure: **eseguire la migrazione** e il Lift- **and-Shift**. 

L'approccio appropriato per l'azienda dipende in genere dai fattori seguenti: 

- Dimensioni e scalabilità della migrazione
- Velocità di migrazione
- Supporto dell'applicazione per la modifica del codice
- È necessario modificare SQL Server versione, sistema operativo o entrambi.
- Ciclo di vita del supporto dei prodotti esistenti
- Finestra per il tempo di inattività dell'applicazione durante la migrazione

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="tempo di inattività della migrazione della macchina virtuale":::

Nella tabella seguente vengono descritte le differenze tra le due strategie di migrazione:
<br />

| **Strategia di migrazione** | **Descrizione** | **Quando usarla** |
| --- | --- | --- |
| **Lift & Shift** | Usare la strategia di migrazione Lift-and-Shift per spostare l'intero SQL Server fisico o virtuale dalla posizione corrente in un'istanza di SQL Server nella macchina virtuale di Azure senza apportare modifiche al sistema operativo o SQL Server versione. Per completare una migrazione in modalità Lift-and-Shift, vedere [Azure migrate](../../../migrate/migrate-services-overview.md). <br /><br /> Il server di origine rimane in linea e le richieste di servizi mentre il server di origine e quello di destinazione sincronizzano i dati consentendo una migrazione quasi senza problemi. | Usare per le migrazioni da singolo a scala molto elevata, anche applicabili a scenari come data center uscire. <br /><br /> Non sono necessarie modifiche del codice minime per le applicazioni o i database SQL, consentendo di eseguire più rapidamente migrazioni globali. <br /><br />Non sono necessari passaggi aggiuntivi per la migrazione dei servizi di business intelligence, ad esempio  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)e [SSAS](/analysis-services/analysis-services-overview). |
|**Migrazione** | Utilizzare una strategia di migrazione quando si desidera aggiornare la SQL Server di destinazione e/o la versione del sistema operativo. <br /> <br /> Selezionare una macchina virtuale di Azure da Azure Marketplace o un'immagine SQL Server preparata corrispondente alla versione del SQL Server di origine. | Usare quando è necessario o si desidera usare le funzionalità disponibili nelle versioni più recenti di SQL Server o se è necessario aggiornare le versioni legacy di SQL Server e/o del sistema operativo che non sono più supportate.  <br /> <br /> Potrebbe richiedere modifiche al database dell'applicazione o dell'utente per supportare l'aggiornamento SQL Server. <br /><br />Potrebbero essere necessarie considerazioni aggiuntive per la migrazione dei servizi di [Business Intelligence](#business-intelligence) se nell'ambito della migrazione. |


## <a name="lift-and-shift"></a>Modalità lift-and-shift  

La tabella seguente illustra in dettaglio il metodo disponibile per la strategia di migrazione **Lift-and-Shift** per eseguire la migrazione del database di SQL Server ai SQL Server nelle macchine virtuali di Azure:
<br />

|**Metodo** | **Versione di origine minima** | **Versione minima di destinazione** | **Vincolo dimensioni backup di origine** |  **Note** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) |  SQL Server esistenti da spostare così come sono nell'istanza di SQL Server in una macchina virtuale di Azure. Consente di ridimensionare i carichi di lavoro di migrazione fino a 35.000 VM. <br /><br /> I server di origine restano online e richiedono richieste durante la sincronizzazione dei dati del server, riducendo al minimo i tempi di inattività. <br /><br /> **Scripting & di automazione**: [Azure Site Recovery script](../../../migrate/how-to-migrate-at-scale.md) ed [esempio di migrazione e pianificazione con scalabilità per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrazione  

A causa della semplicità di installazione, l'approccio di migrazione consigliato consiste nell'eseguire un [backup](/sql/t-sql/statements/backup-transact-sql) nativo SQL Server localmente e quindi copiare il file in Azure. Questo metodo supporta database di dimensioni maggiori (>1 TB) per tutte le versioni di SQL Server a partire da 2008 e backup di database di dimensioni maggiori (>1 TB). Tuttavia, per i database a partire da SQL Server 2014, che hanno dimensioni inferiori a 1 TB e che dispongono di una connettività adeguata ad Azure, [SQL Server backup su URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) è l'approccio migliore. 

Quando si esegue la migrazione di SQL Server database a un'istanza di SQL Server in macchine virtuali di Azure, è importante scegliere un approccio adatto quando è necessario cutover al server di destinazione, in quanto ciò influiscono sull'intervallo di tempo di inattività dell'applicazione.

La tabella seguente illustra in dettaglio tutti i metodi disponibili per eseguire la migrazione del database di SQL Server ai SQL Server nelle macchine virtuali di Azure:
<br />

|**Metodo** | **Versione di origine minima** | **Versione minima di destinazione** | **Vincolo dimensioni backup di origine** | **Note** |
| --- | --- | --- | --- | --- |
| **[Eseguire il backup in un file](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) |  Si tratta di una tecnica semplice e ben collaudata per lo stato di trasferimento dei database tra computer. Utilizzare la compressione per ridurre al minimo le dimensioni del backup per il trasferimento. <br /><br /> **Automazione & scripting**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) e [AzCopy nell'archivio BLOB](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Backup nell'URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB per SQL Server 2016, in caso contrario 1 TB | Un modo alternativo per spostare il file di backup nella macchina virtuale usando archiviazione di Azure. Utilizzare la compressione per ridurre al minimo le dimensioni del backup per il trasferimento. <br /><br /> **Scripting & di automazione**:  [T-SQL o piano di manutenzione](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Database Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) |  Il [DMA](/sql/dma/dma-overview) valuta SQL Server in locale e quindi Aggiorna facilmente le versioni successive di SQL Server o esegue la migrazione a SQL Server in macchine virtuali di Azure, nel database SQL di Azure o in azure SQL istanza gestita. <br /><br /> Non deve essere utilizzato nei database utente abilitati per FILESTREAM.<br /><br /> DMA include inoltre la possibilità di eseguire la migrazione di [account di accesso SQL e Windows](/sql/dma/dma-migrateserverlogins) e di valutare i [pacchetti SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Automazione & script**: [interfaccia della riga di comando](/sql/dma/dma-commandline) |
| **[Scollegamento e collegamento](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) | Usare questo metodo quando si prevede di [archiviare questi file usando il servizio di archiviazione BLOB di Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) e di collegarli a un'istanza di SQL Server in una macchina virtuale di Azure, particolarmente utile con database di dimensioni molto grandi o quando il tempo per il backup e il ripristino è troppo lungo. <br /><br /> **Automazione & scripting**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) e [AzCopy nell'archivio BLOB](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Log shipping](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (solo Windows) | SQL Server 2008 SP4 (solo Windows) | [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) | Il log shipping replica i file di log transazionali dall'ambiente locale a un'istanza di SQL Server in una macchina virtuale di Azure. <br /><br /> Ciò consente un tempo di inattività minimo durante il failover e un sovraccarico di configurazione inferiore rispetto alla configurazione di un gruppo di disponibilità Always On. <br /><br /> **Scripting & di automazione**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Gruppo di disponibilità distribuito](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limite di archiviazione della macchina virtuale di Azure](../../../index.yml) |  Un [gruppo di disponibilità distribuito](/sql/database-engine/availability-groups/windows/distributed-availability-groups) è un tipo speciale di gruppo di disponibilità che si estende su due gruppi di disponibilità distinti. Non è necessario che i gruppi di disponibilità che fanno parte di un gruppo di disponibilità distribuito si trovino nella stessa posizione e includano il supporto tra domini. <br /><br /> Questo metodo consente di ridurre al minimo i tempi di inattività, da usare quando si dispone di un gruppo di disponibilità configurato in locale. <br /><br /> **Scripting & di automazione**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Per i trasferimenti di dati di grandi dimensioni senza opzioni di rete limitate, vedere [trasferimenti di dati di grandi dimensioni con connettività limitata](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Considerazioni

Di seguito è riportato un elenco di punti chiave da considerare quando si esaminano i metodi di migrazione:

- Per ottimizzare le prestazioni di trasferimento dei dati, eseguire la migrazione di database e file in un'istanza di SQL Server in una VM di Azure usando un file di backup compresso. Per i database di dimensioni maggiori, oltre alla compressione, [suddividere il file di backup in file più piccoli](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) per migliorare le prestazioni durante il backup e il trasferimento. 
- Se si esegue la migrazione da SQL Server 2014 o versione successiva, è consigliabile [crittografare i backup](/sql/relational-databases/backup-restore/backup-encryption) per proteggere i dati durante il trasferimento in rete.
- Per ridurre al minimo i tempi di inattività durante la migrazione del database, utilizzare l'opzione Always On gruppo di disponibilità. 
- Per ridurre al minimo i tempi di inattività senza il sovraccarico dovuto alla configurazione di un gruppo di disponibilità, utilizzare l'opzione log shipping. 
- Per limitare le opzioni di rete, usare i metodi di migrazione offline, ad esempio backup e ripristino, o i [servizi di trasferimento su disco](../../../storage/common/storage-solution-large-dataset-low-network.md) disponibili in Azure.
- Per modificare anche la versione di SQL Server in un SQL Server nella macchina virtuale di Azure, vedere [change SQL Server Edition](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Potrebbero essere necessarie considerazioni aggiuntive per la migrazione di SQL Server servizi di business intelligence al di fuori dell'ambito delle migrazioni del database utente. 

Tali servizi includono:

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Versioni supportate

Quando si prepara la migrazione di database SQL Server a SQL Server in macchine virtuali di Azure, assicurarsi di prendere in considerazione le versioni di SQL Server supportate. Per un elenco delle versioni correnti SQL Server supportate nelle macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).


## <a name="next-steps"></a>Passaggi successivi

Per iniziare a migrare i database di SQL Server SQL Server nelle macchine virtuali di Azure, vedere la [Guida alla migrazione dei singoli database](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere l'articolo [servizio e strumenti per la migrazione dei dati.](../../../dms/dms-tools-matrix.md)

- Per ulteriori informazioni su SQL Azure, vedere:
   - [Opzioni di distribuzione](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per informazioni sulle licenze, vedere
   - [Bring Your Own License con il Vantaggio Azure Hybrid](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Ottieni supporto esteso gratuito per SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).