---
title: Domande frequenti sui database SQL di Azure di livello Hyperscale | Microsoft Docs
description: Risposte alle domande comuni dei clienti sul database SQL di Azure nel livello di servizio Hyperscale, detto comunemente database Hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 49d1e171d4d4b2210a98c59332f4842e23a2f2b9
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537843"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Domande frequenti sui database SQL di Azure di livello Hyperscale

Questo articolo offre risposte alle domande più frequenti per i clienti di prendere in considerazione un database nel livello di servizio con Iperscalabilità di Database SQL di Azure, comunemente denominato di un database con scalabilità elevatissima. Questo articolo descrive gli scenari supportati dal livello Hyperscale e i servizi multi funzionalità compatibili con il database SQL Hyperscale in generale.

- Queste domande frequenti sono destinate a coloro che hanno una conoscenza superficiale del livello di servizio Hyperscale e necessitano di risposte a domande e preoccupazioni specifiche.
- Queste domande frequenti non sono da ritenersi una guida e non rispondono a domande su come usare un database SQL di Azure di livello Hyperscale. A tale scopo, fare riferimento alla documentazione sul [database SQL di Azure di livello Hyperscale](sql-database-service-tier-hyperscale.md).

## <a name="general-questions"></a>Domande generali

### <a name="what-is-a-hyperscale-database"></a>Cosa è un database Hyperscale

Un database Hyperscale è un database SQL di Azure nel livello di servizio Hyperscale, basato sulla tecnologia di archiviazione Hyperscale di tipo scale-out. Un database Hyperscale supporta fino a 100 TB di dati e offre velocità effettiva e prestazioni elevate, oltre che velocità di ridimensionamento per adattarsi ai requisiti dei carichi di lavoro. Il ridimensionamento è trasparente per l'applicazione. Connettività, elaborazione di query e così via funzionano come in qualsiasi altro database SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quali tipi di risorse e modelli di acquisto sono supportati dal livello Hyperscale

Il livello di servizio Hyperscale è disponibile solo per i database singoli che usano il modello di acquisto basato su vCore nel database SQL di Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Quali sono le differenze tra il livello di servizio Hyperscale e i livelli di servizio per utilizzo generico e business critical

Il servizio basato su vCore livelli si differenziano principalmente in base al tipo di archiviazione, disponibilità e IOPs.

- Il livello di servizio per utilizzo generico è appropriato per la maggior parte dei carichi di lavoro aziendali e offre un set bilanciato di opzioni di calcolo e di archiviazione in cui la latenza di I/O o i tempi di failover non rappresentano una priorità.
- Il livello di servizio Hyperscale è ottimizzato per i carichi di lavoro di database di dimensioni molto grandi.
- Il livello di servizio business critical è adatto per i carichi di lavoro aziendali in cui la latenza di I/O è una priorità.

| | Tipo di risorsa | Utilizzo generico |  Hyperscale | Business Critical |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** |Tutti|  La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti. | Applicazioni dati con i requisiti di capacità di dati di grandi dimensioni e possibilità di ridimensionamento automatico delle risorse di archiviazione e facile ridimensionamento delle risorse di calcolo. | Applicazioni OLTP con frequenza di transazioni elevata e livelli minimi di latenza di I/O. Offre massima resilienza agli errori tramite diverse repliche isolate.|
|  **Tipo di risorsa** ||Database singolo/pool elastico/istanza gestita | Database singolo | Database singolo/pool elastico/istanza gestita |
| **Dimensioni di calcolo**|Database singolo/pool elastico* | Da 1 a 80 vCore | Da 1 a 80 vCore* | Da 1 a 80 vCore |
| |Istanza gestita | 8, 16, 24, 32, 40, 64, 80 vCore | N/D | 8, 16, 24, 32, 40, 64, 80 vCore |
| **Tipo di archiviazione** | Tutti |Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni archiviazione** | Database singolo/pool elastico | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Velocità effettiva di I/O** | Database singolo** | 500 operazioni di I/O al secondo per vCore fino a un massimo di 7000 | Su scala molto vasta è un'architettura a più livelli con più livelli di memorizzazione nella cache. IOPs effettivo varia in base al carico di lavoro. | 5000 operazioni di I/O al secondo fino a un massimo di 200.000|
| | Istanza gestita | Dipende dalle dimensioni del file | N/D | Istanza gestita: Dipende dalle dimensioni del file|
|**Disponibilità**|Tutti|1 replica, senza scalabilità in lettura, senza cache locale | Più repliche, fino a 15 repliche con scalabilità in lettura, cache locale parziale | 3 repliche, 1 replica con scalabilità in lettura, disponibilità elevata con ridondanza della zona, cache locale completa |
|**Backup**|Tutti|RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita)| RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita), il ripristino temporizzato in tempo costante (PITR) | RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita) |

\* Pool elastici non supportati nel livello di servizio Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Chi deve usare il livello di servizio Hyperscale

Il livello di servizio Hyperscale è destinato principalmente ai clienti che hanno database di SQL Server locali di grandi dimensioni e vogliono modernizzare le proprie applicazioni passando al cloud oppure ai clienti che usano già il database SQL di Azure e vogliono espandere significativamente il potenziale di crescita del database. Il livello Hyperscale è destinato anche ai clienti che sono alla ricerca di livelli elevati di prestazioni e scalabilità. Il livello Hyperscale offre:

- Supporto per database di dimensioni massime di 100 TB
- Rapidità di backup dei database indipendentemente dalle dimensioni (i backup sono basati su snapshot di file)
- Rapidità di ripristino dei database indipendentemente dalle dimensioni (i ripristini vengono eseguiti da snapshot di file)
- Maggiore velocità effettiva dei log e, di conseguenza, tempi rapidi di commit delle transazioni, indipendentemente dalle dimensioni dei database
- Scalabilità orizzontale in lettura con uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby.
- Rapido aumento delle risorse di calcolo, in un tempo costante, per disporre di una maggiore potenza per un carico di lavoro impegnativo e quindi riduzione delle risorse in un tempo costante. Si tratta di un'operazione simile al passaggio, ad esempio, da P6 a P11, ma avviene in modo molto più rapido perché non si tratta di un'operazione di ridimensionamento dei dati.

### <a name="what-regions-currently-support-hyperscale"></a>Quali aree supportano attualmente il livello Hyperscale

Il livello con Iperscalabilità di Database SQL di Azure è attualmente disponibile nelle regioni elencate sotto [Panoramica di Azure SQL Database su scala molto vasta](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>È possibile creare più database Hyperscale per ogni server logico

Sì. Per altre informazioni e per i limiti al numero di database Hyperscale per ogni server logico, vedere [Limiti delle risorse del database SQL per database singoli e in pool in un server logico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quali sono le caratteristiche delle prestazioni di un database con scalabilità elevatissima

L'architettura del database SQL Hyperscale offre livelli elevati di prestazioni e velocità effettiva e supporto per database di grandi dimensioni. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual è la scalabilità di un database Hyperscale

Il database SQL Hyperscale offre scalabilità rapida in base alle esigenze dei carichi di lavoro.

- **Scalabilità verticale**

  Con il livello Hyperscale, è possibile aumentare le dimensioni di calcolo primarie in termini di risorse come CPU e memoria e quindi ridurle, in un tempo costante. Poiché l'archiviazione è condivisa, la scalabilità verticale non è un'operazione di ridimensionamento dei dati.  
- **Scalabilità orizzontale**

  Con il livello Hyperscale è anche possibile effettuare il provisioning di uno o più nodi di calcolo aggiuntivi, da usare per rispondere alle richieste di lettura. Ciò significa che è possibile usare questi nodi di calcolo aggiuntivi come nodi di sola lettura per l'offload del carico di lavoro di lettura dalle risorse di calcolo primarie. In oltre per fare in sola lettura, che questi nodi fungono anche come hot standby in caso di failover dal database primario.

  Il provisioning di ognuno di questi nodi di calcolo aggiuntivi può essere effettuato in un tempo costante ed è un'operazione online. È possibile connettersi a questi nodi di calcolo aggiuntivi di sola lettura impostando l'argomento `ApplicationIntent` nella stringa di connessione su `readonly`. Tutte le connessioni contrassegnate con `readonly` vengono indirizzate automaticamente a uno dei nodi di calcolo di sola lettura aggiuntivi.

## <a name="deep-dive-questions"></a>Domande di approfondimento

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>È possibile combinare con Iperscalabilità e i singoli database in un singolo server logico

Sì,

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Il livello Hyperscale richiede la modifica del modello di programmazione dell'applicazione

No, il modello di programmazione dell'applicazione rimane invariato. È possibile usare come sempre la stringa di connessione e le altre modalità standard per l'interazione con il database SQL di Azure.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Quali livelli di isolamento delle transazioni verranno applicati per impostazione predefinita nel database SQL Hyperscale

Nel nodo primario il livello di isolamento della transazione è RCSI (Read Committed Snapshot Isolation, isolamento dello snapshot Read Committed). Nei nodi secondari con scalabilità in lettura il livello di isolamento è Snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>È possibile trasferire la licenza di SQL Server locale o IaaS al database SQL Hyperscale

Sì. Il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) è disponibile per il livello Hyperscale. Ogni core SQL Server Standard può essere mappato a 1 vCore Hyperscale. Ogni core SQL Server Enterprise può essere mappato a 4 vCore Hyperscale. Non è necessaria una licenza SQL per le repliche secondarie. Il prezzo del vantaggio Azure Hybrid verrà applicato automaticamente alle repliche (secondarie) con scalabilità in lettura.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Per quali tipi di carici di lavoro è pensato il database SQL Hyperscale

Il database SQL Hyperscale supporta tutti i carichi di lavoro di SQL Server ma è ottimizzato principalmente per OLTP. È possibile portare ibrida (HTAP) e analitico (mart dati) carichi di lavoro.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Come è possibile scegliere tra Azure SQL Data Warehouse e il database SQL Hyperscale

Se attualmente si eseguono query analitica interattiva usando SQL Server come un data warehouse, con Iperscalabilità di Database SQL è un'ottima opzione perché è possibile ospitare relativamente ridotto di data warehouse (ad esempio, alcuni TB fino a 10 secondi TB) a un costo inferiore ed è possibile migrare i dati w arehouse carico di lavoro con Iperscalabilità di Database SQL senza modifiche al codice T-SQL.

Se si eseguono analisi dei dati su vasta scala con query complesse e si usano Parallel Data Warehouse (PDW), Teradata o altri data warehouse MPP (Massively Parallel Processor, elaborazione parallela elevata), SQL Data Warehouse può essere la scelta migliore.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Domande sulle funzionalità di calcolo del database SQL Hyperscale

### <a name="can-i-pause-my-compute-at-any-time"></a>È possibile sospendere il calcolo in qualsiasi momento

Non attualmente, tuttavia, è possibile scalare il calcolo e il numero di repliche verso il basso per ridurre i costi durante i periodi non di punta.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>È possibile effettuare il provisioning di risorse di calcolo con RAM aggiuntiva per un carico di lavoro a elevato utilizzo di memoria

No. Per ottenere più RAM, è necessario eseguire l'aggiornamento a dimensioni di calcolo superiori. Per altre informazioni, vedere [Dimensioni di calcolo e archiviazione del livello Hyperscale](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>È possibile effettuare il provisioning di più nodi di calcolo di dimensioni diverse

No.

### <a name="how-many-read-scale-replicas-are-supported"></a>Quante repliche con scalabilità in lettura sono supportate

Per impostazione predefinita, i database con scalabilità elevatissima vengono creati con una replica con scalabilità in lettura (due repliche in totale). È possibile aumentare il numero di repliche di sola lettura tra 0 e 4 usando il [portale di Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) oppure [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Per la disponibilità elevata, è necessario effettuare il provisioning di nodi di calcolo aggiuntivi

In database con Iperscalabilità, viene fornita la resilienza a livello di archiviazione. È necessario solo una replica per offrire resilienza. Quando la replica di calcolo non è attiva, viene creata automaticamente una nuova replica senza alcuna perdita di dati.

Se tuttavia c'è una sola replica, potrebbe essere necessario un po' di tempo per creare la cache locale nella nuova replica dopo il failover. Durante la fase di ricompilazione della cache, il database recupera i dati direttamente dai server di pagina, con una conseguente riduzione delle prestazioni di query e del numero di operazioni di I/O al secondo.

Per le app cruciali che richiedono disponibilità elevata, è necessario effettuare il provisioning di almeno 2 nodi di calcolo, incluso il nodo di calcolo primario (impostazione predefinita). In questo modo è disponibile un nodo di hot standby in caso di failover.

## <a name="data-size-and-storage-questions"></a>Domande sull'archiviazione e sulle dimensioni dei dati

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Quali sono le dimensioni massime del database supportate con il database SQL Hyperscale

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Quali sono le dimensioni del log delle transazioni con il livello Hyperscale

Il log delle transazioni con il livello Hyperscale è praticamente infinito. Non è necessario preoccuparsi di esaurire lo spazio di log in un sistema con velocità effettiva di log elevata. La frequenza di generazione dei log potrebbe tuttavia venire limitata per i carichi di lavoro impegnativi continui. La velocità di generazione log prolungato di picco è circa 100 MB/sec.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Il database temporaneo viene ridimensionato quando le dimensioni del database aumentano

Il database `tempdb` si trova nell'archiviazione SSD locale ed è configurato in base alle dimensioni di calcolo di cui è stato effettuato il provisioning. Il database `tempdb` è ottimizzato e organizzato per offrire il massimo dei vantaggi in termini di prestazioni. Le dimensioni di `tempdb` non possono essere configurate e vengono gestite automaticamente dal sottosistema di archiviazione.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Le dimensioni del database aumentano automaticamente oppure è necessario gestire le dimensioni dei file di dati

Le dimensioni del database aumentano automaticamente man mano che si inseriscono dati.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Quali sono le dimensioni iniziali o più piccole supportate dal database SQL Hyperscale

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>In base a quali incrementi aumentano le dimensioni del database

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Lo spazio di archiviazione nel database SQL Hyperscale è locale o remoto

Con il livello Hyperscale, i file di dati vengono archiviati in Archiviazione Standard di Azure. I dati vengono memorizzati nella cache nell'archiviazione SSD locale, in computer vicini ai nodi di calcolo. I nodi di calcolo hanno inoltre una cache nelle unità SSD locali e in memoria (pool di buffer e così via), per ridurre la frequenza di recupero dei dati dai nodi remoti.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>È possibile gestire o definire file o filegroup con il livello Hyperscale

No
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>È possibile effettuare il provisioning di un limite rigido per la crescita dei dati per il database

No

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Come vengono organizzati i file di dati con il database SQL Hyperscale

I file di dati vengono controllati dai server di pagina. Man mano che le dimensioni dei dati aumentano, vengono aggiunti file di dati e nodi dei server di pagina associati.

### <a name="is-database-shrink-supported"></a>È supportata la compattazione del database

No

### <a name="is-database-compression-supported"></a>È supportata la compressione del database

Yes

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>In presenza di una tabella di grandi dimensioni, i dati della tabella vengono distribuiti in più file di dati

Sì. Le pagine di dati associate a una determinata tabella possono venire distribuite in più file di dati, che fanno tutti parte dello stesso filegroup. SQL Server usa una [strategia di riempimento proporzionale](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) per distribuire i dati nei file di dati.

## <a name="data-migration-questions"></a>Domande sulla migrazione dei dati

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>È possibile spostare i database SQL di Azure esistenti al livello di servizio Hyperscale

Sì. È possibile spostare i database SQL di Azure esistenti al livello Hyperscale. Si tratta di una migrazione unidirezionale. Non è possibile spostare i database da Hyperscale a un altro livello di servizio. È consigliabile creare una copia dei database di produzione ed eseguire la migrazione al livello di servizio Hyperscale per il modello di verifica.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>È possibile spostare i database Hyperscale ad altre edizioni

No. A questo punto, è possibile spostare un database con scalabilità elevatissima per un altro livello di servizio.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune delle funzionalità di Database SQL di Azure non sono supportati in con Iperscalabilità, inclusi ma non limitata backup di conservazione a lungo termine. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.  Prevediamo di queste limitazioni temporanea.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>È possibile spostare il database di SQL Server locale o il database delle macchine virtuali di SQL Server al livello Hyperscale

Sì. È possibile usare tutte le tecnologie di migrazione esistenti per la migrazione al livello Hyperscale, tra cui BACPAC, replica transazionale, caricamento logico dei dati. Vedere anche [Servizio Migrazione del database di Azure](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Quanto è il tempo di inattività durante la migrazione da un ambiente locale o di macchine virtuali al livello Hyperscale e come è possibile ridurlo al minimo

Il tempo di inattività equivale a quello per la migrazione dei database a un database singolo nel database SQL di Azure. È possibile usare la [replica transazionale](replication-to-sql-database.md#data-migration-scenario
) per ridurre al minimo il tempo di inattività per la migrazione di database con dimensioni di pochi TB. Per i database di dimensioni molto grandi (più di 10 TB), può essere opportuno eseguire la migrazione dei dati usando ADF, Spark o altre tecnologie di spostamento dati.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Quanto tempo è necessario per spostare una determinata quantità di dati nel database SQL Hyperscale

È in grado di utilizzare 100 MB al secondo per dati nuovi o modificati con Iperscalabilità.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>È possibile leggere i dati dall'archiviazione BLOB ed eseguire il caricamento rapido (come Polybase e SQL Data Warehouse)

È possibile leggere i dati da Archiviazione di Azure e caricarli in un database Hyperscale (esattamente come è possibile fare con un normale database singolo). La tecnologia Polybase non è attualmente supportata nel database SQL di Azure. È possibile usare la tecnologia Polybase tramite [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) oppure eseguendo un processo Spark in [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) con il [connettore Spark per SQL](sql-database-spark-connector.md). Il connettore Spark per SQL supporta l'inserimento bulk.

Il modello di recupero con registrazione minima o con registrazione bulk non è supportato nel livello Hyperscale. Per fornire disponibilità elevata è richiesto il modello di recupero con registrazione completa. Il livello Hyperscale offre tuttavia una frequenza di inserimento dei dati migliore rispetto a un database SQL di Azure singolo, grazie alla nuova architettura di log.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Il database SQL Hyperscale permette il provisioning di più nodi per l'inserimento di grandi quantità di dati

No. Il database SQL Hyperscale è un'architettura SMP e non un'architettura a multielaborazione simmetrica o multimaster. È possibile solo creare più repliche per la scalabilità orizzontale dei carichi di lavoro di sola lettura.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Qual è la versione di SQL Server meno recente di cui sarà supportata la migrazione al database SQL Hyperscale

SQL Server 2005. Per altre informazioni, vedere [Eseguire la migrazione a un database singolo o in pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Per i problemi di compatibilità, vedere [Risoluzione dei problemi di compatibilità della migrazione di database](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Il database SQL Hyperscale supporta la migrazione da altre origini dati come Aurora, MySQL, Oracle, DB2 e altre piattaforme di database

Sì. Per origini dati diverse da SQL Server è necessaria la migrazione logica. È possibile usare il [Servizio Migrazione del database di Azure](../dms/dms-overview.md) per eseguire una migrazione logica.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Domande su continuità aziendale e ripristino di emergenza

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quali contratti di servizio vengono forniti per un database Hyperscale

Con il valore predefinito di quella primario e 1 replica secondaria leggibile, il contratto di servizio è pari al 99,95% di disponibilità.  Con più repliche, il contratto di servizio passa fino al 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>I backup dei database vengono gestiti automaticamente dal servizio di database SQL di Azure

Yes

### <a name="how-often-are-the-database-backups-taken"></a>Con che frequenza vengono eseguiti i backup dei database

Per i database SQL Hyperscale non sono previsti i tradizionali backup completi, differenziali e del log. Vengono invece acquisiti regolarmente snapshot dei file di dati e il log generato viene semplicemente conservato così com'è per il periodo di conservazione configurato o disponibile.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Il database SQL Hyperscale supporta il ripristino temporizzato

Yes

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Quali sono l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per le operazioni di backup/ripristino nel database SQL Hyperscale

L'obiettivo del punto di ripristino è 0 minuti. L'obiettivo del tempo di ripristino è inferiore a 10 minuti, indipendentemente dalle dimensioni del database. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>I backup di database di grandi dimensioni influiscono sulle prestazioni di calcolo del nodo primario

No. I backup vengono gestiti dal sottosistema di archiviazione e sfruttano gli snapshot di file. Non influiscono sul carico di lavoro dell'utente nel nodo primario.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>È possibile eseguire il ripristino geografico con un database SQL Hyperscale

Sì.  Ripristino geografico è completamente supportato.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>È possibile configurare la replica geografica con il database SQL Hyperscale

Attualmente non è possibile.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Viene eseguita la replica geografica dei nodi di calcolo secondari con il database SQL Hyperscale

Attualmente non è possibile.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>È possibile ripristinare un backup di un database SQL Hyperscale nel server locale o in SQL Server in una macchina virtuale

No. Il formato di archiviazione per i database Hyperscale è diverso da quello di SQL Server tradizionale e non è possibile controllare i backup o accedervi. Per estrarre i dati da un database SQL Hyperscale, usare il servizio di esportazione oppure gli script e BCP.

## <a name="cross-feature-questions"></a>Domande sulle funzionalità

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Dopo la migrazione al livello di servizio Hyperscale si perdono funzionalità o caratteristiche

Sì. Alcune delle funzionalità di Database SQL di Azure non sono supportati in con Iperscalabilità, tra cui backup con conservazione a lungo termine non limitata. Dopo la migrazione dei database al livello Hyperscale, queste funzionalità smettono di funzionare.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase funzionerà con il database SQL Hyperscale

No. La tecnologia Polybase non è supportata nel database SQL di Azure.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Le risorse di calcolo offrono supporto per R e Python

No. R and Python non sono supportati nel database SQL di Azure.

### <a name="are-the-compute-nodes-containerized"></a>I nodi di calcolo sono inclusi in contenitori

No. Il database si trova in un macchina virtuale di calcolo e non in un contenitore.

## <a name="performance-questions"></a>Domande sulle prestazioni

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Qual è la velocità effettiva di cui è possibile eseguire il push nelle operazioni di calcolo più grandi del database SQL Hyperscale

Abbiamo visto un 100 MB/sec coerente dei dati delle modifiche (generazione di dati di log delle transazioni)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Quante operazioni di I/O al secondo è possibile raggiungere nelle operazioni di calcolo più grandi del database SQL Hyperscale

Latenza dei / o e IOPS varia a seconda di modelli di carico di lavoro.  Se i dati che devono accedere sono locali da memorizzare nella cache di calcolo, sarà gli stessi modelli dei / o unità SSD locale.   

### <a name="does-my-throughput-get-affected-by-backups"></a>La velocità effettiva è influenzata dai backup

No. Il calcolo è separato dal livello di archiviazione per evitare un impatto sulle risorse di calcolo.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>La velocità effettiva cambia quando viene effettuato il provisioning di nodi di calcolo aggiuntivi

Perché lo spazio di archiviazione è condivisa e non c'è alcuna replica fisica diretta interazioni tra i nodi di calcolo primari e secondari, tecnicamente, la velocità effettiva nel nodo primario non influirà mediante l'aggiunta di nodi con scalabilità in lettura. Tuttavia, un carico di lavoro impegnativo continuo potrebbe venire limitato per consentire l'applicazione del log nei nodi secondari e permettere ai server di pagina di recuperare, evitando il peggioramento delle prestazioni di lettura nei nodi secondari.

## <a name="scalability-questions"></a>Domande sulla scalabilità

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Quanto tempo è necessario per ridimensionare un nodo di calcolo

Scalabilità di calcolo o verso il basso dovrebbe richiedere circa 5-10 minuti indipendentemente dalle dimensioni dei dati.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Il database è offline mentre vengono eseguite le operazioni di ridimensionamento

No. Le operazioni di ridimensionamento vengono eseguite online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Possono verificarsi interruzioni della connessione mentre vengono eseguite le operazioni di ridimensionamento

Le operazioni di ridimensionamento causano l'interruzione delle connessioni esistenti quando si verifica il failover nel nodo di calcolo con le dimensioni di destinazione. L'aggiunta di repliche di lettura non comporta interruzioni della connessione.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Il ridimensionamento dei nodi di calcolo è un'operazione automatica o attivata dall'utente finale

Attivata dall'utente finale. Non è un'operazione automatica.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Le dimensioni del database `tempb` aumentano in caso di aumento delle risorse di calcolo

Sì. Il database temporaneo viene ridimensionato automaticamente quando aumentano le risorse di calcolo.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>È possibile eseguire il provisioning più nodi di calcolo principale, ad esempio un sistema multi-master in cui più testine di calcolo primario possibile richiedere un livello superiore di concorrenza

No. Solo il nodo di calcolo primario accetta le richieste di lettura/scrittura. I nodi di calcolo secondari accettano solo le richieste di sola lettura.

## <a name="read-scale-questions"></a>Domande sulla scalabilità in lettura

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Di quanti nodi di calcolo secondari è possibile effettuare il provisioning

Creiamo 2 repliche per i database con scalabilità elevatissima per impostazione predefinita. Se si desidera modificare il numero di repliche, è possibile farlo usando [portale di Azure](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Come è possibile stabilire la connessione ai nodi di calcolo secondari

È possibile connettersi a questi nodi di calcolo aggiuntivi di sola lettura impostando l'argomento `ApplicationIntent` nella stringa di connessione su `readonly`. Tutte le connessioni contrassegnate con `readonly` vengono indirizzate automaticamente a uno dei nodi di calcolo di sola lettura aggiuntivi.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>È possibile creare un endpoint dedicato per la replica con scalabilità in lettura

No. È possibile connettersi solo alla scalabilità in lettura replica specificando `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Il sistema esegue il bilanciamento del carico intelligente per il carico di lavoro di lettura

No. Il carico di lavoro di sola lettura viene reindirizzato a una replica casuale di scalabilità in lettura.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>È possibile ridimensionare i nodi di calcolo secondari indipendentemente dal nodo di calcolo primario

No. I nodi di calcolo secondario vengono utilizzati anche per la disponibilità elevata, in modo che devono essere la stessa configurazione del database primario, nel caso di un failover.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Il database temporaneo viene ridimensionato in modo diverso per il nodo di calcolo primario e i nodi di calcolo secondari aggiuntivi

No. Il `tempdb` è configurato in base il provisioning di dimensioni di calcolo, i nodi di calcolo secondario hanno le stesse dimensioni come le principali risorse di calcolo.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>È possibile aggiungere indici e viste nei nodi di calcolo secondari

No. I database Hyperscale hanno un'archiviazione condivisa e ciò significa che tutti i nodi di calcolo vedono gli stessi indici, tabelle e viste. Se si vuole disporre di indici aggiuntivi ottimizzati per operazioni di lettura nei nodi secondari, è prima di tutto necessario aggiungerli nel nodo primario.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Quanto ritardo c'è tra il nodo di calcolo primario e quello secondario

Dal momento in cui viene eseguito il commit di una transazione nel nodo primario, a seconda della frequenza di generazione dei log, l'operazione può essere istantanea o richiedere pochi millisecondi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul livello di servizio con Iperscalabilità, vedere [livello di servizio su scala molto vasta](sql-database-service-tier-hyperscale.md).
