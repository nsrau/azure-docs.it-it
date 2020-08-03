---
title: Domande frequenti sull'iperscalabilità del database SQL di Azure
description: Risposte alle domande comuni poste dai clienti su un database nel database SQL nel livello di servizio con iperscalabilità, comunemente denominato database con iperscalabilità.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 359de25d2bdb57ad5c6386586f987942acc120ef
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500147"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Domande frequenti sull'iperscalabilità del database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce le risposte alle domande frequenti per i clienti che considerano un database nel livello di servizio iperscalabile del database SQL di Azure, definito semplicemente iperscalabile nel resto di queste domande frequenti. Questo articolo descrive gli scenari supportati da iperscalabilità e le funzionalità compatibili con l'iperscalabilità.

- Queste domande frequenti sono destinate a coloro che hanno una conoscenza superficiale del livello di servizio Hyperscale e necessitano di risposte a domande e preoccupazioni specifiche.
- Queste domande frequenti non sono destinate a essere una guida o a rispondere a domande su come usare un database con iperscalabilità. Per un'introduzione a iperscalabilità, è consigliabile fare riferimento alla documentazione sull' [iperscalabilità del database SQL di Azure](service-tier-hyperscale.md) .

## <a name="general-questions"></a>Domande generali

### <a name="what-is-a-hyperscale-database"></a>Cosa è un database Hyperscale

Un database con iperscalabilità è un database nel database SQL nel livello di servizio con scalabilità orizzontale supportato dalla tecnologia di archiviazione con scalabilità orizzontale di iperscalabilità. Un database Hyperscale supporta fino a 100 TB di dati e offre velocità effettiva e prestazioni elevate, oltre che velocità di ridimensionamento per adattarsi ai requisiti dei carichi di lavoro. La scalabilità è trasparente per l'applicazione: connettività, elaborazione di query e così via. funziona come qualsiasi altro database nel database SQL di Azure.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quali tipi di risorse e modelli di acquisto sono supportati dal livello Hyperscale

Il livello di servizio Hyperscale è disponibile solo per i database singoli che usano il modello di acquisto basato su vCore nel database SQL di Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Quali sono le differenze tra il livello di servizio Hyperscale e i livelli di servizio per utilizzo generico e business critical

I livelli di servizio basati su vCore sono differenziati in base alla disponibilità del database e al tipo di archiviazione, alle prestazioni e alle dimensioni massime, come descritto nella tabella seguente.

| | Tipo di risorsa | Utilizzo generico |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** |Tutti|Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti.|La maggior parte dei carichi di lavoro aziendali. Ridimensionamento automatico delle dimensioni di archiviazione fino a 100 TB, scalabilità verticale e orizzontale rapida del calcolo, ripristino rapido del database.|Applicazioni OLTP con frequenza di transazione elevata e bassa latenza di i/o. Offre la massima resilienza agli errori e a failover rapidi usando più repliche aggiornate in modo sincrono.|
|  **Tipo di risorsa** ||Database SQL/SQL Istanza gestita | Database singolo | Database SQL/SQL Istanza gestita |
| **Dimensioni di calcolo**|Database SQL * | Da 1 a 80 vCore | Da 1 a 80 vCore* | Da 1 a 80 vCore |
| **Dimensioni di calcolo**|Istanza gestita di SQL | 8, 16, 24, 32, 40, 64, 80 vCore | N/D | 8, 16, 24, 32, 40, 64, 80 vCore |
| **Tipo di archiviazione** | Tutti |Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni dello spazio di archiviazione** | Database SQL *| 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| **Dimensioni dello spazio di archiviazione** | Istanza gestita di SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **IOPS** | Database singolo | 500 operazioni di I/O al secondo per vCore fino a un massimo di 7000 | La funzionalità iperscalabile è un'architettura a più livelli con memorizzazione nella cache a più livelli. Gli IOPS effettivi dipendono dal carico di lavoro. | 5000 operazioni di I/O al secondo fino a un massimo di 200.000|
| **IOPS** | Istanza gestita di SQL | Dipende dalle dimensioni del file | N/D | 1375 IOPS/vCore |
|**Disponibilità**|Tutti|1 replica, nessuna scalabilità in lettura, nessuna cache locale | Più repliche, fino a 4 scalabilità in lettura, cache locale parziale | 3 repliche, 1 scalabilità in lettura, disponibilità elevata con ridondanza della zona, archiviazione locale completa |
|**Backup**|Tutti|RA-GRS, conservazione di 7-35 giorni (7 giorni per impostazione predefinita)| RA-GRS, conservazione di 7 giorni, ripristino temporizzato temporizzato costante (ripristino temporizzato) | RA-GRS, conservazione di 7-35 giorni (7 giorni per impostazione predefinita) |

\*I pool elastici non sono supportati nel livello di servizio con iperscalabilità

### <a name="who-should-use-the-hyperscale-service-tier"></a>Chi deve usare il livello di servizio Hyperscale

Il livello di servizio con iperscalabilità è destinato ai clienti che hanno database SQL Server locali di grandi dimensioni e vogliono modernizzare le applicazioni passando al cloud o per i clienti che usano già il database SQL di Azure e vogliono ampliare significativamente il rischio di crescita del database. Il livello Hyperscale è destinato anche ai clienti che sono alla ricerca di livelli elevati di prestazioni e scalabilità. Il livello Hyperscale offre:

- Dimensioni database fino a 100 TB
- Backup veloci del database indipendentemente dalle dimensioni del database (i backup sono basati su snapshot di archiviazione)
- Ripristini di database rapidi indipendentemente dalle dimensioni del database (i ripristini sono da snapshot di archiviazione)
- Maggiore velocità effettiva dei log indipendentemente dalle dimensioni del database e dal numero di vcore
- Scalabilità in lettura con una o più repliche di sola lettura, usate per l'offload delle letture e come hot standby.
- Rapido aumento delle risorse di calcolo, in un tempo costante, per disporre di una maggiore potenza per un carico di lavoro impegnativo e quindi riduzione delle risorse in un tempo costante. Questa operazione è simile alla scalabilità verso l'alto e verso il basso tra un P6 e un P11, ad esempio, ma molto più veloce, perché non si tratta di un'operazione di dimensione dati.

### <a name="what-regions-currently-support-hyperscale"></a>Quali aree supportano attualmente il livello Hyperscale

Il livello di servizio iperscalabile è attualmente disponibile nelle aree elencate nella [Panoramica sull'iperscalabilità del database SQL di Azure](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>È possibile creare più database con iperscalabilità per server

Sì. Per ulteriori informazioni e limiti sul numero di database con iperscalabilità per server, vedere [limiti delle risorse del database SQL per database singoli e in pool in un server](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quali sono le caratteristiche di prestazioni di un database con iperscalabilità

L'architettura con iperscalabilità fornisce prestazioni e velocità effettiva elevate, supportando al contempo grandi dimensioni di database

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual è la scalabilità di un database Hyperscale

L'iperscalabilità offre una scalabilità rapida in base alla domanda del carico di lavoro.

- **Scalabilità verticale**

  Con l'iperscalabilità, è possibile aumentare le dimensioni di calcolo primarie in termini di risorse come CPU e memoria e quindi ridurle in tempo costante. Poiché l'archiviazione è condivisa, la scalabilità verticale non è un'operazione di ridimensionamento dei dati.  
- **Scalabilità orizzontale**

  Con l'iperscalabilità, è anche possibile eseguire il provisioning di una o più repliche di calcolo aggiuntive che è possibile usare per soddisfare le richieste di lettura. Ciò significa che è possibile usare le repliche di calcolo aggiuntive come repliche di sola lettura per eseguire l'offload del carico di lavoro di lettura dal calcolo primario. Oltre a essere di sola lettura, queste repliche sono utilizzate anche come hot-standby in caso di failover dal database primario.

  Il provisioning di ognuna di queste repliche di calcolo aggiuntive può essere eseguito in tempo costante ed è un'operazione online. È possibile connettersi a queste repliche di calcolo di sola lettura aggiuntive impostando l' `ApplicationIntent` argomento nella stringa di connessione su `ReadOnly` . Tutte le connessioni con `ReadOnly` finalità dell'applicazione vengono indirizzate automaticamente a una delle repliche di calcolo aggiuntive di sola lettura.

## <a name="deep-dive-questions"></a>Domande di approfondimento

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>È possibile combinare iperscalare e database singoli in un unico server

Sì, è possibile.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Il livello Hyperscale richiede la modifica del modello di programmazione dell'applicazione

No, il modello di programmazione dell'applicazione rimane invariato. Usare la stringa di connessione come di consueto e gli altri modi normali per interagire con il database iperscalabile.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Quale livello di isolamento delle transazioni è l'impostazione predefinita in un database con iperscalabilità

Nella replica primaria, il livello di isolamento della transazione predefinito è RCSI (Read Committed Snapshot Isolation). Nelle repliche secondarie di lettura con scalabilità orizzontale, il livello di isolamento predefinito è snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>È possibile portare la licenza locale o IaaS SQL Server per l'iperscalabilità

Sì. Il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) è disponibile per il livello Hyperscale. Ogni core SQL Server Standard può essere mappato a 1 vCore Hyperscale. Ogni core SQL Server Enterprise può essere mappato a 4 vCore Hyperscale. Non è necessaria una licenza SQL per le repliche secondarie. Il prezzo Vantaggio Azure Hybrid verrà applicato automaticamente per le repliche con scalabilità orizzontale (secondaria).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Quale tipo di carichi di lavoro è progettato per l'iperscalabilità

L'iperscalabilità supporta tutti i carichi di lavoro SQL Server, ma è ottimizzato principalmente per OLTP. È possibile importare anche carichi di lavoro ibridi (HTAP) e analitici (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Come è possibile scegliere tra Azure SQL Data Warehouse e l'iperscalabilità del database SQL di Azure

Se si eseguono query di analisi interattive usando SQL Server come data warehouse, l'iperscalabilità è un'ottima opzione perché è possibile ospitare data warehouse di piccole e medie dimensioni, ad esempio pochi TB fino a 100 TB, a un costo inferiore ed è possibile eseguire la migrazione dei carichi di lavoro di data warehouse di SQL Server a iperscalabilità con modifiche minime al codice T-SQL.

Se si esegue l'analisi dei dati su larga scala con query complesse e frequenze di inserimento prolungate superiori a 100 MB/s oppure si utilizzano Parallel data warehouse (PDW), Teradata o altri data warehouse di elaborazione parallela massiva (MPP), SQL Data Warehouse può essere la scelta migliore.
  
## <a name="hyperscale-compute-questions"></a>Domande di calcolo con iperscalabilità

### <a name="can-i-pause-my-compute-at-any-time"></a>È possibile sospendere il calcolo in qualsiasi momento

Al momento, tuttavia, è possibile ridimensionare il calcolo e il numero di repliche per ridurre i costi durante le ore non di punta.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>È possibile eseguire il provisioning di una replica di calcolo con RAM aggiuntiva per il carico

No. Per ottenere più RAM, è necessario eseguire l'aggiornamento a dimensioni di calcolo superiori. Per altre informazioni, vedere [Dimensioni di calcolo e archiviazione del livello Hyperscale](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>È possibile eseguire il provisioning di più repliche di calcolo di dimensioni diverse

No.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Il numero di repliche con scalabilità orizzontale in lettura supportate

Per impostazione predefinita, i database con iperscalabilità vengono creati con una replica con scalabilità orizzontale in lettura (due repliche, inclusa quella primaria). È possibile ridimensionare il numero di repliche di sola lettura tra 0 e 4 usando [portale di Azure](https://portal.azure.com) o l' [API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Per la disponibilità elevata, è necessario eseguire il provisioning di repliche di calcolo aggiuntive

Nei database con iperscalabilità, la resilienza dei dati viene fornita a livello di archiviazione. È necessaria una sola replica per garantire la resilienza. Quando la replica di calcolo non è attiva, viene creata automaticamente una nuova replica senza alcuna perdita di dati.

Se tuttavia c'è una sola replica, potrebbe essere necessario un po' di tempo per creare la cache locale nella nuova replica dopo il failover. Durante la fase di ricompilazione della cache, il database recupera i dati direttamente dai server della pagina, comportando una latenza di archiviazione superiore e prestazioni delle query ridotte.

Per le app cruciali che richiedono una disponibilità elevata con un failover minimo, è necessario effettuare il provisioning di almeno 2 repliche di calcolo, inclusa la replica primaria di calcolo. Questa è la configurazione predefinita. In questo modo, è disponibile una replica hot standby che funge da destinazione del failover.

## <a name="data-size-and-storage-questions"></a>Domande sull'archiviazione e sulle dimensioni dei dati

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Quali sono le dimensioni massime del database supportate con iperscalabilità

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Quali sono le dimensioni del log delle transazioni con il livello Hyperscale

Il log delle transazioni con il livello Hyperscale è praticamente infinito. Non è necessario preoccuparsi di esaurire lo spazio di log in un sistema con velocità effettiva di log elevata. Tuttavia, la velocità di generazione del log potrebbe essere limitata per i carichi di lavoro di scrittura aggressivi continui. La velocità massima di generazione dei log è 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Aumenta la `tempdb` scalabilità del database

Il `tempdb` database si trova nell'archivio SSD locale ed è dimensionato proporzionalmente alle dimensioni di calcolo di cui si esegue il provisioning. Il `tempdb` è ottimizzato per offrire i massimi vantaggi a livello di prestazioni. `tempdb`la dimensione non è configurabile e viene gestita per l'utente.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Le dimensioni del database aumentano automaticamente o è necessario gestire le dimensioni dei file di dati

Le dimensioni del database aumentano automaticamente man mano che si inseriscono dati.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual è la dimensione minima del database supportata da iperscalare o che inizia con

40 GB. Viene creato un database con iperscalabilità con una dimensione iniziale di 10 GB. Inizia quindi a crescere di 10 GB ogni 10 minuti, fino a raggiungere le dimensioni di 40 GB. Ognuno di questi mandrini da 10 GB viene allocato in un server di pagine diverso per fornire più IOPS e un maggiore parallelismo di I/O. A causa di questa ottimizzazione, anche se si scelgono dimensioni di database iniziali inferiori a 40 GB, il database aumenterà automaticamente ad almeno 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>In base a quali incrementi aumentano le dimensioni del database

Ogni file di dati aumenta di 10 GB. È possibile che più file di dati aumentino contemporaneamente.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>È lo spazio di archiviazione in locale o remoto iperscalabile

Con il livello Hyperscale, i file di dati vengono archiviati in Archiviazione Standard di Azure. I dati sono completamente memorizzati nella cache nell'archivio SSD locale, nei server di paging vicini alle repliche di calcolo. Inoltre, le repliche di calcolo hanno cache di dati nell'unità SSD locale e in memoria, per ridurre la frequenza di recupero dei dati dai server della pagina remota.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>È possibile gestire o definire file o filegroup con il livello Hyperscale

No. I file di dati vengono aggiunti automaticamente. I motivi comuni per la creazione di filegroup aggiuntivi non si applicano all'architettura di archiviazione con iperscalabilità.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>È possibile effettuare il provisioning di un limite rigido per la crescita dei dati per il database

No.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>In che modo i file di dati sono disposti con iperscalabilità

I file di dati sono controllati dai server di paging, con un server di paging per ogni file di dati. Man mano che aumentano le dimensioni dei dati, vengono aggiunti i file di dati e i server delle pagine associati.

### <a name="is-database-shrink-supported"></a>È supportata la compattazione del database

No.

### <a name="is-data-compression-supported"></a>Compressione dati supportata

Sì, tra cui la compressione di riga, pagina e columnstore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>In presenza di una tabella di grandi dimensioni, i dati della tabella vengono distribuiti in più file di dati

Sì. Le pagine di dati associate a una determinata tabella possono venire distribuite in più file di dati, che fanno tutti parte dello stesso filegroup. SQL Server usa la [strategia di riempimento proporzionale](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) per distribuire i dati nei file di dati.

## <a name="data-migration-questions"></a>Domande sulla migrazione dei dati

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>È possibile spostare i database esistenti nel database SQL di Azure nel livello di servizio con iperscalabilità

Sì. È possibile spostare i database esistenti nel database SQL di Azure in iperscalabilità. Si tratta di una migrazione unidirezionale. Non è possibile spostare i database da Hyperscale a un altro livello di servizio. Per il modello di prova (verifica), è consigliabile creare una copia del database ed eseguire la migrazione della copia a iperscalabilità. 

Il tempo necessario per spostare un database esistente in iperscalabilità è costituito dal tempo necessario per copiare i dati e dal tempo necessario per riprodurre le modifiche apportate nel database di origine durante la copia dei dati. Il tempo di copia dei dati è proporzionale alle dimensioni dei dati. Il tempo di riproduzione delle modifiche sarà più breve se lo spostamento viene eseguito durante un periodo di attività di scrittura insufficiente.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>È possibile spostare i database con iperscalabilità in altri livelli di servizio

No. Al momento non è possibile spostare un database iperscalabile in un altro livello di servizio.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune funzionalità del database SQL di Azure non sono ancora supportate in iperscalabilità, inclusa la conservazione dei backup a lungo termine. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.  Si prevede che queste limitazioni siano temporanee.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>È possibile spostare il database SQL Server locale o il database di SQL Server in una macchina virtuale cloud su scalabilità iperscalabile

Sì. È possibile utilizzare tutte le tecnologie di migrazione esistenti per eseguire la migrazione a iperscalabilità, inclusa la replica transazionale e qualsiasi altra tecnologia di spostamento dei dati (copia bulk, Azure Data Factory, Azure Databricks, SSIS). Vedere anche il [servizio migrazione del database di Azure](../../dms/dms-overview.md), che supporta molti scenari di migrazione.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual è il tempo di inattività durante la migrazione da un ambiente locale o di macchina virtuale a iperscalabilità e come posso ridurlo a icona

Il tempo di inattività per la migrazione a iperscalabilità è uguale a quello dei tempi di inattività durante la migrazione dei database ad altri livelli di servizio del database SQL di Azure. È possibile usare la [replica transazionale](replication-to-sql-database.md#data-migration-scenario
) per ridurre al minimo il tempo di inattività per la migrazione di database con dimensioni di pochi TB. Per database di dimensioni molto grandi (10 + TB), è possibile prendere in considerazione la migrazione dei dati usando ADF, Spark o altre tecnologie di spostamento dei dati.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo è necessario per portare la quantità X di dati su scalabilità eccessiva

L'iperscalabilità è in grado di utilizzare 100 MB/s di dati nuovi o modificati, ma il tempo necessario per spostare i dati nei database nel database SQL di Azure è influenzato anche dalla velocità effettiva di rete disponibile, dalla velocità di lettura dell'origine e dall'obiettivo del livello di servizio del database di destinazione.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>È possibile leggere i dati dall'archivio BLOB e caricare rapidamente (ad esempio, la polibase in SQL Data Warehouse)

È possibile fare in modo che un'applicazione client legga i dati da archiviazione di Azure e carichi i dati in un database con iperscalabilità (analogamente a qualsiasi altro database nel database SQL di Azure). La polibase non è attualmente supportata nel database SQL di Azure. Come alternativa per fornire un carico rapido, è possibile usare [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)o usare un processo spark in [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) con il [connettore Spark per SQL](spark-connector.md). Il connettore Spark per SQL supporta l'inserimento bulk.

È anche possibile eseguire la lettura bulk dei dati dall'archivio BLOB di Azure usando BULK INSERT o OPENROWSET: [esempi di accesso bulk ai dati nell'archivio BLOB di Azure](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Il modello di recupero con registrazione minima o con registrazione bulk non è supportato nel livello Hyperscale. Per fornire disponibilità elevata e ripristino temporizzato, è necessario il modello di recupero con esecuzione completa. Tuttavia, l'architettura di log con iperscalabilità offre una migliore velocità di inserimento dei dati rispetto agli altri livelli di servizio del database SQL di Azure.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>L'iperscalabilità consente il provisioning di più nodi per l'inserimento parallelo di grandi quantità di dati

No. L'iperscalabilità è un'architettura SMP (symmetric multiprocessing) e non è un'architettura MPP (Massive Parallel Processing) o multimaster. È possibile solo creare più repliche per la scalabilità orizzontale dei carichi di lavoro di sola lettura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual è la versione meno recente di SQL Server supportata per la migrazione a iperscalabilità

SQL Server 2005. Per altre informazioni, vedere [Eseguire la migrazione a un database singolo o in pool](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). Per i problemi di compatibilità, vedere [Risoluzione dei problemi di compatibilità della migrazione di database](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>L'iperscalabilità supporta la migrazione da altre origini dati, ad esempio Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e altre piattaforme di database

Sì. Il [servizio migrazione del database di Azure](../../dms/dms-overview.md) supporta molti scenari di migrazione.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Domande su continuità aziendale e ripristino di emergenza

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quali contratti di prestazioni sono disponibili per un database con iperscalabilità

Vedere [SLA per il database SQL di Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Le repliche di calcolo secondarie aggiuntive aumentano la disponibilità, fino al 99,99% per un database con due o più repliche di calcolo secondarie.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>I backup del database vengono gestiti per me dal database SQL di Azure

Sì.

### <a name="how-often-are-the-database-backups-taken"></a>Con che frequenza vengono eseguiti i backup dei database

Non sono disponibili backup completi, differenziali e di log tradizionali per i database con iperscalabilità. Sono invece presenti snapshot di archiviazione regolari dei file di dati. Il log generato viene semplicemente mantenuto così com'è per il periodo di memorizzazione configurato, consentendo il ripristino fino a qualsiasi punto nel tempo entro il periodo di memorizzazione.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Il ripristino temporizzato supporta l'iperscalabilità

Sì.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Qual è l'obiettivo del punto di ripristino (RPO)/Recovery Time Objective (RTO) per il ripristino del database in iperscalabilità

Il valore di RPO è 0 min. L'obiettivo di RTO è inferiore a 10 minuti, indipendentemente dalle dimensioni del database.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Il backup del database influisce sulle prestazioni di calcolo sulle repliche primarie o secondarie

No. I backup vengono gestiti dal sottosistema di archiviazione e sfruttano gli snapshot di archiviazione. Non influiscano sui carichi di lavoro degli utenti.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>È possibile eseguire il ripristino geografico con un database con iperscalabilità

Sì. Il ripristino geografico è completamente supportato. A differenza del ripristino temporizzato, il ripristino geografico richiede un'operazione di dimensione dei dati. Poiché i file di dati vengono copiati in parallelo, la durata di questa operazione dipende principalmente dalle dimensioni del file più grande nel database, invece che dalle dimensioni totali del database. Il tempo di ripristino geografico sarà significativamente più breve se il database viene ripristinato nell'area di Azure [associata all'](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) area del database di origine.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>È possibile configurare la replica geografica con un database con iperscalabilità

Attualmente non è possibile.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>È possibile eseguire un backup del database con iperscalabilità e ripristinarlo nel server locale o in SQL Server in una macchina virtuale

No. Il formato di archiviazione per i database con iperscalabilità è diverso da qualsiasi versione rilasciata di SQL Server e non è possibile controllare i backup né accedervi. Per estrarre i dati da un database con iperscalabilità, è possibile estrarre i dati usando qualsiasi tecnologia di spostamento dei dati, ad esempio Azure Data Factory, Azure Databricks, SSIS e così via.

## <a name="cross-feature-questions"></a>Domande tra funzionalità

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune funzionalità del database SQL di Azure non sono supportate in iperscalabilità, inclusa la conservazione dei backup a lungo termine. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.

### <a name="will-polybase-work-with-hyperscale"></a>Il polibase funzionerà con iperscalabilità

No. La polibase non è supportata nel database SQL di Azure.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>L'iperscalabilità ha supporto per R e Python

Attualmente non è possibile.

### <a name="are-compute-nodes-containerized"></a>Nodi di calcolo in contenitori

No. I processi di iperscalabilità vengono eseguiti su nodi [Service Fabric](https://azure.microsoft.com/services/service-fabric/) (VM), non in contenitori.

## <a name="performance-questions"></a>Domande sulle prestazioni

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto è possibile eseguire il push di una velocità effettiva di scrittura in un database con iperscalabilità

Il limite di velocità effettiva del log delle transazioni è impostato su 100 MB/s per qualsiasi dimensione di calcolo iperscalabile. La possibilità di ottenere questa frequenza dipende da diversi fattori, tra cui il tipo di carico di lavoro, la configurazione del client e la capacità di calcolo sufficiente sulla replica di calcolo primaria per produrre log a questa velocità.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Il numero di operazioni di i/o al secondo per il calcolo più grande

La latenza di IOPS e i/o variano a seconda dei modelli di carico di lavoro. Se i dati a cui si accede vengono memorizzati nella cache della replica di calcolo, vengono visualizzate prestazioni di i/o simili a quelle dell'unità SSD locale.

### <a name="does-my-throughput-get-affected-by-backups"></a>La velocità effettiva è influenzata dai backup

No. Il calcolo è separato dal livello di archiviazione. In questo modo si elimina l'effetto sulle prestazioni del backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>La velocità effettiva viene influenzata durante il provisioning di repliche di calcolo aggiuntive

Poiché l'archiviazione è condivisa e non viene eseguita alcuna replica fisica diretta tra le repliche di calcolo primarie e secondarie, la velocità effettiva della replica primaria non sarà interessata direttamente dall'aggiunta di repliche secondarie. Tuttavia, è possibile limitare il carico di lavoro di scrittura continua aggressiva sul database primario per consentire l'applicazione del log sulle repliche secondarie e sui server di paging, per evitare una riduzione delle prestazioni di lettura sulle repliche secondarie.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Ricerca per categorie diagnosticare e risolvere i problemi di prestazioni in un database con iperscalabilità

Per la maggior parte dei problemi di prestazioni, in particolare quelli non radicati nelle prestazioni di archiviazione, si applicano i passaggi comuni di diagnostica SQL e risoluzione dei problemi. Per la diagnostica dell'archiviazione specifica dell'iperscalabilità, vedere diagnostica per la [risoluzione dei problemi di prestazioni dell'iperscalabilità SQL](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Domande sulla scalabilità

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo è necessario per la scalabilità verticale e orizzontale di una replica di calcolo

La scalabilità verso l'alto o verso il basso richiede in genere fino a 2 minuti indipendentemente dalla dimensione dei dati.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Il database è offline mentre vengono eseguite le operazioni di ridimensionamento

No. Le operazioni di ridimensionamento vengono eseguite online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Possono verificarsi interruzioni della connessione mentre vengono eseguite le operazioni di ridimensionamento

La scalabilità verso l'alto o verso il basso comporta l'eliminazione delle connessioni esistenti quando si verifica un failover al termine dell'operazione di ridimensionamento. L'aggiunta di repliche secondarie non comporta cali di connessione.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Aumento e riduzione delle prestazioni delle repliche di calcolo automatiche o dell'operazione attivata dall'utente finale

Attivata dall'utente finale. Non è un'operazione automatica.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Le dimensioni del `tempdb` database aumentano anche quando viene aumentata la scalabilità verticale del calcolo

Sì. Il `tempdb` database verrà ridimensionato automaticamente in base alla crescita del calcolo.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>È possibile eseguire il provisioning di più repliche di calcolo primarie, ad esempio un sistema multimaster, in cui più teste di calcolo primarie possono guidare un livello più elevato di concorrenza

No. Solo la replica primaria di calcolo accetta richieste di lettura/scrittura. Le repliche di calcolo secondarie accettano solo richieste di sola lettura.

## <a name="read-scale-out-questions"></a>Domande sulla scalabilità in lettura

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Numero di repliche di calcolo secondarie di cui è possibile eseguire il provisioning

Per impostazione predefinita, viene creata una replica secondaria per i database con iperscalabilità. Se si vuole modificare il numero di repliche, è possibile usare [portale di Azure](https://portal.azure.com) o l' [API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Ricerca per categorie connettersi alle repliche di calcolo secondarie

È possibile connettersi a queste repliche di calcolo di sola lettura aggiuntive impostando l' `ApplicationIntent` argomento nella stringa di connessione su `ReadOnly` . Qualsiasi connessione contrassegnata con `ReadOnly` viene indirizzata automaticamente a una delle repliche di calcolo aggiuntive di sola lettura.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Ricerca per categorie verificare se la connessione alla replica di calcolo secondaria è stata stabilita con SSMS o altri strumenti client?

È possibile eseguire la query T-SQL seguente: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
Il risultato è `READ_ONLY` se si è connessi a una replica secondaria di sola lettura e `READ_WRITE` se si è connessi alla replica primaria. Si noti che il contesto del database deve essere impostato sul nome del database iperscalato, non sul `master` database.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>È possibile creare un endpoint dedicato per una replica con scalabilità orizzontale in lettura

No. È possibile connettersi solo per le repliche con scalabilità orizzontale in lettura specificando `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Il sistema esegue il bilanciamento del carico intelligente per il carico di lavoro di lettura

No. Una nuova connessione con finalità di sola lettura viene reindirizzata a una replica con scalabilità orizzontale in lettura arbitraria.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>È possibile ridimensionare le repliche di calcolo secondarie in modo indipendente dalla replica primaria

No. La replica di calcolo secondaria viene usata anche come destinazione del failover a disponibilità elevata, quindi è necessario che la stessa configurazione del database primario fornisca le prestazioni previste dopo il failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Si ottengono dimensioni diverse `tempdb` per il calcolo primario e le repliche di calcolo secondarie aggiuntive

No. Il `tempdb` database è configurato in base al provisioning delle dimensioni di calcolo, le repliche di calcolo secondarie hanno le stesse dimensioni del calcolo primario.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>È possibile aggiungere indici e viste sulle repliche di calcolo secondarie

No. I database con iperscalabilità includono spazio di archiviazione condiviso, vale a dire che tutte le repliche di calcolo visualizzano le stesse tabelle, gli indici e le viste. Se si desidera che gli indici aggiuntivi siano ottimizzati per le letture sul database secondario, è necessario aggiungerli nel database primario.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Il ritardo tra le repliche di calcolo primarie e secondarie

La latenza dei dati dal momento in cui viene eseguito il commit di una transazione nel database primario nel momento in cui è visibile in una replica secondaria dipende dalla frequenza di generazione del log corrente. La latenza dei dati tipica è di pochi millisecondi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul livello di servizio di iperscalabilità, vedere [livello di servizio di iperscalabilità](service-tier-hyperscale.md).
