---
title: Migliorare le prestazioni delle applicazioni di Azure con Azure Advisor | Microsoft Docs
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254659"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Migliorare le prestazioni delle applicazioni di Azure con Azure Advisor

Grazie ai consigli di Azure Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Ridurre la durata DNS nel profilo di Gestione traffico per eseguire più rapidamente il failover a endpoint integri

Le [Impostazioni della durata (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) nel profilo di Gestione traffico consentono di specificare la velocità con cui viene cambiato endpoint, nel caso in cui un determinato endpoint smette di rispondere alle query. Ridurre i valori di durata (TTL) significa che i client verranno indirizzati più rapidamente agli endpoint funzionanti.

Azure Advisor identifica i profili di Gestione Traffico con una durata configurata maggiore e consiglia di configurare la durata (TTL) su 20 o 60 secondi a seconda che il profilo sia configurato o no per il [failover rapido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Migliorare le prestazioni del database con Advisor per database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure. Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre quindi consigli ideali per l'esecuzione del carico di lavoro tipico del database.

> [!NOTE]
> Per ottenere consigli, è necessario che un database sia stato in uso per circa una settimana, nel corso della quale deve essersi verificata attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per altre informazioni su Advisor per database SQL, vedere [Advisor per database SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

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
> Il ritiro degli avvisi classici in Monitoraggio di Azure è previsto per il mese di giugno 2019. È consigliabile aggiornare l'account di archiviazione classico all'uso di Resource Manager in modo da mantenere la funzionalità di avviso con la nuova piattaforma. Per altre informazioni, vedere [Disattivazione avvisi classici](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioni

Un'area di Azure può supportare un massimo di 250 account di archiviazione per ogni sottoscrizione. Una volta raggiunto il limite, non sarà possibile creare qualsiasi altri account di archiviazione in tale combinazione di area o una sottoscrizione. Advisor consente di rilevare le sottoscrizioni e consigli della superficie per progettare un minor numero degli account di archiviazione per qualsiasi che siano per raggiungere il limite massimo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Ottimizzare le prestazioni dei server MySQL di Azure e Azure PostgreSQL Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Correggere la pressione della CPU del server MySQL di Azure e Azure PostgreSQL Azure MariaDB con i colli di bottiglia della CPU
Un utilizzo molto elevato della CPU per un periodo prolungato può provocare prestazioni di query lente per il carico di lavoro. Aumento delle dimensioni della CPU sarà consentono di ottimizzare il runtime di query di database e migliorare le prestazioni complessive. Azure Advisor identifica i server con un utilizzo elevato della CPU che è probabile che eseguono carichi di lavoro della CPU vincolata e consigliabile ridimensionare le risorse di calcolo.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Ridurre i vincoli di memoria nei server Azure MySQL, PostgreSQL di Azure e Azure MariaDB o verso una memoria con ottimizzazione per la SKU
Una percentuale di riscontri cache basso può comportare un maggiore IOPS e rallentamento delle prestazioni di query. Probabilmente a causa di un piano di query non valida o l'esecuzione di un carico di lavoro con utilizzo intensivo di memoria. Correggere il piano di query oppure [aumentando la memoria](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) del Database di Azure per server di database PostgreSQL, il server di database di Azure MySQL o MariaDB Azure server consente di ottimizzare l'esecuzione del carico di lavoro database. Azure Advisor identifica i server interessati a causa di questa varianza di pool di buffer è elevato e consiglia una risoluzione il piano di query, lo spostamento di uno SKU superiore con più memoria o aumento delle dimensioni di archiviazione per ottenere una quantità maggiore di IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Usare un MySQL di Azure o Azure PostgreSQL lettura della Replica per aumentare le operazioni di lettura per la lettura dei carichi di lavoro con utilizzo intensivo
Azure Advisor sfrutta l'euristica in base al carico di lavoro, ad esempio la percentuale di letture e scritture nel server negli ultimi sette giorni per identificare i carichi di lavoro a elevato utilizzo di lettura. Il database di Azure per PostgreSQL risorsa o un database di Azure per MySQL risorse con un rapporto molto elevato di operazioni di lettura/scrittura può comportare conflitti di CPU e/o memoria portano a rallentare le prestazioni delle query. Aggiunta di un [replica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) aiuterà a scalabilità orizzontale di letture e del server di replica, impedendo i vincoli di CPU e/o memoria nel server primario. Advisor identifica i server con tali carichi di lavoro elevati di operazioni di lettura intense e consiglia di aggiungere un [replica di lettura](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) per trasferire una parte i carichi di lavoro di lettura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Ridimensionare il server Azure MySQL, PostgreSQL di Azure o Azure MariaDB a uno SKU superiore per evitare che i vincoli di connessione
Ogni nuova connessione al server di database occupa una certa quantità di memoria. Comporta una riduzione delle prestazioni del server di database se le connessioni al server hanno esito negativo a causa di un [limite massimo](https://docs.microsoft.com/azure/postgresql/concepts-limits) in memoria. Azure Advisor verranno identificati i server che eseguono con molti errori di connessione e consigliabile eseguire l'aggiornamento dei limiti di connessioni del server per fornire maggiore quantità di memoria al server di scalabilità verticale di calcolo o utilizzando SKU con ottimizzazione per la memoria, che hanno maggiore capacità di calcolo per ogni core.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Ridimensionare la Cache a una dimensione diversa o lo SKU per migliorare la Cache e le prestazioni dell'applicazione

Le istanze di cache hanno prestazioni migliori quando non viene eseguito con utilizzo elevato di memoria, carico del server elevato o della larghezza di banda di rete elevata che potrebbe provocare la non rispondere, riscontrare la perdita di dati o non saranno più disponibili. Advisor identificherà le istanze di Cache in queste condizioni e consigliabile applicare le procedure consigliate per ridurre la pressione della memoria, carico del server o della larghezza di banda di rete oppure la scalabilità per uno SKU o dimensioni diverse con maggiore capacità.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Aggiungere aree geografiche con traffico al proprio account Azure Cosmos DB

Advisor rileverà gli account Azure Cosmos DB che include il traffico da un'area in cui non è attualmente configurata e consiglia di aggiungere tale area. Questo consente di migliorare la latenza per le richieste provenienti dalla stessa area e assicura la disponibilità in caso di interruzioni nell'area. [Altre informazioni sulla distribuzione globale dei dati con Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Azure Cosmos DB Configura criteri di indicizzazione con clienti inclusi o esclusi i percorsi

Azure Advisor identifica i contenitori di Cosmos DB che stanno usando i criteri di indicizzazione predefiniti, ma possono trarre vantaggio da un criterio di indicizzazione personalizzato basato sul modello di carico di lavoro. I criteri di indicizzazione predefiniti indicizzano tutte le proprietà, ma usando un criterio di indicizzazione personalizzato con percorsi espliciti inclusi o esclusi nei filtri di query può ridurre le UR e spazio di archiviazione usato per l'indicizzazione. [Altre informazioni sulla modifica dei criteri di indice](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurare le dimensioni della pagina di query di Azure Cosmos DB (MaxItemCount) su -1 

Azure Advisor identifica i contenitori di Azure Cosmos DB che usano le dimensioni della pagina query pari a 100 e consiglia di usare una dimensione di pagina pari a -1 per analisi più veloci. [Altre informazioni sul numero massimo di elementi](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Prestazioni**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
