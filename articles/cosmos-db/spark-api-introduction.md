---
title: Introduzione all'analisi operativa predefinita in Azure Cosmos DB con Apache Spark
description: Informazioni su come usare il supporto predefinito di Apache Spark in Azure Cosmos DB per l'esecuzione di analisi operativa e intelligenza artificiale
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338618"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Analisi operativa predefinita in Azure Cosmos DB con Apache Spark

Con Azure Cosmos DB, è possibile eseguire analisi e funzionalità di intelligenza artificiale con distribuzione globale e a bassa latenza su dati transazionali. Con il supporto nativo per i notebook Apache Spark e Jupyter, Azure Cosmos DB aiuta a ridurre il tempo necessario per ottenere informazioni dettagliate. Dal momento che i dati vengono inseriti e forniti e le analisi vengono eseguite sulla replica di database locale in un'area di Azure, è possibile eseguire direttamente query Spark su dati multimodello indicizzati archiviati nelle partizioni di dati.

Il supporto predefinito di Apache Spark in Azure Cosmos DB consente di eseguire analisi da Apache Spark sui dati archiviati in un account Azure Cosmos. È disponibile il supporto nativo per l'esecuzione diretta dei processi Apache Spark nei database Cosmos distribuiti a livello globale. Con queste funzionalità, sviluppatori, data engineer e data scientist possono usare Azure Cosmos DB come piattaforma dati flessibile, scalabile e ad alte prestazioni per eseguire carichi di lavoro **OLTP e OLAP/HTAP**.

Il supporto di Apache Spark in Azure Cosmos DB offre i vantaggi seguenti:

* È possibile ridurre al minimo il tempo necessario per ottenere informazioni dettagliate per utenti e dati distribuiti geograficamente.

* È possibile semplificare l'architettura della soluzione e ridurre il [costo totale di proprietà](total-cost-ownership.md) (TCO). Il sistema avrà il minor numero di componenti per l'elaborazione dei dati ed evita qualsiasi spostamento dei dati non necessario tra di essi.

* Crea un confine di [sicurezza](secure-access-to-data.md), [conformità](compliance.md) e controllo che include tutti i dati sottoposti a gestione.

* Fornisce funzionalità di analisi sempre attive o [a disponibilità elevata](high-availability.md) per gli utenti finali, supportate da rigorosi contratti di servizio.

![Visualizzazione del supporto per Apache Spark in Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Il supporto di Apache Spark in Azure Cosmos DB consente di creare e distribuire soluzioni come modelli di intelligenza artificiale e Deep Learning, analisi predittiva, elementi consigliati, IoT, clienti a 360°, rilevamento delle frodi, analisi clickstream e del sentiment del testo. Queste soluzioni vengono applicate direttamente ai dati di Azure Cosmos DB.

È possibile configurare un processo ETL batch e di streaming in Azure Cosmos DB, senza dover uscire dal servizio di database o aggiungere altri servizi di calcolo. È possibile incrementare in modo elastico l'ambiente di calcolo quando necessario per eseguire un processo ETL e ridurlo quando il processo è stato eseguito.

Il supporto di Apache Spark in Azure Cosmos DB offre il supporto predefinito per il Machine Learning nei runtime di Apache Spark. I runtime includono Spark MLLib, Microsoft Machine Learning per Spark, Azure Machine Learning e Servizi cognitivi. Con queste funzionalità, data scientist, data engineer e analisti dei dati possono creare e rendere operativi modelli di Machine Learning direttamente in Azure Cosmos DB, in una frazione del tempo e a costi contenuti.


## <a name="key-benefits"></a>Vantaggi principali

### <a name="low-latency-operational-analytics-and-ai"></a>Analisi operativa e intelligenza artificiale a bassa latenza

Con Apache Spark sul database di Azure Cosmos DB distribuito a livello globale ora è possibile ottenere analisi approfondite in tempi rapidi in tutto il mondo. Azure Cosmos DB consente **analisi operative distribuite a livello globale e a bassa latenza** con scalabilità elastica attraverso tre tecniche principali:

* Poiché il database di Azure Cosmos DB è distribuito a livello globale, tutti i dati vengono inseriti in locale nella posizione in cui si trovano i produttori dei dati (ad esempio, gli utenti). Le query vengono eseguite sulle repliche locali più vicine sia ai produttori che ai consumer dei dati, indipendentemente dalla posizione in cui si trovano.

* Tutte le query analitiche vengono eseguite direttamente sui dati indicizzati archiviati nelle partizioni dati, senza richiedere spostamenti di dati non necessari.

* Poiché Spark condivide la stessa posizione con Azure Cosmos DB, viene eseguito un numero inferiore di conversioni intermedie e spostamenti dei dati, migliorando le prestazioni e la scalabilità.

* Tutte le funzionalità di base di Azure Cosmos DB, ad esempio multimaster, failover automatico, zone di disponibilità e così via, sono disponibili per Apache Spark predefinito in Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Esperienza serverless unificata per Apache Spark

Essendo un database multimodello, Azure Cosmos DB ora espande il proprio supporto per le API OSS, fornendo un'**esperienza serverless unificata per Apache Spark** con modelli di dati chiave-valore, documento, a grafi e con famiglie di colonne. I diversi modelli di dati sono supportati tramite le API MongoDB, Cassandra, Gremlin, Etcd e SQL, tutte le quali operano sugli stessi dati sottostanti. 

Con il supporto di Apache Spark in Azure Cosmos DB è possibile supportare in modo nativo le applicazioni scritte in Scala, Python e Java, nonché usare varie librerie strettamente integrate per SQL. Queste librerie includono ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), elaborazione di flussi ([Spark Structured Streaming](https://spark.apache.org/streaming/)) ed elaborazione di grafi (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Questi strumenti semplificano l'utilizzo di Apache Spark per diversi casi d'uso. Non è necessario occuparsi della gestione di Spark o di configurare cluster Spark. È possibile usare le familiari API di Apache Spark e i **notebook di Jupyter** per l'API di analisi e SQL o qualsiasi API NoSQL OSS come Cassandra per l'elaborazione transazionale sugli stessi dati sottostanti nello stesso momento.

### <a name="no-schema-or-index-management"></a>Nessuna gestione di schemi o indici

A differenza dei tradizionali database analitici, con Azure Cosmos DB i data engineer e i data scientist non devono più occuparsi di complessi schemi e della gestione degli indici. Il motore di database in Azure Cosmos DB non richiede alcuno schema esplicito né la gestione degli indici ed è in grado di indicizzare automaticamente tutti i dati inseriti per servire rapidamente le query Apache Spark.

### <a name="consistency-choices"></a>Opzioni per la coerenza

Poiché i processi Apache Spark vengono eseguiti nelle partizioni dati del database di Azure Cosmos DB, le query disporranno di [cinque opzioni ben definite per la coerenza](consistency-levels.md). Questi modelli di coerenza offrono la flessibilità di scegliere la coerenza assoluta per fornire i risultati più accurati per gli algoritmi di Machine Learning senza compromettere la latenza e la disponibilità elevata.

### <a name="comprehensive-slas"></a>Contratti di servizio completi

I processi Apache Spark avranno i vantaggi di Azure Cosmos DB, ad esempio [contratti di servizio](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) completi leader di settore (99,999) senza alcun sovraccarico per la gestione di cluster Apache Spark distinti. Questi contratti di servizio includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Carichi di lavoro operativi e analitici misti con isolamento completo

L'integrazione di Apache Spark in Azure Cosmos DB consente di superare la separazione tra transazionale e analitico, che rappresentava uno dei principali problemi dei clienti durante la creazione di applicazioni native del cloud su scala globale. I carichi di lavoro OLTP e OLAP vengono eseguiti side-by-side e non interferiscono tra loro.

### <a name="low-latency-analytical-and-transactional-storage"></a>Archiviazione transazionale e analitica a bassa latenza

Azure Cosmos DB archivia i dati in modo nativo in due livelli di archiviazione distinti: archiviazione transazionale (orientata alle righe) e analitica (orientata alle colonne, in formato file di Apache Parquet). Replica i dati in ogni livello globalmente e consente agli utenti di gestire i dati in modo indipendente in questi livelli in base ai criteri di conservazione.

Questa architettura di archiviazione consente carichi di lavoro transazionali e analitici di importanza critica sugli stessi dati distribuiti a livello globale. Con Cosmos DB, non è necessario eseguire alcuna operazione ETL né alcuna operazione di spostamento dei dati non necessaria. È possibile eseguire semplicemente i carichi di lavoro transazionali e analitici sugli stessi dati sottostanti. I carichi di lavoro transazionali possono usare le note API di accesso transazionale, tra cui SQL, Cassandra, MongoDB, Gremlin e Etcd, mentre i carichi di lavoro di analisi e intelligenza artificiale possono usare Apache Spark SQL, i framework di ML e l'intera toolchain di Apache Spark.

### <a name="snapshots-and-historical-analytical-queries"></a>Snapshot e query analitiche cronologiche

È possibile creare su richiesta o con pianificazione snapshot dei dati compressi a colonne archiviati nel livello analitico per eseguire query analitiche direttamente su uno snapshot specifico. Questa funzionalità consente di eseguire query analitiche flashback o nel passato, rollback, percorsi di controllo cronologici completi ed esperimenti di apprendimento automatico e di intelligenza artificiale riproducibili.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui vantaggi di Azure Cosmos DB, vedere l'articolo [Panoramica](introduction.md).
* [Introduzione all'API di Azure Cosmos DB per MongoDB](mongodb-introduction.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](cassandra-introduction.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](graph-introduction.md)
* [Introduzione all'API Tabella di Azure Cosmos DB](table-introduction.md)