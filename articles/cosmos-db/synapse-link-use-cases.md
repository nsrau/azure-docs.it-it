---
title: Casi d'uso dell'analisi in tempo reale con Collegamento ad Azure Synapse per Azure Cosmos DB
description: Informazioni su come usare Collegamento ad Azure Synapse per Azure Cosmos DB in operazioni di analisi della supply chain, previsioni, creazione di report, personalizzazione in tempo reale e manutenzione predittiva di IoT.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: d6039ac3bead535fd9c7de64a8e92a0d7a58f390
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319237"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Collegamento ad Azure Synapse per Azure Cosmos DB: casi d'uso dell'analisi quasi in tempo reale

[Collegamento ad Azure Synapse ](synapse-link.md) per Azure Cosmos DB è una funzionalità HTAP (Hybrid Transactional and Analytical Processing) nativa del cloud che consente di eseguire analisi quasi in tempo reale su dati operativi. Collegamento a Synapse crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics.

Per capire quali casi d'uso del settore possono sfruttare questa funzionalità HTAP nativa del cloud per l'analisi quasi in tempo reale su dati operativi, ecco tre esempi comuni per Collegamento ad Azure Synapse per Azure Cosmos DB:

* Analisi della supply chain, previsioni e creazione di report
* Personalizzazione in tempo reale
* Manutenzione predittiva e rilevamento delle anomalie in scenari IoT

> [!NOTE]
> Collegamento ad Azure Synapse per Azure Cosmos DB è destinato allo scenario in cui i team aziendali hanno l'esigenza di eseguire analisi quasi in tempo reale. Queste analisi vengono eseguite senza procedure ETL sui dati operativi generati nelle applicazioni transazionali basate su Azure Cosmos DB. Nel caso di requisiti di data warehouse tradizionali, ad esempio la gestione di carichi di lavoro, la concorrenza elevata e la persistenza degli aggregati tra più origini dati, rimane comunque l'esigenza di un data warehouse separato.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Analisi della supply chain, previsioni e creazione di report

Gli studi di ricerca mostrano che l'incorporamento di analisi di Big Data nelle operazioni della supply chain comporta miglioramenti nei tempi tra ordine e consegna e nell'efficienza generale.

I produttori adottano tecnologie native del cloud per interrompere i vincoli da sistemi ERP (Enterprise Resource Planning) e SCM (Supply Chain Management) legacy. Con le supply chain che generano volumi crescenti di dati operativi ogni minuto (su ordini, spedizioni, transazioni), ai produttori serve un database operativo. Per ottenere il livello di intelligenza contestuale in tempo reale e acquisire un vantaggio competitivo, è necessario che questo database operativo sia scalabile per gestire i volumi di dati e una piattaforma analitica.

L'architettura seguente mostra i vantaggi associati all'uso di Azure Cosmos DB come database operativo nativo del cloud e di Collegamento a Synapse nell'analisi della supply chain:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Collegamento a sinapsi di Azure per Azure Cosmos DB in analisi Supply Chain " border="false":::

In base all'architettura precedente, è possibile ottenere i casi d'uso seguenti con Collegamento a Synapse per Azure Cosmos DB:

* **Preparare ed eseguire il training della pipeline predittiva:** generare informazioni dettagliate dai dati operativi attraverso la supply chain tramite Machine Learning. In questo modo è possibile ridurre l'inventario, i costi operativi e i tempi tra ordine e consegna per i clienti.

  Collegamento a Synapse consente di analizzare i dati operativi mutevoli in Azure Cosmos DB senza processi ETL manuali. È quindi possibile evitare costi aggiuntivi, latenza e complessità operativa. Collegamento a Synapse consente a ingegneri e scienziati dei dati di creare pipeline predittive affidabili:

  * Eseguire query sui dati operativi dall'archivio analitico di Azure Cosmos DB sfruttando l'integrazione nativa con i pool di Apache Spark in Azure Synapse Analytics. È possibile eseguire query sui dati in un notebook interattivo o con processi remoti pianificati senza interventi complessi di ingegneria dei dati.

  * Creare modelli di Machine Learning (ML) con algoritmi ML di Spark e l'integrazione di Azure ML in Azure Synapse Analytics.

  * Eseguire il writeback dei risultati dopo l'inferenza del modello in Azure Cosmos DB per l'assegnazione di punteggi operativi quasi in tempo reale.

* **Creazione di report operativi:** i team della supply chain hanno bisogno di report flessibili e personalizzati su dati operativi accurati e in tempo reale. Questi report sono necessari per ottenere uno snapshot dell'efficacia, della redditività e della produttività della supply chain. Gli analisti dei dati e altri stakeholder chiave possono rivalutare costantemente il business e identificare le aree da modificare per ridurre i costi operativi. 

  Collegamento a Synapse per Azure Cosmos DB rende possibili scenari avanzati di business intelligence (BI) e creazione di report:

  * Eseguire query sui dati operativi da Azure Cosmos DB archivio analitico usando l'integrazione nativa con la sinapsi SQL senza server e l'espressività completa del linguaggio T-SQL.

  * Modellare e pubblicare i dashboard di Business Intelligence per l'aggiornamento automatico tramite la Azure Cosmos DB tramite la sinapsi supporto SQL senza server per gli strumenti di business intelligence noti. ad esempio Azure Analysis Services, Power BI Premium e così via.

Di seguito sono riportate alcune indicazioni per l'integrazione dei dati in batch e in streaming in Azure Cosmos DB:

* **Integrazione e orchestrazione di dati in batch:** con le supply chain che diventano sempre più complesse, è necessario integrare le relative piattaforme di dati con un'ampia varietà di origini e formati. Azure Synapse incorpora lo stesso motore di integrazione dei dati e le stesse esperienze di Azure Data Factory. Grazie a questa integrazione, gli ingegneri dei dati possono creare pipeline di dati avanzate senza un motore di orchestrazione distinto:

  * Spostare i dati da più di 85 origini dati supportate in [Azure Cosmos DB con Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Scrivere pipeline ETL senza codice in Azure Cosmos DB, inclusi i [mapping da relazionale a gerarchico e da gerarchico a gerarchico con i flussi di dati di mapping](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Integrazione ed elaborazione di dati in streaming:** con la crescita dell'IoT industriale (sensori che rilevano gli asset dalla fabbrica al punto vendita, flotte di logistica connesse e così via), si registra un'esplosione di dati in tempo reale generati in streaming che è necessario integrare con il tradizionale flusso di dati lento per generare informazioni dettagliate. Analisi di flusso di Azure è un servizio consigliato per lo streaming di ETL e l'elaborazione in Azure con un'[ampia gamma di scenari](../stream-analytics/streaming-technologies.md). Analisi di flusso di Azure supporta [Azure Cosmos DB come sink di dati nativo](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Personalizzazione in tempo reale

Oggi i retailer devono sviluppare soluzioni di e-commerce sicure e scalabili che soddisfino le esigenze dei clienti e del business. Queste soluzioni di e-commerce devono coinvolgere i clienti tramite offerte e prodotti personalizzati, elaborare le transazioni in modo rapido e sicuro e soddisfare i requisiti di evasione degli ordini e assistenza clienti. Azure Cosmos DB, insieme al recente servizio Collegamento a Synapse per Azure Cosmos DB, consente ai retailer di generare raccomandazioni personalizzate per i clienti in tempo reale. Queste soluzioni usano impostazioni di coerenza ottimizzabili e a bassa latenza per generare informazioni dettagliate immediate, come illustrato nell'architettura seguente:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Collegamento ad Azure Synapse per Azure Cosmos DB nella personalizzazione in tempo reale" border="false":::

Caso d'uso di Collegamento a Synapse per Azure Cosmos DB:

* **Preparare ed eseguire il training della pipeline predittiva:** è possibile generare informazioni dettagliate dai dati operativi tra business unit o segmenti di clienti usando Synapse Spark e modelli di Machine Learning. Il risultato si traduce in una distribuzione personalizzata ai segmenti di clienti target, oltre che in esperienze predittive per gli utenti finali e in iniziative di marketing mirate in base ai requisiti degli utenti.

## <a name="iot-predictive-maintenance"></a>Manutenzione predittiva di IoT

Le innovazioni dell'IoT industriale hanno portato a una riduzione drastica dei tempi di inattività dei macchinari e a un aumento generale dell'efficienza in tutti i settori industriali. Una di queste innovazioni è l'analisi di manutenzione predittiva per i macchinari al perimetro del cloud.

Di seguito è riportata un'architettura che sfrutta le funzionalità HTAP native del cloud di Collegamento ad Azure Synapse per Azure Cosmos DB nella manutenzione predittiva di IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Collegamento ad Azure Synapse per Azure Cosmos DB per la manutenzione predittiva di IoT" border="false" :::

Casi d'uso di Collegamento a Synapse per Azure Cosmos DB:

* **Preparare ed eseguire il training della pipeline predittiva:** i dati operativi cronologici provenienti dai sensori dei dispositivi IoT possono essere usati per il training di modelli predittivi, ad esempio i rilevatori di anomalie. Questi rilevatori di anomalie vengono quindi ridistribuiti al perimetro per il monitoraggio in tempo reale. Tale ciclo virtuoso consente la ripetizione continua del training dei modelli predittivi.

* **Creazione di report operativi:** con la crescita delle iniziative di gemelli digitali, le aziende raccolgono enormi quantità di dati operativi da un numero elevato di sensori per creare una copia digitale di ogni computer. Questi dati soddisfano i requisiti di BI per l'interpretazione delle tendenze su dati cronologici in aggiunta alle applicazioni in tempo reale sui recenti dati ad accesso frequente.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Scenario di esempio: HTAP per Azure Cosmos DB

Da circa dieci anni Azure Cosmos DB viene usato da migliaia di clienti per le applicazioni cruciali che richiedono scalabilità elastica, distribuzione globale preconfigurata e replica multimaster per operazioni di lettura e scrittura a bassa latenza e disponibilità elevata nei carichi di lavoro transazionali.
 
L'elenco seguente mostra una panoramica dei vari modelli di carico di lavoro supportati con dati operativi tramite Azure Cosmos DB:

* Servizi e app in tempo reale
* Elaborazione di flussi di eventi
* Dashboard di BI
* Analisi dei Big Data
* Machine Learning

Con Collegamento ad Azure Synapse, oltre a supportare i carichi di lavoro tradizionali, Azure Cosmos DB esegue anche carichi di lavoro analitici in tempo reale su dati operativi cronologici. Questo risultato si ottiene senza requisiti di ETL e con l'isolamento delle prestazioni garantito dai carichi di lavoro transazionali.

L'immagine seguente mostra i modelli di carico di lavoro con Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Modelli di carico di lavoro di Collegamento ad Azure Synapse per Azure Cosmos DB" border="false":::

Si esaminerà l'esempio di un'azienda di e-commerce, CompanyXYZ, con operazioni globali in 20 paesi o aree geografiche per illustrare i vantaggi derivanti dalla scelta di Azure Cosmos DB come singolo database in tempo reale che soddisfa i requisiti transazionali e analitici di una piattaforma di gestione dell'inventario.

* Il core business di CompanyXYZ dipende dal sistema di gestione dell'inventario, quindi la disponibilità e l'affidabilità sono requisiti imprescindibili. Vantaggi dell'uso di Azure Cosmos DB:

  * Grazie alla stretta integrazione con l'infrastruttura di Azure e alla replica globale multimaster e trasparente, Azure Cosmos DB offre [disponibilità elevata al 99,999%](high-availability.md) leader di settore per le interruzioni del servizio locali.

* I partner della supply chain di CompanyXYZ possono trovarsi in aree geografiche disparate, ma devono avere una singola visione dell'inventario di prodotti in tutto il mondo per supportare le operazioni locali. È anche necessario avere la possibilità di leggere in tempo reale gli aggiornamenti apportati da altri partner della supply chain, oltre alla possibilità di eseguire aggiornamenti a una velocità effettiva elevata senza preoccuparsi di entrare in conflitto con altri partner. Vantaggi dell'uso di Azure Cosmos DB:

  * Grazie a un esclusivo protocollo di replica multimaster e a un archivio transazionale ottimizzato per la scrittura e privo di latch, Azure Cosmos DB garantisce latenze inferiori ai 10 ms per operazioni di lettura e di scrittura indicizzate al 99° percentile in tutto il mondo.

  * Inserimento a velocità effettiva elevata dei feed di dati in batch e in streaming con [indicizzazione in tempo reale](index-policy.md) nell'archivio transazionale.

  * L'archivio transazionale di Azure Cosmos DB offre altre tre opzioni rispetto ai due estremi dei livelli di coerenza immediata ed eventuale per raggiungere il [compromesso tra disponibilità e prestazioni](consistency-levels-tradeoffs.md) più appropriato per le esigenze aziendali.

* I partner della supply chain di CompanyXYZ hanno modelli di traffico estremamente fluttuanti, nell'ordine di centinaia di milioni di richieste al secondo, pertanto la piattaforma di gestione dell'inventario deve supportare picchi imprevisti del traffico.  Vantaggi dell'uso di Azure Cosmos DB:

  * L'archivio transazionale di Azure Cosmos DB supporta la scalabilità elastica dell'archiviazione e della velocità effettiva tramite partizionamento orizzontale. I contenitori e i database configurati in modalità Autopilot assicurano una scalabilità automatica e istantanea della velocità effettiva con provisioning, in base alle esigenze delle applicazioni e senza effetti su disponibilità, latenza, velocità effettiva o prestazioni del carico di lavoro a livello globale.

* CompanyXYZ ha l'esigenza di stabilire una piattaforma di analisi sicura per ospitare dati di inventario cronologici a livello di sistema e rendere così possibili l'analisi e l'acquisizione di informazioni dettagliate tra partner della supply chain, business unit e funzioni. La piattaforma di analisi deve consentire la collaborazione tra il sistema, i tradizionali caso d'uso di BI/creazione di report, i casi d'uso di analisi avanzata e le soluzioni intelligenti predittive sui dati di inventario operativi. Vantaggi dell'uso di Collegamento a Synapse per Azure Cosmos DB:

  * Usando l'[archivio analitico di Azure Cosmos DB](analytical-store-introduction.md), un archivio a colonne completamente isolato, Collegamento a Synapse consente di evitare l'analisi ETL (Extract-Transform-Load, estrazione, trasformazione e caricamento) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) su dati operativi distribuiti a livello globale su larga scala.  Gli analisti aziendali, gli ingegneri dei dati e gli scienziati dei dati possono ora usare Synapse Spark o Synapse SQL in modo interoperabile per eseguire attività di business intelligence, analisi e pipeline di Machine Learning quasi in tempo reale senza influire sulle prestazioni dei carichi di lavoro transazionali di Azure Cosmos DB. Per altri dettagli, vedere i [vantaggi di Collegamento a Synapse in Azure Cosmos DB](synapse-link.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link.md) 

* [Archivio analitico di Azure Cosmos DB](analytical-store-introduction.md)

* [Uso di Collegamento ad Azure Synapse per Azure Cosmos DB](configure-synapse-link.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Supporto del runtime di SQL Server in Azure sinapsi Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)
