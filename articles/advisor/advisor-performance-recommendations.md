---
title: Migliorare le prestazioni delle app di Azure con Advisor
description: Usare le raccomandazioni sulle prestazioni in Azure Advisor per migliorare la velocità e la velocità di risposta delle applicazioni cruciali per l'azienda.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 6a008411d4422853e6a98fad59bd4519b42a9548
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308677"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Migliorare le prestazioni delle applicazioni Azure usando Azure Advisor

I suggerimenti sulle prestazioni in Azure Advisor consentono di migliorare la velocità e la velocità di risposta delle applicazioni aziendali cruciali. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Ridurre la durata (TTL) DNS nel profilo di gestione traffico per eseguire il failover a endpoint integri più velocemente

È possibile usare [le impostazioni TTL (time-to-Live)](../traffic-manager/traffic-manager-performance-considerations.md) nel profilo di gestione traffico di Azure per specificare la velocità con cui cambiare gli endpoint se un determinato endpoint smette di rispondere alle query. Se si riducono i valori TTL, i client verranno indirizzati a endpoint operativi più velocemente.

Azure Advisor identifica i profili di gestione traffico con un valore TTL più lungo configurato. Si consiglia di configurare la durata (TTL) su 20 secondi o 60 secondi, a seconda che il profilo sia configurato per [failover rapido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Migliorare le prestazioni del database tramite SQL Advisor per database (temporaneamente disabilitato)

Azure Advisor offre una vista coerente e consolidata delle raccomandazioni per tutte le risorse di Azure. Si integra con SQL Advisor per database per fornire indicazioni per migliorare le prestazioni dei database. SQL Advisor per database valuta le prestazioni dei database analizzando la cronologia di utilizzo. Offre quindi consigli più adatti per l'esecuzione del carico di lavoro tipico del database.

> [!NOTE]
> Prima di poter ottenere consigli, è necessario che il database sia in uso per circa una settimana e che, entro tale settimana, sia necessaria un'attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per ulteriori informazioni, vedere [SQL Advisor per database](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Aggiornare la libreria client di archiviazione alla versione più recente per migliorare l'affidabilità e le prestazioni

La versione più recente di storage Client Library SDK contiene correzioni per i problemi segnalati dai clienti e identificati in modo proattivo tramite il processo di controllo di qualità. La versione più recente offre anche l'ottimizzazione dell'affidabilità e delle prestazioni insieme a nuove funzionalità che consentono di migliorare l'esperienza complessiva con l'uso di archiviazione di Azure. Advisor fornisce consigli e passaggi necessari per eseguire l'aggiornamento alla versione più recente dell'SDK se si usa una versione non aggiornata. Le indicazioni sono per le lingue supportate: C++ e .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Servizi app

Azure Advisor integra i consigli per migliorare l'esperienza del servizio app e individuare le funzionalità della piattaforma pertinenti. Esempi di consigli sul servizio app sono:
* Rilevamento delle istanze in cui le risorse di memoria o CPU sono esaurite dai runtime delle app, con opzioni di mitigazione.
* Rilevamento delle istanze in cui la condivisione percorso risorse come app Web e database può migliorare le prestazioni e ridurre i costi.

Per ulteriori informazioni, vedere [procedure consigliate per il servizio app Azure](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Usare Managed disks per evitare la limitazione di I/O del disco

Advisor identifica le macchine virtuali che appartengono a un account di archiviazione che raggiunge la propria destinazione di scalabilità. Per effetto di questa condizione, le macchine virtuali sono soggette alla limitazione delle operazioni di I/O. Advisor consiglia di usare Managed disks per evitare un calo delle prestazioni.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali usando l'Archiviazione Premium

Advisor identifica le macchine virtuali con dischi standard con un volume elevato di transazioni nell'account di archiviazione. Si consiglia di eseguire l'aggiornamento a dischi Premium. 

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di archiviazione Premium archiviano i dati in unità SSD (Solid-State Drive). Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione di tutti i dischi delle macchine virtuali che richiedono un elevato livello di IOPS ad archiviazione Premium.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Rimuovere l'asimmetria dei dati nelle tabelle di analisi delle sinapsi di Azure per migliorare le prestazioni delle query

L'asimmetria dei dati può causare lo spostamento dei dati o i colli di bottiglia delle risorse non necessari quando si esegue il carico di lavoro Advisor rileva l'asimmetria dei dati di distribuzione superiore al 15%. È consigliabile ridistribuire i dati e rivedere le selezioni della chiave di distribuzione della tabella. Per altre informazioni sull'identificazione e la rimozione dell'asimmetria, vedere [risoluzione dei problemi di asimmetria](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Creare o aggiornare le statistiche delle tabelle obsolete nelle tabelle di analisi delle sinapsi di Azure per migliorare le prestazioni delle query

Advisor identifica le tabelle che non dispongono di [statistiche di tabella](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) aggiornate e consiglia di creare o aggiornare le statistiche. Il Query Optimizer in Azure sinapsi Analytics usa le statistiche aggiornate per stimare la cardinalità o il numero di righe nei risultati della query. Queste stime consentono al Query Optimizer di creare un piano di query per offrire le prestazioni più rapide.

## <a name="improve-mysql-connection-management"></a>Migliorare la gestione delle connessioni a MySQL

L'analisi di Advisor può indicare che l'applicazione che si connette a un server MySQL potrebbe non gestire le connessioni in modo efficiente. Questa condizione può causare un consumo di risorse superfluo e una latenza dell'applicazione complessiva superiore. Per migliorare la gestione delle connessioni, è consigliabile ridurre il numero di connessioni di breve durata ed eliminare le connessioni inattive non necessarie. È possibile apportare questi miglioramenti configurando una connessione lato server pool, ad esempio ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Scalabilità verticale per ottimizzare l'utilizzo della cache nelle tabelle di analisi delle sinapsi di Azure per migliorare le prestazioni delle query

Azure Advisor rileva se le tabelle di Azure sinapsi Analytics hanno una percentuale di utilizzo elevato della cache e una percentuale di hit bassa. Questa condizione indica una rimozione della cache elevata, che può influire sulle prestazioni dell'istanza di Azure sinapsi Analytics. Advisor consiglia di eseguire la scalabilità verticale dell'istanza di Azure sinapsi Analytics per assicurarsi di allocare una capacità di cache sufficiente per il carico di lavoro.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Convertire le tabelle di analisi delle sinapsi di Azure in tabelle replicate per migliorare le prestazioni delle query

Advisor identifica le tabelle che non sono tabelle replicate, ma che potrebbero trarre vantaggio dalla conversione. Si consiglia di convertire queste tabelle. Le indicazioni sono basate su:
- Dimensione della tabella replicata. 
- Numero di colonne. 
- Tipo di distribuzione della tabella. 
- Il numero di partizioni nella tabella di analisi delle sinapsi di Azure. 

È possibile che venga fornita un'euristica aggiuntiva nell'indicazione del contesto. Per altre informazioni su come viene determinata questa raccomandazione, vedere le [raccomandazioni di Azure sinapsi Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Eseguire la migrazione dell'account di archiviazione a Azure Resource Manager per ottenere le funzionalità più recenti di Azure

Eseguire la migrazione del modello di distribuzione dell'account di archiviazione a Azure Resource Manager per sfruttare i vantaggi di:
- Distribuzioni modello.
- Opzioni di sicurezza aggiuntive.
- Possibilità di eseguire l'aggiornamento a un account GPv2 in modo da poter usare le funzionalità di archiviazione di Azure più recenti. 

Advisor identifica gli account di archiviazione autonomi che usano il modello di distribuzione classica e consiglia di eseguire la migrazione al modello di distribuzione Gestione risorse.

> [!NOTE]
> Gli avvisi classici in monitoraggio di Azure sono stati ritirati nel 2019 agosto. È consigliabile aggiornare l'account di archiviazione classico all'uso di Resource Manager in modo da mantenere la funzionalità di avviso con la nuova piattaforma. Per ulteriori informazioni, vedere il [ritiro di avvisi classici](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioni

Un'area di Azure supporta un massimo di 250 di account di archiviazione per sottoscrizione. Una volta raggiunto questo limite, non sarà possibile creare account di archiviazione in tale combinazione di area/sottoscrizione. Advisor controlla le sottoscrizioni e fornisce consigli per la progettazione di un numero inferiore di account di archiviazione per qualsiasi sottoscrizione/area vicina al raggiungimento del limite massimo.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Prendere in considerazione la possibilità di aumentare le dimensioni dello SKU del gateway VPN per risolvere un uso elevato di P2S

Ogni SKU del gateway VPN di Azure può supportare solo un numero specificato di connessioni P2S simultanee. Se il numero di connessioni è prossimo al limite del gateway, i tentativi aggiuntivi di connessione potrebbero non riuscire. Se si aumentano le dimensioni del gateway, sarà possibile supportare più utenti P2S simultanei. Advisor fornisce consigli e istruzioni per aumentare le dimensioni del gateway.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Valutare la possibilità di aumentare le dimensioni dello SKU del gateway VPN per soddisfare la CPU elevata

In un carico di traffico elevato, il gateway VPN potrebbe eliminare i pacchetti a causa di una CPU elevata. Provare a aggiornare lo SKU del gateway VPN. L'aumento delle dimensioni del gateway VPN garantisce che le connessioni non vengano eliminate a causa della CPU elevata. Advisor fornisce consigli per risolvere in modo proattivo questo problema. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Aumentare le dimensioni del batch durante il caricamento per massimizzare la velocità effettiva di carico, la compressione dei dati e le prestazioni delle query

Advisor rileva se è possibile aumentare le prestazioni e la velocità effettiva del carico aumentando le dimensioni del batch durante il caricamento nel database. È possibile prendere in considerazione l'uso dell'istruzione COPY. Se non è possibile usare l'istruzione COPY, provare ad aumentare le dimensioni del batch quando si usa il caricamento di utilità come l'API SQLBulkCopy o BCP. Una regola generale corretta consiste nell'usare una dimensione del batch compresa tra 100.000 e 1 milione righe. L'aumento delle dimensioni del batch aumenterà la velocità effettiva del carico, la compressione dei dati e le prestazioni delle query

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Condividere il percorso dell'account di archiviazione nella stessa area per ridurre al minimo la latenza durante il caricamento

Advisor rileva se si sta eseguendo il caricamento da un'area diversa dal pool SQL dedicato. Prendere in considerazione il caricamento da un account di archiviazione che si trova nella stessa area del pool SQL dedicato per ridurre al minimo la latenza durante il caricamento dei dati. Questa modifica consente di ridurre al minimo la latenza e migliorare le prestazioni di caricamento.

## <a name="use-a-supported-kubernetes-version"></a>Usa una versione di Kubernetes supportata

Advisor rileva le versioni non supportate di Kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Ottimizzare le prestazioni del database di Azure per MySQL, database di Azure per PostgreSQL e database di Azure per i server MariaDB

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Correzione della pressione della CPU del database di Azure per MySQL, database di Azure per PostgreSQL e database di Azure per i server MariaDB con colli di bottiglia della CPU
Un utilizzo elevato della CPU in un periodo prolungato può causare un rallentamento delle prestazioni delle query per il carico di lavoro. L'aumento delle dimensioni della CPU consente di ottimizzare la fase di esecuzione delle query del database e migliorare le prestazioni complessive. Advisor identifica i server con un utilizzo elevato della CPU che probabilmente esegue carichi di lavoro vincolati alla CPU e consiglia la scalabilità del calcolo.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Ridurre i vincoli di memoria nel database di Azure per MySQL, database di Azure per PostgreSQL e database di Azure per i server MariaDB oppure passare a uno SKU con ottimizzazione per la memoria
Una percentuale di riscontri nella cache ridotta può comportare un rallentamento delle prestazioni delle query e maggiore IOPS. Questa condizione potrebbe essere causata da un piano di query non valido o da un carico di lavoro con utilizzo intensivo della memoria. Correggendo il piano di query o [aumentando la memoria](../postgresql/concepts-pricing-tiers.md) di database di Azure per PostgreSQL, database di Azure per MySQL o database di Azure per il server MariaDB, è possibile ottimizzare l'esecuzione del carico di lavoro del database. Azure Advisor identifica i server interessati da questa varianza del pool di buffer elevata. Si consiglia di eseguire una di queste operazioni: 
- Correzione del piano di query
- Passare a uno SKU con una maggiore quantità di memoria 
- Aumentare le dimensioni di archiviazione per ottenere un numero maggiore di IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Usare una replica di lettura di database di Azure per MySQL o database di Azure per PostgreSQL per la scalabilità orizzontale delle letture per carichi di lavoro a elevato utilizzo di lettura
Advisor usa l'euristica basata sul carico di lavoro, ad esempio il rapporto tra le letture e le scritture nel server negli ultimi sette giorni per identificare i carichi di lavoro a elevato utilizzo di lettura. Una risorsa database di Azure per PostgreSQL o database di Azure per MySQL con un rapporto di lettura/scrittura elevato può comportare conflitti di CPU o di memoria e causare un rallentamento delle prestazioni delle query. L'aggiunta di una [replica](../postgresql/howto-read-replicas-portal.md) consente di scalare in orizzontale le letture nel server di replica e di evitare vincoli di CPU o di memoria nel server primario. Advisor identifica i server con carichi di lavoro a elevato utilizzo di lettura e consiglia di aggiungere una [replica di lettura](../postgresql/concepts-read-replicas.md) per l'offload di alcuni carichi di lavoro di lettura.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Ridimensionare il database di Azure per MySQL, database di Azure per PostgreSQL o database di Azure per il server MariaDB in uno SKU superiore per evitare vincoli di connessione
Ogni nuova connessione al server di database occupa la memoria. Le prestazioni del server di database peggiorano se le connessioni al server hanno esito negativo a causa di un [limite massimo](../postgresql/concepts-limits.md) di memoria. Azure Advisor identifica i server che eseguono con molti errori di connessione. Si consiglia di aggiornare i limiti di connessione del server per fornire una maggiore quantità di memoria al server eseguendo una delle operazioni seguenti:
- Aumentare il livello di calcolo. 
- Usare SKU con ottimizzazione per la memoria, che hanno più risorse di calcolo per core.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Ridimensionare la cache a una dimensione o a uno SKU diverso per migliorare le prestazioni della cache e dell'applicazione

Le istanze della cache offrono prestazioni ottimali quando non vengono eseguite con un utilizzo elevato della memoria, un carico elevato del server o una larghezza di banda di rete elevata. Queste condizioni possono causare la mancata risposta, l'esperienza di perdita di dati o l'indisponibilità. In queste condizioni, Advisor identifica le istanze della cache. Si consiglia di eseguire una di queste operazioni:
- Applicare le procedure consigliate per ridurre la quantità di memoria, il carico del server o la larghezza di banda di rete.
- Ridimensionare a dimensioni o SKU diversi con maggiore capacità.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Aggiungere aree con traffico all'account di Azure Cosmos DB

Advisor rileva Azure Cosmos DB account con traffico da un'area che non è attualmente configurata. È consigliabile aggiungere tale area. Questa operazione migliora la latenza per le richieste provenienti da tale area e garantisce la disponibilità in caso di interruzioni dell'area. [Scopri di più sulla distribuzione di dati globali con Azure Cosmos DB.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Configurare i criteri di indicizzazione Azure Cosmos DB usando percorsi personalizzati inclusi o esclusi

Advisor identifica Azure Cosmos DB contenitori che usano i criteri di indicizzazione predefiniti ma possono trarre vantaggio da criteri di indicizzazione personalizzati. Questa determinazione si basa sul modello del carico di lavoro. Il criterio di indicizzazione predefinito indicizza tutte le proprietà. Un criterio di indicizzazione personalizzato con percorsi inclusi o esclusi espliciti utilizzati nei filtri di query può ridurre le UR e l'archiviazione utilizzata per l'indicizzazione. [Altre informazioni sulla modifica dei criteri di indice.](/azure/cosmos-db/index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Impostare le dimensioni della pagina della query Azure Cosmos DB (MaxItemCount) su-1 

Azure Advisor identifica Azure Cosmos DB contenitori che utilizzano una dimensione della pagina di query di 100. Si consiglia di utilizzare una dimensione di pagina pari a-1 per analisi più veloci. [Altre informazioni su MaxItemCount.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Provare a usare la funzionalità Scritture accelerate nel cluster HBase per migliorare le prestazioni del cluster
Azure Advisor analizza i registri di sistema negli ultimi 7 giorni e identifica se il cluster ha riscontrato gli scenari seguenti:
1. Latenza elevata dell'ora di sincronizzazione log write-ahead 
2. Numero elevato di richieste di scrittura (almeno 3 in un'ora di oltre 1000 avg_write_requests/second/node)

Queste condizioni indicano che il cluster è soggetto a latenze di scrittura elevate. Il problema potrebbe essere dovuto a carichi di lavoro intensivi eseguiti nel cluster. Per migliorare le prestazioni del cluster, è consigliabile usare la funzionalità di scrittura accelerata fornita da Azure HDInsight HBase. La funzionalità scritture accelerate per i cluster HDInsight Apache HBase collega i dischi gestiti da SSD Premium a ogni RegionServer (nodo di lavoro) invece di usare l'archiviazione cloud. Di conseguenza, offre bassa latenza di scrittura e una migliore resilienza per le applicazioni. Per altre informazioni su questa funzionalità, vedere [altre informazioni](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Esaminare la cache della tabella Esplora dati di Azure-periodo (criteri) per migliorare le prestazioni (anteprima)
Questo consiglio fa emergere le tabelle di Esplora dati di Azure con un numero più elevato di query che si riferiscono al periodo (criteri) configurato per la permanenza nella cache. Verranno visualizzate le 10 tabelle principali per percentuale di cache che accedono ai dati non appartenenti alla cache. Azione consigliata per migliorare le prestazioni del cluster: Limitare le query nella tabella all'intervallo di tempo minimo necessario (entro i criteri definiti). In alternativa, se servono i dati dell'intero intervallo di tempo, aumentare il periodo della cache al valore consigliato.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Migliorare le prestazioni ottimizzando il dimensionamento della tabella temporanea MySQL
L'analisi di Advisor indica che è possibile che il server MySQL incorra un sovraccarico di I/O non necessario a causa di impostazioni di parametri low-table temporanee. Ciò può comportare transazioni non necessarie basate su disco e prestazioni ridotte. Per ridurre il numero di transazioni basate su disco, è consigliabile aumentare i valori del parametro "tmp_table_size' and 'max_heap_table_size". [Altre informazioni](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Distribuire i dati nel gruppo di server per la distribuzione del carico di lavoro tra i nodi
Advisor identifica i gruppi di server in cui i dati non sono stati distribuiti, ma rimane sul coordinatore. In base a questa operazione, Advisor consiglia di distribuire i dati nei nodi del ruolo di lavoro per i gruppi di server per i vantaggi della scalabilità completa (CITUS). Ciò consente di migliorare le prestazioni di esecuzione delle query utilizzando la risorsa di ogni nodo nel gruppo di server. [Altre informazioni](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Migliorare l'esperienza utente e la connettività distribuendo VM più vicine al percorso di distribuzione di desktop virtuale Windows
Si è riscontrato che le macchine virtuali si trovano in un'area diversa o lontana da quella da cui si connettono gli utenti, tramite Desktop virtuale Windows. Questo può comportare tempi di risposta della connessione più lunghi e influisce sull'esperienza utente complessiva in Desktop virtuale Windows. Quando si creano macchine virtuali per i pool di host, è consigliabile tentare di usare un'area più vicina all'utente. Una maggiore vicinanza assicura un'affidabilità costante del servizio Desktop virtuale Windows e una migliore qualità complessiva dell'esperienza. [Altre informazioni sulla latenza di connessione](../virtual-desktop/connection-latency.md)sono disponibili qui.

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Eseguire l'aggiornamento alla versione più recente dell'SDK dello strumento di lettura immersiva
In questa sottoscrizione sono state rilevate risorse che usano versioni obsolete dell'SDK dello strumento di lettura immersiva. L'uso della versione più recente dell'SDK dello strumento di lettura immersiva offre una maggiore sicurezza, prestazioni più elevate e un set di funzionalità ampliate per la personalizzazione e il miglioramento dell'esperienza di integrazione.
Scopri di più su [immersive Reader SDK](../cognitive-services/immersive-reader/index.yml).


## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor selezionare la scheda **prestazioni** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Punteggio di Advisor](azure-advisor-score.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Consigli sull'affidabilità di Advisor](advisor-high-availability-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](/rest/api/advisor/)