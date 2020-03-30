---
title: Domande frequenti sull'hyperscale del database SQL di AzureAzure SQL Database Hyperscale FAQ
description: Risposte alle domande comuni dei clienti sul database SQL di Azure nel livello di servizio Hyperscale, detto comunemente database Hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268626"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Domande frequenti sull'hyperscale del database SQL di AzureAzure SQL Database Hyperscale FAQ

Questo articolo fornisce le risposte alle domande frequenti per i clienti che considerano un database nel livello di servizio Hyperscale del database SQL di Azure, denominato semplicemente Hyperscale nella parte restante di queste domande frequenti. In questo articolo vengono descritti gli scenari supportati da Hyperscale e le funzionalità compatibili con Hyperscale.

- Queste domande frequenti sono destinate a coloro che hanno una conoscenza superficiale del livello di servizio Hyperscale e necessitano di risposte a domande e preoccupazioni specifiche.
- Questa FAQ non deve essere una guida o rispondere a domande su come utilizzare un database Hyperscale. Per un'introduzione a Hyperscale, è consigliabile fare riferimento alla documentazione di Hyperscale del database SQL di Azure.For an introduction to [Hyperscale,](sql-database-service-tier-hyperscale.md) we recommend you refer to the Azure SQL Database Hyperscale documentation.

## <a name="general-questions"></a>Domande generali

### <a name="what-is-a-hyperscale-database"></a>Cosa è un database Hyperscale

Un database Hyperscale è un database SQL di Azure nel livello di servizio Hyperscale, basato sulla tecnologia di archiviazione Hyperscale di tipo scale-out. Un database Hyperscale supporta fino a 100 TB di dati e offre velocità effettiva e prestazioni elevate, oltre che velocità di ridimensionamento per adattarsi ai requisiti dei carichi di lavoro. La scalabilità è trasparente per l'applicazione: connettività, elaborazione delle query e così via funzionano come qualsiasi altro database SQL di Azure.Scaling is transparent to the application – connectivity, query processing, etc. work like any other Azure SQL database.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quali tipi di risorse e modelli di acquisto sono supportati dal livello Hyperscale

Il livello di servizio Hyperscale è disponibile solo per i database singoli che usano il modello di acquisto basato su vCore nel database SQL di Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Quali sono le differenze tra il livello di servizio Hyperscale e i livelli di servizio per utilizzo generico e business critical

I livelli di servizio basati su vCore vengono differenziati in base alla disponibilità e al tipo di archiviazione del database, alle prestazioni e alle dimensioni massime, come descritto nella tabella seguente.

| | Tipo di risorsa | Utilizzo generico |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** |Tutti|Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti.|La maggior parte dei carichi di lavoro aziendali. Dimensione di archiviazione con scalabilità automatica fino a 100 TB, scalabilità rapida di calcolo verticale e orizzontale, ripristino rapido del database.|Applicazioni OLTP con un tasso di transazione elevato e una bassa latenza di I/O. Offre la massima resilienza agli errori e ai failover rapidi utilizzando più repliche aggiornate in modo sincrono.|
|  **Tipo di risorsa** ||Database singolo/pool elastico/istanza gestita | Database singolo | Database singolo/pool elastico/istanza gestita |
| **Dimensioni di calcolo**|Database singolo/pool elastico* | Da 1 a 80 vCore | Da 1 a 80 vCore* | Da 1 a 80 vCore |
| |Istanza gestita | 8, 16, 24, 32, 40, 64, 80 vCore | N/D | 8, 16, 24, 32, 40, 64, 80 vCore |
| **Tipo di archiviazione** | Tutti |Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni archiviazione** | Database singolo/pool elastico*| 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **IOPS** | Database singolo | 500 operazioni di I/O al secondo per vCore fino a un massimo di 7000 | Hyperscale è un'architettura a più livelli con memorizzazione nella cache a più livelli. Le operazioni di I/O al secondo valide dipenderanno dal carico di lavoro. | 5000 operazioni di I/O al secondo fino a un massimo di 200.000|
| | Istanza gestita | Dipende dalle dimensioni del file | N/D | 1375 IOPS/vCore |
|**Disponibilità**|Tutti|1 replica, nessuna scalabilità orizzontale in lettura, nessuna cache locale | Repliche multiple, fino a 4 cache in lettura scalabili, cache locale parziale | 3 repliche, 1 scalabilità orizzontale in lettura, HA con ridondanza di zona, archiviazione locale completa |
|**Backup**|Tutti|RA-GRS, 7-35 giorni di conservazione (7 giorni per impostazione predefinita)| RA-GRS, ritenzione di 7 giorni, recupero point-in-time costante (PITR) | RA-GRS, 7-35 giorni di conservazione (7 giorni per impostazione predefinita) |

\*I pool elastici non sono supportati nel livello di servizio Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Chi deve usare il livello di servizio Hyperscale

Il livello di servizio Hyperscale è destinato ai clienti che dispongono di database SQL Server locali di grandi dimensioni e desiderano modernizzare le applicazioni passando al cloud o ai clienti che utilizzano già il database SQL di Azure e desiderano espandere potenziale di crescita del database. Il livello Hyperscale è destinato anche ai clienti che sono alla ricerca di livelli elevati di prestazioni e scalabilità. Il livello Hyperscale offre:

- Dimensioni del database fino a 100 TB
- Backup di database veloci indipendentemente dalle dimensioni del database (i backup sono basati su snapshot di archiviazione)
- Ripristini veloci del database indipendentemente dalle dimensioni del database (i ripristini provengono da snapshot di archiviazione)Fast database restores regardless of database size (restores are from storage snapshots)
- Maggiore velocità effettiva del log indipendentemente dalle dimensioni del database e dal numero di vCore
- Lettura Scalabilità orizzontale con una o più repliche di sola lettura, usate per l'offload di lettura e come hot standby.
- Rapido aumento delle risorse di calcolo, in un tempo costante, per disporre di una maggiore potenza per un carico di lavoro impegnativo e quindi riduzione delle risorse in un tempo costante. Questo è simile alla scalabilità verticale e verso il basso tra un P6 e un P11, ad esempio, ma molto più veloce in quanto non si tratta di una dimensione del funzionamento dei dati.

### <a name="what-regions-currently-support-hyperscale"></a>Quali aree supportano attualmente il livello Hyperscale

Il livello di servizio Hyperscale è attualmente disponibile nelle aree elencate in [Panoramica dell'hyperscale](sql-database-service-tier-hyperscale.md#regions)del database SQL di Azure.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>È possibile creare più database Hyperscale per ogni server logico

Sì. Per altre informazioni e per i limiti al numero di database Hyperscale per ogni server logico, vedere [Limiti delle risorse del database SQL per database singoli e in pool in un server logico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quali sono le caratteristiche delle prestazioni di un database Hyperscale

L'architettura Hyperscale offre prestazioni e velocità effettiva elevate grazie al supporto di database di grandi dimensioni. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual è la scalabilità di un database Hyperscale

Hyperscale offre una scalabilità rapida in base alla richiesta del carico di lavoro.

- **Scalabilità verticale**

  Con Hyperscale, è possibile aumentare le dimensioni di calcolo primarie in termini di risorse come CPU e memoria e quindi ridurre, in tempo costante. Poiché l'archiviazione è condivisa, la scalabilità verticale non è un'operazione di ridimensionamento dei dati.  
- **Scalabilità orizzontale**

  Con Hyperscale, è inoltre possibile eseguire il provisioning di una o più repliche di calcolo aggiuntive che è possibile usare per soddisfare le richieste di lettura. Ciò significa che è possibile usare queste repliche di calcolo aggiuntive come repliche di sola lettura per eseguire l'offload del carico di lavoro di lettura dal calcolo primario. Oltre alla sola lettura, queste repliche fungono anche da hot-standby in caso di failover dal database primario.

  Il provisioning di ognuna di queste repliche di calcolo aggiuntive può essere eseguito in tempi costanti ed è un'operazione online. È possibile connettersi a queste repliche di `ApplicationIntent` calcolo di sola `ReadOnly`lettura aggiuntive impostando l'argomento nella stringa di connessione su . Tutte le `ReadOnly` connessioni con la finalità dell'applicazione vengono automaticamente indirizzate a una delle repliche di calcolo di sola lettura aggiuntive.

## <a name="deep-dive-questions"></a>Domande sull'immersione profonda

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>È possibile combinare i database Hyperscale e single in un unico server logico

Sì,

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Il livello Hyperscale richiede la modifica del modello di programmazione dell'applicazione

No, il modello di programmazione dell'applicazione rimane invariato. Utilizzare la stringa di connessione come di consueto e gli altri modi regolari per interagire con il database Hyperscale.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Il livello di isolamento delle transazioni è l'impostazione predefinita in un database HyperscaleWhat transaction isolation level is the default in a Hyperscale database

Nella replica primaria, il livello di isolamento della transazione predefinito è RCSI (Read Committed Snapshot Isolation). Nelle repliche secondarie con scalabilità orizzontale in lettura, il livello di isolamento predefinito è Snapshot.On the Read Scale-out secondary replicas, the default isolation level is Snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso portare la mia licenza di SQL Server locale o IaaS su Hyperscale

Sì. Il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) è disponibile per il livello Hyperscale. Ogni core SQL Server Standard può essere mappato a 1 vCore Hyperscale. Ogni core SQL Server Enterprise può essere mappato a 4 vCore Hyperscale. Non è necessaria una licenza SQL per le repliche secondarie. Il prezzo del vantaggio Azure Hybrid verrà applicato automaticamente alle repliche con scalabilità orizzontale in lettura (secondaria).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Per che tipo di carichi di lavoro è progettato per Hyperscale

Hyperscale supporta tutti i carichi di lavoro di SQL Server, ma è ottimizzato principalmente per OLTP. È possibile portare carichi di lavoro ibrido (HTAP) e analitico (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Come è possibile scegliere tra Azure SQL Data Warehouse e l'hyperscale del database SQL di AzureHow I can choose between Azure SQL Data Warehouse and Azure SQL Database Hyperscale

Se attualmente si eseguono query di analisi interattive utilizzando SQL Server come data warehouse, Hyperscale è un'ottima opzione perché è possibile ospitare data warehouse di piccole e medie dimensioni (ad esempio alcuni TB fino a 100 TB) a un costo inferiore ed è possibile eseguire la migrazione dei dati di SQL Server carichi di lavoro di magazzino per Hyperscale con modifiche minime al codice T-SQL.

Se si esegue l'analisi dei dati su larga scala con query complesse e velocità di inserimento sostenute superiori a 100 MB/s oppure si utilizza Parallel Data Warehouse (PDW), Teradata o altri data warehouse di elaborazione a massa parallela (MPP), SQL Data Warehouse potrebbe essere la scelta migliore.
  
## <a name="hyperscale-compute-questions"></a>Domande sul calcolo Hyperscale

### <a name="can-i-pause-my-compute-at-any-time"></a>È possibile sospendere il calcolo in qualsiasi momento

Non in questo momento, tuttavia è possibile ridimensionare il calcolo e il numero di repliche verso il basso per ridurre i costi durante le ore non di punta.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>È possibile eseguire il provisioning di una replica di calcolo con RAM aggiuntiva per il carico di lavoro che richiede un utilizzo intensivo della memoria

No. Per ottenere più RAM, è necessario eseguire l'aggiornamento a dimensioni di calcolo superiori. Per altre informazioni, vedere [Dimensioni di calcolo e archiviazione del livello Hyperscale](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>È possibile eseguire il provisioning di più repliche di calcolo di dimensioni diverseCan I provision multiple compute replicas of different sizes

No.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Numero di repliche con scalabilità orizzontale in lettura supportate

I database di hyperscale vengono creati con una replica con scalabilità orizzontale in lettura (due repliche, inclusa la replica primaria), per impostazione predefinita. È possibile ridimensionare il numero di repliche di sola lettura tra 0 e 4 usando il portale di [Azure](https://portal.azure.com) o l'API [REST.](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Per la disponibilità elevata, è necessario eseguire il provisioning di repliche di calcolo aggiuntiveFor high availability, Do Do to provision additional compute replicas

Nei database Hyperscale la resilienza dei dati viene fornita a livello di archiviazione. È necessaria una sola replica per garantire resilienza. Quando la replica di calcolo non è attiva, viene creata automaticamente una nuova replica senza alcuna perdita di dati.

Se tuttavia c'è una sola replica, potrebbe essere necessario un po' di tempo per creare la cache locale nella nuova replica dopo il failover. Durante la fase di ricostruzione della cache, il database recupera i dati direttamente dai server di paging, con conseguente latenza di archiviazione più elevata e prestazioni delle query ridotte.

Per le app mission-critical che richiedono disponibilità elevata con un impatto di failover minimo, è consigliabile eseguire il provisioning di almeno 2 repliche di calcolo, inclusa la replica di calcolo primaria. Questa è la configurazione predefinita. In questo modo è disponibile una replica hot-standby che funge da destinazione di failover.

## <a name="data-size-and-storage-questions"></a>Domande sulle dimensioni dei dati e sull'archiviazione

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual è la dimensione massima del database supportata con Hyperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Quali sono le dimensioni del log delle transazioni con il livello Hyperscale

Il log delle transazioni con il livello Hyperscale è praticamente infinito. Non è necessario preoccuparsi di esaurire lo spazio di log in un sistema con velocità effettiva di log elevata. Tuttavia, la frequenza di generazione dei log potrebbe essere limitata per carichi di lavoro di scrittura aggressiva continua. Il picco sostenuto di generazione dei log è di 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>La `tempdb` mia scalabilità man mano che il mio database cresce

Il `tempdb` database si trova nell'archiviazione SSD locale e viene ridimensionato in modo proporzionale alle dimensioni di calcolo di cui si esegue il provisioning. L'utente `tempdb` è ottimizzato per offrire i massimi vantaggi in termini di prestazioni. `tempdb`non è configurabile ed è gestita per l'utente.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Le dimensioni del database aumentano automaticamente o devo gestire le dimensioni dei file di dati

Le dimensioni del database aumentano automaticamente man mano che si inseriscono dati.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual è la dimensione minima del database supportata o a hyperscale

di 40 GB. Viene creato un database Hyperscale con una dimensione iniziale di 10 GB. Quindi, inizia a crescere di 10 GB ogni 10 minuti, fino a raggiungere la dimensione di 40 GB. Ognuno di questi mandrini da 10 GB viene allocato in un server di paging diverso per fornire più operazioni di I/O al secondo e un parallelismo i/O superiore. A causa di questa ottimizzazione, anche se si scelgono dimensioni iniziali del database inferiori a 40 GB, il database aumenterà fino ad almeno 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>In base a quali incrementi aumentano le dimensioni del database

Ogni file di dati aumenta di 10 GB. Più file di dati possono crescere contemporaneamente.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>L'archiviazione è in Hyperscale locale o remota

Con il livello Hyperscale, i file di dati vengono archiviati in Archiviazione Standard di Azure. I dati vengono completamente memorizzati nella cache dell'archiviazione SSD locale, nei server di paging vicini alle repliche di calcolo. Inoltre, le repliche di calcolo dispongono di cache di dati nell'unità SSD locale e in memoria, per ridurre la frequenza di recupero dei dati dai server di paging remoti.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>È possibile gestire o definire file o filegroup con il livello Hyperscale

No. I file di dati vengono aggiunti automaticamente. I motivi comuni per la creazione di filegroup aggiuntivi non si applicano nell'architettura di archiviazione Hyperscale.The common reasons for creating additional filegroups do not apply in the Hyperscale storage architecture.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>È possibile effettuare il provisioning di un limite rigido per la crescita dei dati per il database

No.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Come vengono disposti i file di dati con Hyperscale

I file di dati sono controllati da server di paging, con un server di pagina per file di dati. Con l'aumentare delle dimensioni dei dati, vengono aggiunti i file di dati e i server di paging associati.

### <a name="is-database-shrink-supported"></a>È supportata la compattazione del database

No.

### <a name="is-data-compression-supported"></a>La compressione dei dati è supportata

Sì, inclusa la compressione di righe, pagine e columnstore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>In presenza di una tabella di grandi dimensioni, i dati della tabella vengono distribuiti in più file di dati

Sì. Le pagine di dati associate a una determinata tabella possono venire distribuite in più file di dati, che fanno tutti parte dello stesso filegroup. SQL Serversql Server usa la strategia di [riempimento proporzionale](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) per distribuire i dati sui file di dati.

## <a name="data-migration-questions"></a>Domande sulla migrazione dei dati

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>È possibile spostare i database SQL di Azure esistenti al livello di servizio Hyperscale

Sì. È possibile spostare i database SQL di Azure esistenti al livello Hyperscale. Si tratta di una migrazione unidirezionale. Non è possibile spostare i database da Hyperscale a un altro livello di servizio. Per le prove di concetto (POC), è consigliabile creare una copia del database ed eseguire la migrazione della copia in Hyperscale.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>È possibile spostare i database Hyperscale in altri livelli di servizio

No. Al momento, non è possibile spostare un database Hyperscale in un altro livello di servizio.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune delle funzionalità del database SQL di Azure non sono ancora supportate in Hyperscale, inclusa, a dire il fatto che non si limita, alla conservazione dei backup a lungo termine. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.  Ci aspettiamo che queste limitazioni siano temporanee.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>È possibile spostare il database di SQL Server locale o il database di SQL Server in una macchina virtuale cloud in Hyperscale

Sì. È possibile usare tutte le tecnologie di migrazione esistenti per eseguire la migrazione a Hyperscale, inclusa la replica transazionale, e qualsiasi altra tecnologia di spostamento dei dati (copia globale, Azure Data Factory, Azure Databricks, SSIS). Vedere anche il [servizio di migrazione](../dms/dms-overview.md)del database di Azure , che supporta molti scenari di migrazione.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual è il tempo di inattività durante la migrazione da un ambiente locale o macchina virtuale a Hyperscale e come è possibile ridurlo a icona

Il tempo di inattività per la migrazione a Hyperscale è lo stesso del tempo di inattività quando si esegue la migrazione dei database ad altri livelli del servizio di database SQL di Azure.Downtime for migration to Hyperscale is the same as the downtime when you migrate your databases to other Azure SQL Database service tiers. È possibile usare la [replica transazionale](replication-to-sql-database.md#data-migration-scenario
) per ridurre al minimo il tempo di inattività per la migrazione di database con dimensioni di pochi TB. Per database di grandi dimensioni (10 TB), è possibile eseguire la migrazione dei dati utilizzando ADF, Spark o altre tecnologie di spostamento dei dati.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo ci vorrebbe per portare in X quantità di dati in Hyperscale

Hyperscale è in grado di consumare 100 MB/s di dati nuovi/modificati, ma il tempo necessario per spostare i dati nei database SQL di Azure è influenzato anche dalla velocità effettiva di rete disponibile, dalla velocità di lettura dell'origine e dall'obiettivo del livello di servizio del database di destinazione.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>È possibile leggere i dati dall'archiviazione BLOB ed eseguire un caricamento rapido (ad esempio Polybase in SQL Data Warehouse)Can I read data from blob storage and do fast load (like Polybase in SQL Data Warehouse)

È possibile fare in modo che un'applicazione client legga i dati da Archiviazione di Azure e carichi di dati in un database Hyperscale (proprio come per qualsiasi altro database SQL di Azure). Polybase is currently not supported in Azure SQL Database. In alternativa per fornire un caricamento rapido, è possibile usare [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)o un processo Spark in Azure [Databricks](https://docs.microsoft.com/azure/azure-databricks/) con il [connettore Spark per SQL.](sql-database-spark-connector.md) Il connettore Spark per SQL supporta l'inserimento bulk.

È anche possibile eseguire la lettura bulk dei dati dall'archivio BLOB di Azure usando BULK INSERT o OPENROWSET: [esempi di accesso bulk ai dati nell'archiviazione BLOB di Azure.It](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)is also possible to bulk read data from Azure Blob store using BULK INSERT or OPENROWSET: Examples of Bulk Access to Data in Azure Blob Storage .

Il modello di recupero con registrazione minima o con registrazione bulk non è supportato nel livello Hyperscale. Il modello di recupero con registrazione completa è necessario per garantire disponibilità elevata e ripristino temporizzato. Tuttavia, l'architettura del log Hyperscale offre una migliore frequenza di inserimento dati rispetto ad altri livelli di servizio del database SQL di Azure.However, Hyperscale log architecture provides better data ingest rate compared to other Azure SQL Database service tiers.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Hyperscale consente il provisioning di più nodi per l'inserimento parallelo di grandi quantità di dati

No. Hyperscale è un'architettura SMP (Multiprocessing) simmetrica e non è un'architettura MPP (Massicciamente parallela) o un'architettura multimaster. È possibile solo creare più repliche per la scalabilità orizzontale dei carichi di lavoro di sola lettura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual è la versione meno recente di SQL Server supportata per la migrazione a Hyperscale

SQL Server 2005. Per altre informazioni, vedere [Eseguire la migrazione a un database singolo o in pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Per i problemi di compatibilità, vedere [Risoluzione dei problemi di compatibilità della migrazione di database](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Hyperscale supporta la migrazione da altre origini dati come Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e altre piattaforme di database

Sì. [Il servizio di migrazione del database](../dms/dms-overview.md) di Azure supporta molti scenari di migrazione.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Domande sulla continuità aziendale e sul ripristino di emergenza

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quali contratti di accesso schiavi vengono forniti per un database HyperscaleWhat SLAs are provided for a Hyperscale database

Vedere [SLA per il database SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)di Azure. Le repliche di calcolo secondarie aggiuntive aumentano la disponibilità, fino al 99,99% per un database con due o più repliche di calcolo secondarie.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>I backup dei database vengono gestiti automaticamente dal servizio di database SQL di Azure

Sì.

### <a name="how-often-are-the-database-backups-taken"></a>Con che frequenza vengono eseguiti i backup dei database

Non esistono backup completi, differenziali e di log tradizionali per i database di hyperscale. Sono invece disponibili snapshot di archiviazione regolari dei file di dati. Il log generato viene semplicemente mantenuto così com'è per il periodo di conservazione configurato, consentendo il ripristino in qualsiasi momento all'interno del periodo di conservazione.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Hyperscale supporta il ripristino temporizzato

Sì.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Che cos'è l'obiettivo del punto di ripristino (RPO)/Recovery Time Objective (RTO) per il ripristino del database in Hyperscale

L'RPO è 0 min. L'obiettivo RTO è inferiore a 10 minuti, indipendentemente dalle dimensioni del database. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Il backup del database influisce sulle prestazioni di calcolo nelle repliche primarie o secondarie

No. I backup vengono gestiti dal sottosistema di archiviazione e sfruttano gli snapshot di archiviazione. Non influiscono sui carichi di lavoro degli utenti.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>È possibile eseguire il geo-ripristino con un database Hyperscale

Sì. Il ripristino geografico è completamente supportato. A differenza del ripristino temporizzato, il ripristino geografico richiede un'operazione di dimensione dei dati. I file di dati vengono copiati in parallelo, pertanto la durata di questa operazione dipende principalmente dalle dimensioni del file più grande nel database, anziché dalle dimensioni totali del database. Il tempo di ripristino geografico sarà significativamente più breve se il database viene ripristinato nell'area di Azure [associata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) all'area del database di origine.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>È possibile configurare la replica geografica con il database Hyperscale

Attualmente non è possibile.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>È possibile eseguire un backup del database Hyperscale e ripristinarlo nel server locale o in SQL Server in una macchina virtuale

No. Il formato di archiviazione per i database Hyperscale è diverso da qualsiasi versione rilasciata di SQL ServerSQL Server e non è possibile controllarli. Per estrarre i dati da un database Hyperscale, è possibile estrarre i dati usando qualsiasi tecnologia di spostamento dati, ad esempio Azure Data Factory, Azure Databricks, SSIS e così via.

## <a name="cross-feature-questions"></a>Domande trasversali

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune delle funzionalità del database SQL di Azure non sono supportate in Hyperscale, inclusa, ma non limitata alla conservazione dei backup a lungo termine. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.

### <a name="will-polybase-work-with-hyperscale"></a>Polibase funziona con Hyperscale

No. Polybase is not supported in Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Hyperscale ha il supporto per R e Python

Attualmente non è possibile.

### <a name="are-compute-nodes-containerized"></a>I nodi di calcolo vengono in sestati in contenitore

No. I processi di hyperscale vengono eseguiti in nodi di [Service Fabric](https://azure.microsoft.com/services/service-fabric/) (VM) e non in contenitori.

## <a name="performance-questions"></a>Domande sulle prestazioni

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanta velocità effettiva di scrittura è possibile eseguire il push in un database HyperscaleHow how write throughput can push in a Hyperscale database

Il limite di velocità effettiva del log delle transazioni è impostato su 100 MB/s per qualsiasi dimensione di calcolo Hyperscale.Transaction log throughput cap is set to 100 MB/s for any Hyperscale compute size. La capacità di raggiungere questa velocità dipende da più fattori, tra cui, a titolo emeno, il tipo di carico di lavoro, la configurazione client e la capacità di calcolo sufficiente nella replica di calcolo primaria per produrre log a questa velocità.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quante operazioni di I/O al secondo ottengo nel calcolo più grande

Le operazioni di I/O al secondo e la latenza di I/O variano a seconda dei modelli di carico di lavoro. Se i dati a cui si accede vengono memorizzati nella cache nella replica di calcolo, si noterà prestazioni di I/O simili a quelle dell'unità SSD locale.

### <a name="does-my-throughput-get-affected-by-backups"></a>La velocità effettiva è influenzata dai backup

No. Il calcolo viene disaccoppiato dal livello di archiviazione. In questo modo si elimina l'impatto sulle prestazioni del backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>La velocità effettiva viene influenzata durante il provisioning di repliche di calcolo aggiuntive

Poiché l'archiviazione è condivisa e non viene eseguita alcuna replica fisica diretta tra repliche di calcolo primarie e secondarie, la velocità effettiva nella replica primaria non sarà influenzata direttamente dall'aggiunta di repliche secondarie. Tuttavia, è possibile limitare il carico di lavoro di scrittura continua in modo aggressivo sul database primario per consentire l'applicazione del log nelle repliche secondarie e nei server di paging per recuperare il ritardo, per evitare una riduzione delle prestazioni di lettura nelle repliche secondarie.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Come è possibile diagnosticare e risolvere i problemi di prestazioni in un database Hyperscale

Per la maggior parte dei problemi di prestazioni, in particolare quelli non radicati nelle prestazioni di archiviazione, si applicano i passaggi comuni di diagnostica e risoluzione dei problemi di SQL Server.For most performance problems, particularly the ones in storage performance, common SQL Server diagnostic and troubleshooting steps apply. Per la diagnostica di archiviazione specifica di Hyperscale, vedere Diagnostica della risoluzione dei problemi di [prestazioni di Hyperscale di SQL](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Domande sulla scalabilità

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo ci vorrebbe per scalare su e verso il basso una replica di calcolo

Il calcolo della scalabilità verso l'alto o verso il basso dovrebbe richiedere da 5 a 10 minuti indipendentemente dalle dimensioni dei dati.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Il database è offline mentre vengono eseguite le operazioni di ridimensionamento

No. Le operazioni di ridimensionamento vengono eseguite online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Possono verificarsi interruzioni della connessione mentre vengono eseguite le operazioni di ridimensionamento

La scalabilità verticale o verso il basso comporta l'eliminazione delle connessioni esistenti quando si verifica un failover al termine dell'operazione di ridimensionamento. L'aggiunta di repliche secondarie non comporta l'eliminazione della connessione.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>L'operazione di ridimensionamento verso l'alto e verso l'alto delle repliche di calcolo automatico o dell'utente finale è attivata

Attivata dall'utente finale. Non è un'operazione automatica.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Anche le dimensioni `tempdb` del database aumentano man mano che il calcolo viene aumentato

Sì. Il `tempdb` database verrà eseguito automaticamente all'aumentare del calcolo.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>È possibile eseguire il provisioning di più repliche di calcolo primarie, ad esempio un sistema multimaster, in cui più teste di calcolo primarie possono guidare un livello superiore di concorrenza

No. Solo la replica di calcolo primaria accetta richieste di lettura/scrittura. Le repliche di calcolo secondarie accettano solo richieste di sola lettura.

## <a name="read-scale-out-questions"></a>Leggi le domande sulla scalabilità orizzontaleRead Scale-out Questions

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quante repliche di calcolo secondarie è possibile eseguire il provisioning

Per impostazione predefinita, viene creata una replica secondaria per i database Hyperscale.We create one secondary replica for Hyperscale databases by default. Se si vuole modificare il numero di repliche, è possibile farlo usando il portale di [Azure](https://portal.azure.com) o l'API [REST.](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Come connettersi a queste repliche di calcolo secondarieHow do Mi connect to these secondary compute replicas

È possibile connettersi a queste repliche di `ApplicationIntent` calcolo di sola `ReadOnly`lettura aggiuntive impostando l'argomento nella stringa di connessione su . Tutte le `ReadOnly` connessioni contrassegnate con vengono instradate automaticamente a una delle repliche di calcolo di sola lettura aggiuntive.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Come è possibile eseguire la convalida se la connessione alla replica di calcolo secondaria è stata eseguita correttamente tramite SSMS o altri strumenti client?

È possibile eseguire la seguente `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`query T-SQL: .
Il risultato è `READ_ONLY` se si è connessi a `READ_WRITE` una replica secondaria di sola lettura e se si è connessi alla replica primaria. Si noti che il contesto del database deve essere `master` impostato sul nome del database Hyperscale, non sul database.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>È possibile creare un endpoint dedicato per una replica con scalabilità orizzontale in lettura

No. È possibile connettersi alle repliche con scalabilità orizzontale in lettura solo specificando `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Il sistema esegue il bilanciamento del carico intelligente per il carico di lavoro di lettura

No. Una nuova connessione con finalità di sola lettura viene reindirizzata a una replica di scalabilità orizzontale in lettura arbitraria.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>È possibile scalare le repliche di calcolo secondarie indipendentemente dalla replica primaria

No. La replica di calcolo secondaria viene usata anche come destinazioni di failover a disponibilità elevata, pertanto è necessario avere la stessa configurazione del database primario per fornire le prestazioni previste dopo il failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>È possibile `tempdb` ottenere un dimensionamento diverso per il calcolo primario e le repliche di calcolo secondarie aggiuntive

No. Il `tempdb` database è configurato in base al provisioning delle dimensioni di calcolo, le repliche di calcolo secondarie hanno le stesse dimensioni del calcolo primario.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>È possibile aggiungere indici e viste nelle repliche di calcolo secondarie

No. I database di hyperscale dispongono di archiviazione condivisa, vale a dire che tutte le repliche di calcolo visualizzano le stesse tabelle, indici e viste. Se si desidera che gli indici aggiuntivi vengano ottimizzati per le letture secondarie, è necessario aggiungerli nella versione primaria.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto ritardo ci sarà tra le repliche di calcolo primarie e secondarie

La latenza dei dati dal momento in cui viene eseguito il commit di una transazione nel database primario al momento in cui è visibile in un database secondario dipende dalla velocità di generazione del log corrente. La latenza dati tipica è in millisecondi bassi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul livello di servizio Hyperscale, vedere [Livello di servizio Hyperscale](sql-database-service-tier-hyperscale.md).
