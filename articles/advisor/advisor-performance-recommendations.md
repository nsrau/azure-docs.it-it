---
title: Migliorare le prestazioni delle applicazioni di Azure con Azure Advisor
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443048"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Migliorare le prestazioni delle applicazioni di Azure con Azure Advisor

Grazie ai consigli di Azure Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Ridurre la durata DNS nel profilo di Gestione traffico per eseguire più rapidamente il failover a endpoint integri

Le [Impostazioni della durata (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) nel profilo di Gestione traffico consentono di specificare la velocità con cui viene cambiato endpoint, nel caso in cui un determinato endpoint smette di rispondere alle query. Ridurre i valori di durata (TTL) significa che i client verranno indirizzati più rapidamente agli endpoint funzionanti.

Azure Advisor identifica i profili di Gestione Traffico con una durata configurata maggiore e consiglia di configurare la durata (TTL) su 20 o 60 secondi a seconda che il profilo sia configurato o no per il [failover rapido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Migliorare le prestazioni del database con Advisor per database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure.Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre quindi consigli ideali per l'esecuzione del carico di lavoro tipico del database.

> [!NOTE]
> Per ottenere consigli, è necessario che un database sia stato in uso per circa una settimana, nel corso della quale deve essersi verificata attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per ulteriori informazioni su SQL Database Advisor, vedere [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Servizi app

Azure Advisor integra consigli sulle procedure consigliate per migliorare l'esperienza di Servizi app e scoprire le funzionalità della piattaforma pertinente. Esempi di consigli per Servizi app sono:
* Rilevamento delle istanze in cui le risorse di CPU o memoria vengono esaurite dai runtime delle app con opzioni di migrazione.
* Rilevamento delle istanze in cui la collocazione delle risorse come App Web e database può aumentare le prestazioni e ridurre il costo.

Per altre informazioni sui consigli per Servizi app, vedere [Procedure consigliate per Servizio app di Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Per evitare la limitazione di I/O dei dischi, usare il servizio Managed Disks

Advisor identificherà le macchine virtuali che appartengono a un account di archiviazione che sta per raggiungere la destinazione di scalabilità. Per effetto di questa condizione, le macchine virtuali sono soggette alla limitazione delle operazioni di I/O. Advisor consiglierà l'uso di Managed Disks per evitare che le prestazioni di queste macchine virtuali peggiorino.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali usando l'Archiviazione Premium

Advisor identifica le macchine virtuali con dischi standard e con un volume elevato di transazioni nell'account di archiviazione e consiglia l'aggiornamento a dischi Premium. 

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di Archiviazione Premium memorizzano i dati in unità SSD. Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di macchine virtuali che richiedono un numero elevato di operazioni di I/O al secondo.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Rimuovere l'asimmetria dei dati sulla tabella di SQL data warehouse per aumentare le prestazioni delle query

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati inutile o colli di bottiglia nelle risorse. Advisor rileverà un'asimmetria dei dati della distribuzione maggiore del 15% e consiglierà di ridistribuire i dati e rivedere le selezioni della chiave di distribuzione della tabella. Per altre informazioni sull'identificazione e la rimozione dell'asimmetria, vedere [risoluzione dei problemi di asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Creare o aggiornare le statistiche della tabella obsolete nella tabella di SQL data warehouse per aumentare le prestazioni delle query

Advisor identifica le tabelle che non hanno [statistiche della tabella](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) aggiornate e consiglia di crearle o di aggiornarle. Query Optimizer di SQL Data Warehouse usa le statistiche aggiornate per stimare la cardinalità o il numero di righe nel risultato della query al fine di creare un piano di query di qualità elevata per prestazioni ottimali.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Per migliorare le prestazioni delle query, è possibile passare a un livello superiore per ottimizzare l'utilizzo della cache nelle tabelle di SQL Data Warehouse.

Azure Advisor rileva se SQL Data Warehouse ha una percentuale alta di utilizzo della cache e una percentuale bassa di riscontri. Questa condizione indica una rimozione dalla cache elevata e ciò può influire negativamente sulle prestazioni di SQL Data Warehouse. Advisor suggerisce di aumentare le prestazioni di SQL Data Warehouse per assicurarsi di allocare una capacità sufficiente di cache per il carico di lavoro.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Convertire le tabelle di SQL Data Warehouse in tabelle replicate per aumentare le prestazioni delle query

Advisor identifica le tabelle che non sono tabelle replicate, ma che potrebbero trarre vantaggio dalla conversione e suggerisce di convertire queste tabelle. I consigli si basano sulle dimensioni, sul numero di colonne, sul tipo di distribuzione e sul numero di partizioni della tabella di SQL Data Warehouse. Il consiglio per il contesto potrebbe includere euristiche aggiuntive. Per altre informazioni su come viene determinato questo consiglio, vedere [Consigli di SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Eseguire la migrazione dell'account di archiviazione ad Azure Resource Manager per ottenere tutte le funzionalità di Azure più recenti

Eseguire la migrazione del modello di distribuzione dell'account di archiviazione ad Azure Resource Manager (Resource Manager) per usufruire delle distribuzioni di modelli, di maggiori opzioni di sicurezza e della possibilità di eseguire l'aggiornamento a un account GPv2 per l'uso delle funzionalità più recenti di Archiviazione di Azure. Advisor identifica eventuali account di archiviazione autonomi che usano il modello di distribuzione classica e consiglia di eseguire la migrazione al modello di distribuzione di Resource Manager.

> [!NOTE]
> Gli avvisi classici in Monitoraggio di Azure sono stati ritirati nell'agosto 2019.Classic alerts in Azure Monitor have been retired in August 2019. È consigliabile aggiornare l'account di archiviazione classico all'uso di Resource Manager in modo da mantenere la funzionalità di avviso con la nuova piattaforma. Per altre informazioni, vedere [Disattivazione avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioneDesign your storage accounts to prevent hitting the maximum subscription limit

Un'area di Azure può supportare un massimo di 250 account di archiviazione per ogni sottoscrizione. Una volta raggiunto il limite, non sarà possibile creare altri account di archiviazione in tale combinazione area/sottoscrizione. Advisor controllerà le sottoscrizioni e presenterà consigli sulle superfici per la progettazione di un numero inferiore di account di archiviazione per quelli vicini al raggiungimento del limite massimo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Ottimizzare le prestazioni dei server Azure MySQL, Azure PostgreSQL e Azure MariaDBOptimize the performance of your Azure MySQL, Azure PostgreSQL, and Azure MariaDB servers 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Correggere la pressione della CPU dei server MySQL, Azure PostgreSQL e Azure MariaDB con colli di bottiglia della CPUFix the CPU pressure of your Azure MySQL, Azure PostgreSQL, and Azure MariaDB servers with CPU bottlenecks
Un utilizzo molto elevato della CPU in un periodo prolungato può causare un rallentamento delle prestazioni delle query per il carico di lavoro. L'aumento delle dimensioni della CPU consentirà di ottimizzare il runtime delle query di database e migliorare le prestazioni complessive. Azure Advisor identificherà i server con un utilizzo elevato della CPU che eseguono probabilmente carichi di lavoro vincolati alla CPU e consiglia di ridimensionare il calcolo.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Ridurre i vincoli di memoria nei server Azure MySQL, Azure PostgreSQL e Azure MariaDB o passare a uno SKU ottimizzato per la memoria
Un rapporto di hit della cache basso può comportare un miglioramento delle prestazioni delle query e un aumento delle operazioni di I/O al secondo. Ciò potrebbe essere dovuto a un piano di query non valido o all'esecuzione di un carico di lavoro che richiede un utilizzo intensivo della memoria. Correggere il piano di query o [aumentare la memoria](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) del database di Azure per il server di database PostgreSQL, il server di database MySQL di Azure o il server MariaDB di Azure consente di ottimizzare l'esecuzione del carico di lavoro del database. Azure Advisor identifica i server interessati a causa di questa varianza del pool di buffer elevato e consiglia di correggere il piano di query, passare a uno SKU superiore con più memoria o aumentare le dimensioni di archiviazione per ottenere più operazioni di I/O al secondo.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Usare una replica di lettura di Azure MySQL o Azure PostgreSQL per aumentare la scalabilità orizzontale delle letture per carichi di lavoro con utilizzo intensivo di letturaUse a Azure MySQL or Azure PostgreSQL Read Replica to scale out reads for read intensive workloads
Azure Advisor sfrutta l'euristica basata sul carico di lavoro, ad esempio il rapporto tra letture e scritture nel server negli ultimi sette giorni per identificare carichi di lavoro che richiedono un utilizzo intensivo della lettura. Il database di Azure per la risorsa PostgreSQL o il database di Azure per la risorsa MySQL con un rapporto di lettura/scrittura molto elevato può causare conflitti di CPU e/o memoria che causano un rallentamento delle prestazioni delle query. L'aggiunta di una [replica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) consentirà di ridimensionare le letture al server di replica, evitando vincoli di CPU e/o memoria nel server primario. Advisor identificherà i server con carichi di lavoro ad alta intensità di lettura e consiglia di aggiungere una [replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) di lettura per eseguire l'offload di alcuni dei carichi di lavoro di lettura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Ridimensionare il server Azure MySQL, Azure PostgreSQL o Azure MariaDB a uno SKU superiore per evitare vincoli di connessioneScale your Azure MySQL, Azure PostgreSQL, or Azure MariaDB server to a higher SKU to prevent connection constraints
Ogni nuova connessione al server di database occupa una certa quantità di memoria. Le prestazioni del server di database peggiorano se le connessioni al server non riescono a causa di un [limite superiore](https://docs.microsoft.com/azure/postgresql/concepts-limits) in memoria. Azure Advisor identificherà i server in esecuzione con molti errori di connessione e consiglia di aggiornare i limiti delle connessioni del server per fornire più memoria al server aumentando la scalabilità del calcolo o usando SKU ottimizzati per la memoria, che hanno più calcolo per core.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Ridimensionare la cache a dimensioni o SKU diversi per migliorare le prestazioni della cache e dell'applicazioneScale your Cache to a different size or SKU to improve Cache and application performance

Le istanze della cache offrono prestazioni ottimali quando non vengono eseguite con una pressione elevata della memoria, un carico elevato del server o un'elevata larghezza di banda di rete, il che potrebbe causare una mancata risposta, la perdita di dati o la mancata disponibilità. Advisor identificherà le istanze della cache in queste condizioni e consiglia di applicare le procedure consigliate per ridurre il utilizzo della memoria, il carico del server o la larghezza di banda di rete o la scalabilità a dimensioni o SKU diversi con una maggiore capacità.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Aggiungere aree con traffico all'account di Azure Cosmos DBAdd regions with traffic to your Azure Cosmos DB account

Advisor rileverà gli account di Azure Cosmos DB con traffico proveniente da un'area che non è attualmente configurata e consiglia di aggiungere tale area. Ciò migliorerà la latenza per le richieste provenienti da tale area e garantirà la disponibilità in caso di interruzioni dell'area. [Altre informazioni sulla distribuzione globale dei dati con Azure Cosmos DBLearn more about global data distribution with Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configurare i criteri di indicizzazione del database Cosmos di Azure con percorsi inclusi o esclusi dal cliente

Azure Advisor identificherà i contenitori del database Cosmos che usano i criteri di indicizzazione predefiniti, ma potrebbero trarre vantaggio da criteri di indicizzazione personalizzati basati sul modello di carico di lavoro. I criteri di indicizzazione predefiniti indicizzano tutte le proprietà, ma l'utilizzo di un criterio di indicizzazione personalizzato con percorsi inclusi o esclusi espliciti utilizzati nei filtri di query può ridurre le RU e lo spazio di archiviazione utilizzati per l'indicizzazione. [Ulteriori informazioni sulla modifica dei criteri di indice](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurare le dimensioni della pagina di query di Azure Cosmos DB (MaxItemCount) su -1 

Azure Advisor identificherà i contenitori del database Cosmos di Azure che usano le dimensioni della pagina della query pari a 100 e consiglia di usare una dimensione di pagina pari a -1 per analisi più rapide. [Ulteriori informazioni sul numero massimo di elementi](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Prestazioni**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione a Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulla disponibilità elevata](advisor-high-availability-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Consigli per l'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
