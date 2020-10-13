---
title: Vantaggi di Collegamento ad Azure Synapse per Azure Cosmos DB e quando usare il servizio
description: Informazioni su Collegamento ad Azure Synapse per Azure Cosmos DB. Collegamento a Synapse consente di eseguire analisi quasi in tempo reale (HTAP) usando Azure Synapse Analytics su dati operativi in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e02f0bf1bb9646a644a40279a8bae930de34c0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567996"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>Che cos'è Collegamento ad Azure Synapse per Azure Cosmos DB (anteprima)?

> [!IMPORTANT]
> Collegamento ad Azure Synapse per Azure Cosmos DB è attualmente disponibile in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Collegamento ad Azure Synapse per Azure Cosmos DB è una funzionalità HTAP (Hybrid Transactional and Analytical Processing) nativa del cloud che consente di eseguire analisi quasi in tempo reale su dati operativi in Azure Cosmos DB. Collegamento ad Azure Synapse crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics.

Usando l'[archivio analitico di Azure Cosmos DB](analytical-store-introduction.md), un archivio a colonne completamente isolato, Collegamento a Synapse consente di evitare l'analisi ETL (Extract-Transform-Load, estrazione, trasformazione e caricamento) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) su dati operativi su larga scala. Gli analisti aziendali, gli ingegneri dei dati e gli scienziati dei dati possono ora usare Synapse Spark o Synapse SQL in modo intercambiabile per eseguire attività di business intelligence, analisi e pipeline di Machine Learning quasi in tempo reale. Questo risultato si ottiene senza influire sulle prestazioni dei carichi di lavoro transazionali in Azure Cosmos DB. 

L'immagine seguente mostra l'integrazione di Collegamento ad Azure Synapse con Azure Cosmos DB e Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagramma dell'architettura per l'integrazione di Azure Synapse Analytics con Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Vantaggi

Per analizzare set di dati operativi di grandi dimensioni riducendo al minimo l'impatto sulle prestazioni dei carichi di lavoro transazionali cruciali, solitamente i dati operativi di Azure Cosmos DB vengono estratti ed elaborati da pipeline ETL (Extract-Transform-Load, estrazione, trasformazione e caricamento). Le pipeline ETL richiedono molti livelli di spostamento dei dati, il che genera una complessità operativa elevata e un impatto sulle prestazioni dei carichi di lavoro transazionali. L'analisi di dati operativi dal momento in cui vengono generati comporta inoltre un aumento della latenza.

Rispetto alle soluzioni tradizionali basate su ETL, Collegamento ad Azure Synapse per Azure Cosmos DB offre diversi vantaggi, tra cui:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Riduzione della complessità senza processi ETL da gestire

Collegamento ad Azure Synapse consente di accedere direttamente all'archivio analitico di Azure Cosmos DB tramite Azure Synapse Analytics senza complessi spostamenti di dati. Tutti gli aggiornamenti apportati ai dati operativi sono visibili nell'archivio analitico quasi in tempo reale senza ETL o processi di feed di modifiche. È possibile eseguire analisi su larga scala nell'archivio analitico, da Synapse Analytics, senza trasformazioni aggiuntive dei dati.

### <a name="near-real-time-insights-into-your-operational-data"></a>Informazioni dettagliate quasi in tempo reale sui dati operativi

È ora possibile ottenere informazioni dettagliate sui dati operativi quasi in tempo reale usando Collegamento ad Azure Synapse. I sistemi basati su ETL tendono ad avere una latenza più elevata per l'analisi dei dati operativi, a causa dei molti livelli necessari per estrarli, trasformarli e caricarli. Grazie all'integrazione nativa dell'archivio analitico di Azure Cosmos DB con Azure Synapse Analytics, è possibile analizzare i dati operativi quasi in tempo reale, rendendo possibili nuovi scenari aziendali. 

### <a name="no-impact-on-operational-workloads"></a>Nessun impatto sui carichi di lavoro operativi

Con Collegamento a Synapse, è possibile eseguire query analitiche sull'archivio analitico di Azure Cosmos DB (un archivio a colonne separato) durante l'elaborazione delle operazioni transazionali, usando una velocità effettiva con provisioning per il carico di lavoro transazionale (un archivio transazionale basato su righe).  Il carico di lavoro analitico viene gestito indipendentemente dal traffico del carico di lavoro transazionale senza consumare la velocità effettiva con provisioning dei dati operativi.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Ottimizzazione per carichi di lavoro analitici su larga scala

L'archivio analitico di Azure Cosmos DB è ottimizzato per offrire scalabilità, elasticità e prestazioni per carichi di lavoro analitici senza alcuna dipendenza dai runtime di calcolo. La tecnologia di archiviazione è gestita automaticamente per ottimizzare i carichi di lavoro analitici. Grazie al supporto incorporato in Azure Synapse Analytics, l'accesso al livello di archiviazione assicura semplicità e prestazioni elevate.

### <a name="cost-effective"></a>Conveniente

Con Collegamento ad Azure Synapse, si ottiene una soluzione per l'analisi operativa completamente gestita e ottimizzata per i costi. La soluzione elimina i livelli aggiuntivi di archiviazione e calcolo necessari nelle tradizionali pipeline ETL per l'analisi dei dati operativi. 

L'archivio analitico di Azure Cosmos DB segue un modello di determinazione dei prezzi a consumo, in base all'archiviazione dei dati e al numero di operazioni di lettura/scrittura e di query di analisi eseguite. Non è necessario effettuare il provisioning di una velocità effettiva, come è attualmente richiesto per i carichi di lavoro transazionali. L'accesso ai dati con motori di calcolo estremamente elastici da Azure Synapse Analytics aumenta in modo significativo l'efficienza dei costi complessivi associati alla gestione delle risorse di archiviazione e di calcolo.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analisi per le Scritture disponibili localmente, distribuite a livello globale e in più aree

È possibile eseguire efficacemente query analitiche sulla copia locale dei dati più vicina in Azure Cosmos DB. Azure Cosmos DB include la funzionalità all'avanguardia per l'esecuzione dei carichi di lavoro analitici distribuiti a livello globale oltre che dei carichi di lavoro transazionali in modalità attiva/attiva.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Abilitare scenari HTAP per i dati operativi

Collegamento a Synapse raggruppa l'archivio analitico di Azure Cosmos DB con il supporto del runtime analitico di Azure Synapse. Grazie a questa integrazione, è possibile sviluppare soluzioni HTAP (Hybrid Transactional/Analytical Processing) native del cloud che generano informazioni dettagliate basate sugli aggiornamenti in tempo reale dei dati operativi su set di dati di grandi dimensioni. È così possibile realizzare nuovi scenari aziendali per generare avvisi basati su tendenze dinamiche, creare dashboard quasi in tempo reale e definire esperienze aziendali basate sul comportamento degli utenti.

### <a name="azure-cosmos-db-analytical-store"></a>Archivio analitico di Azure Cosmos DB

L'archivio analitico di Azure Cosmos DB è una rappresentazione orientata alle colonne dei dati operativi disponibili in Azure Cosmos DB. Questo archivio analitico è indicato per eseguire query rapide e a costi contenuti su set di dati operativi di grandi dimensioni, senza copiare i dati e senza influire sulle prestazioni dei carichi di lavoro transazionali.

L'archivio analitico preleva automaticamente inserimenti, aggiornamenti ed eliminazioni ad alta frequenza nei carichi di lavoro transazionali quasi in tempo reale, come funzionalità completamente gestita (sincronizzazione automatica) di Azure Cosmos DB. Non sono necessari feed di modifiche o processi ETL. 

Se si ha un account Azure Cosmos DB distribuito a livello globale, un archivio analitico abilitato per un contenitore sarà disponibile in tutte le aree per tale account. Per altre informazioni sull'archivio analitico, vedere l'articolo [Panoramica dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md).

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrazione con Azure Synapse Analytics

Con Collegamento a Synapse è ora possibile connettersi direttamente ai contenitori Azure Cosmos DB da Azure Synapse Analytics e accedere all'archivio analitico senza connettori separati. Azure sinapsi Analytics supporta attualmente il collegamento sinapsi con [sinapsi Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) e [sinapsi SQL senza server](../synapse-analytics/sql/on-demand-workspace-overview.md).

È possibile eseguire query sui dati dell'archivio analitico di Azure Cosmos DB simultaneamente, con interoperabilità tra diversi runtime di analisi supportati da Azure Synapse Analytics. Per analizzare i dati operativi, non è necessario eseguire trasformazioni aggiuntive. È possibile eseguire query e analizzare i dati dell'archivio analitico tramite:

* Synapse Apache Spark con supporto completo per Scala, Python, SparkSQL e C#. Synapse Spark è fondamentale per gli scenari di ingegneria dei dati e data science

* SQL serverless con linguaggio T-SQL e supporto per strumenti di BI familiari, ad esempio Power BI Premium e così via.

> [!NOTE]
> Da Azure Synapse Analytics è possibile accedere agli archivi analitici e transazionali nel contenitore Azure Cosmos DB. Se tuttavia si vuole eseguire un'analisi su larga scala dei dati operativi, è consigliabile usare l'archivio analitico per evitare l'impatto sulle prestazioni dei carichi di lavoro transazionali.

> [!NOTE]
> È possibile eseguire analisi a bassa latenza in un'area di Azure connettendo il contenitore Azure Cosmos DB al runtime di Synapse in tale area.

Questa integrazione consente gli scenari HTAP seguenti per utenti diversi:

* Un ingegnere di business intelligence che vuole modellare e pubblicare un report Power BI per accedere ai dati operativi Live in Azure Cosmos DB direttamente tramite sinapsi SQL.

* Un analista di dati che vuole ricavare informazioni dettagliate dai dati operativi di un contenitore Azure Cosmos DB eseguendo query con Synapse SQL, leggere i dati su larga scala e combinare i risultati con altre origini dati.

* Uno scienziato dei dati che vuole usare Synapse Spark per trovare una funzionalità che consenta di migliorare il modello e sottoporlo a training senza eseguire operazioni complesse di ingegneria dei dati. I risultati del modello possono anche essere scritti dopo l'inferenza in Azure Cosmos DB per l'assegnazione di punteggi in tempo reale ai dati tramite Spark Synapse.

* Un ingegnere dei dati che vuole rendere i dati accessibili ai consumer creando tabelle SQL o Spark su contenitori Azure Cosmos DB senza processi ETL manuali.

Per altre informazioni sul supporto del runtime di Azure Synapse Analytics per Azure Cosmos DB, vedere [Supporto di Azure Synapse Analytics per Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Quando usare Collegamento ad Azure Synapse per Azure Cosmos DB?

L'uso di Collegamento a Synapse è consigliato nei casi seguenti:

* Per i clienti di Azure Cosmos DB che vogliono eseguire attività di analisi, BI e Machine Learning sui dati operativi. In questi casi, Collegamento a Synapse offre un'esperienza di analisi maggiormente integrata senza influire sulla velocità effettiva con provisioning dell'archivio transazionale. Ad esempio:

  * Si eseguono attività di analisi o BI sui dati operativi di Azure Cosmos DB direttamente usando connettori separati

  * Si eseguono processi ETL per estrarre dati operativi in un sistema di analisi distinto.
 
In questi casi, Collegamento a Synapse offre un'esperienza di analisi maggiormente integrata senza influire sulla velocità effettiva con provisioning dell'archivio transazionale.

L'uso di Collegamento a Synapse non è consigliato se è necessario rispettare i requisiti dei tradizionali data warehouse, ad esempio concorrenza elevata, gestione dei carichi di lavoro e persistenza degli aggregati tra più origini dati. Per altre informazioni, vedere gli [scenari comuni che possono essere basati su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Limitazioni

* Il collegamento sinapsi di Azure è supportato per l'API Azure Cosmos DB SQL (Core) e l'API Azure Cosmos DB per MongoDB.

* Attualmente, l'archivio analitico può essere abilitato solo per i nuovi contenitori. Per usare l'archivio analitico per i contenitori esistenti, migrare i dati dai contenitori esistenti ai nuovi contenitori usando [Azure Cosmos DB strumenti di migrazione](cosmosdb-migrationchoices.md). È possibile abilitare il collegamento sinapsi su account Azure Cosmos DB nuovi ed esistenti.

* L'accesso all'archivio analitico di Azure Cosmos DB con Synapse SQL serverless è attualmente in fase di anteprima controllata. Per richiedere l'accesso, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

* In anteprima, per gli account di database abilitati per il collegamento sinapsi, il backup e il ripristino di contenitori non sono supportati. Se si dispone di carichi di lavoro che richiedono funzionalità di backup e ripristino, è consigliabile non abilitare il collegamento sinapsi per gli account di database. 

* L'accesso all'archivio analitico di Azure Cosmos DB con Synapse SQL con provisioning non è attualmente disponibile.

## <a name="pricing"></a>Prezzi

Il modello di fatturazione di Collegamento ad Azure Synapse include i costi sostenuti con l'uso dell'archivio analitico di Azure Cosmos DB e del runtime di Synapse. Per altre informazioni, vedere gli articoli [Prezzi dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Panoramica dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md)

* [Introduzione a Collegamento ad Azure Synapse per Azure Cosmos DB](configure-synapse-link.md)
 
* [Funzionalità supportate dal runtime di Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casi d'uso di Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md)
