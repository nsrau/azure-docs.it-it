---
title: Modelli di soluzioni Azure Stream Analitica
description: Informazioni sui modelli di soluzione per Azure Stream Analitica.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5929ff439bc31e16643e5c57868cd6b68f9cd99c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329568"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Modelli di soluzioni Azure Stream Analitica

Come molti altri servizi in Azure, Stream Analitica è più adatta con altri servizi per creare una soluzione end-to-end più grande. Questo articolo descrive vari schemi architetturali e semplice soluzioni di Azure Stream Analitica. È possibile creare questi schemi per lo sviluppo di soluzioni più complesse. I modelli descritti in questo articolo sono utilizzabile in un'ampia gamma di scenari. Esempi di modelli specifici dello scenario sono disponibili nel [architetture delle soluzioni Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Creare un processo di Stream Analitica all'esperienza di creazione di dashboard in tempo reale di risparmio energia

Con Azure Stream Analitica, è possibile organizzare rapidamente gli avvisi e i dashboard in tempo reale. Una soluzione semplice e inserisce gli eventi da hub eventi o IoT Hub, e [feed di dashboard di Power BI con un set di dati di streaming](/power-bi/service-real-time-streaming). Per altre informazioni, vedere l'esercitazione dettagliata [analizzare i dati delle telefonate con Analitica Stream e visualizzare i risultati nel dashboard di Power BI](stream-analytics-manage-job.md).

![Dashboard di Power BI di ASA](media/stream-analytics-solution-patterns/pbidashboard.png)

Questa soluzione può essere compilata in pochi minuti dal portale di Azure. È presente che alcuna codifica esteso non coinvolti e linguaggio SQL viene usato per esprimere la logica di business.

Questo modello di soluzione offre la latenza più bassa dall'origine evento per il dashboard di Power BI in un browser. Azure Stream Analitica è il solo servizio di Azure con questa funzionalità predefinita.

## <a name="use-sql-for-dashboard"></a>Usare SQL per il dashboard

Dashboard di Power BI offre bassa latenza, ma non può essere usato per generare report di Power BI competente completo. Un modello comune di creazione di report è di output dei dati a un database SQL prima di tutto. Quindi usare connettore SQL di Power BI per eseguire query SQL per i dati più recenti.

![Dashboard di SQL ASA](media/stream-analytics-solution-patterns/sqldashboard.png)

Con SQL database offre maggiore flessibilità ma a scapito di una latenza leggermente più elevata. Questa soluzione è ottimale per i processi con i requisiti di latenza superiori a un secondo. Con questo metodo, è possibile ottimizzare la funzionalità di Power BI a sezione ulteriormente e ripartiscono i dati per report e molte più opzioni di visualizzazione. È anche possibile ottenere la flessibilità dell'uso di altre soluzioni di dashboard, ad esempio Tableau.

SQL non è un archivio dati di velocità effettiva elevata. La velocità effettiva massima per un database SQL di Azure Stream Analitica è attualmente circa 24 MB/s. Se le origini di eventi nella soluzione producono dati a una maggiore velocità, è necessario usare la logica di elaborazione in Stream Analitica per ridurre la velocità di output in SQL. Tecniche quali l'applicazione di filtri, funzioni di aggregazione, criteri di ricerca con join temporali e le funzioni analitiche sono utilizzabile. La frequenza di output in SQL può essere ottimizzata ulteriormente usando tecniche descritte in [output Analitica Stream di Azure al Database SQL di Azure](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporare approfondite in tempo reale dell'applicazione con messaggistica degli eventi

L'uso in secondo luogo più diffuso di Analitica Stream consiste nel generare avvisi in tempo reale. In questo modello di soluzione per la logica di business in Stream Analitica può essere usata per rilevare [modelli temporali e quelli spaziali](stream-analytics-geospatial-functions.md) oppure [anomalie](stream-analytics-machine-learning-anomaly-detection.md), quindi generare segnali di avviso. Tuttavia, a differenza della soluzione di dashboard in cui Analitica Stream Usa Power BI come endpoint preferito, un numero di sink di dati intermedi è utilizzabile. Questi sink includono hub eventi, Bus di servizio e le funzioni di Azure. Come il compilatore di applicazioni, occorre decidere quali dati di sink è adatta per il proprio scenario.

Per la logica consumer di eventi a valle deve essere implementata per generare gli avvisi nel flusso di lavoro aziendale esistente. Poiché è possibile implementare la logica personalizzata in funzioni di Azure, funzioni di Azure è il modo più rapido è possibile eseguire questa integrazione. Esercitazione per l'uso di funzioni di Azure come output per un processo di Stream Analitica è reperibile nel [esecuzione di funzioni di Azure da processi di Azure Stream Analitica](stream-analytics-with-azure-functions.md). Funzioni di Azure supporta anche diversi tipi di notifiche tra cui testo e indirizzo di posta elettronica. App per la logica può essere usato anche per questo tipo integrazione con hub di eventi tra Stream Analitica e App per la logica.

![App di messaggistica eventi ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Hub eventi, d'altra parte, offre il punto di integrazione più flessibile. Molti altri servizi, ad esempio Esplora dati di Azure e Time Series Insights possono utilizzare gli eventi da hub eventi. I servizi possono essere connessi direttamente al sink di hub eventi da Azure Stream Analitica per completare la soluzione. Hub eventi è anche il massima velocità effettiva del broker di messaggistica disponibile in Azure per questi scenari di integrazione.

## <a name="dynamic-applications-and-websites"></a>Siti Web e applicazioni dinamiche

È possibile creare visualizzazioni personalizzate in tempo reale, ad esempio di dashboard o eseguire il mapping di visualizzazione, usando Azure Stream Analitica e servizio Azure SignalR. Usa SignalR, i client web possono essere aggiornati e mostrano il contenuto dinamico in tempo reale.

![App dinamico ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporare approfondite in tempo reale dell'applicazione tramite gli archivi dati

La maggior parte dei servizi web e applicazioni web oggi utilizzano un modello di richiesta/risposta per soddisfare il livello di presentazione. Il modello di richiesta/risposta è semplice da compilare e possono essere facilmente ridimensionati con tempi di riposta ridotti con un front-end senza stato e gli archivi a scalabilità, come Cosmos DB.

Volume elevato dei dati crea spesso i colli di bottiglia in un sistema basato su CRUD. Il [modello di soluzione di origine eventi](/azure/architecture/patterns/event-sourcing) viene usato per risolvere i colli di bottiglia delle prestazioni. Informazioni approfondite e modelli temporali sono anche difficile e inefficiente per estrarre da un archivio dati tradizionale. Volumi elevati di dati moderne applicazioni basate su spesso adottano un'architettura basata su flussi di dati. Azure Stream Analitica, come il motore di calcolo per i dati in transito è un fattore fondamentale in quell'architettura.

![App di determinazione dell'origine eventi ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

In questo modello di soluzione, gli eventi vengono elaborati e aggregati in archivi dati da Azure Stream Analitica. Il livello dell'applicazione interagisce con gli archivi di dati usando il modello di richiesta/risposta tradizionale. A causa di capacità degli Analitica Stream di elaborare un numero elevato di eventi in tempo reale, l'applicazione è altamente scalabile senza la necessità di aumentare il livello di archivio dati. Il livello di archivio dati è essenzialmente una vista materializzata nel sistema. [Output di Analitica Stream di Azure ad Azure Cosmos DB](stream-analytics-documentdb-output.md) viene descritto come utilizzare Cosmos DB come un output Stream Analitica.

Nelle applicazioni reali in cui la logica di elaborazione è complesso e non vi è la necessità di aggiornare alcune parti della logica in modo indipendente, più processi di Analitica Stream possono essere composti insieme a hub eventi come il gestore eventi intermediario.

![App di determinazione dell'origine di eventi complessi di ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Questo modello consente di migliorare la resilienza e facilità di gestione del sistema. Tuttavia, anche se Stream Analitica le garanzie di tipo exactly-once l'elaborazione, è possibile che gli eventi duplicati potrebbero inserire nell'hub eventi di intermediario. È importante per il processo di Stream Analitica a valle della deduplicazione di eventi con le chiavi per la logica in una finestra di modalità loopback. Per altre informazioni sul recapito di eventi, vedere [garanzie di recapito di eventi](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) riferimento.

## <a name="use-reference-data-for-application-customization"></a>Usare i dati di riferimento per la personalizzazione dell'applicazione

La funzionalità di dati di riferimento di Analitica Stream di Azure è progettata specificamente per la personalizzazione degli utenti finali, ad esempio gli avvisi di soglia, l'elaborazione delle regole, e [recinti virtuali](geospatial-scenarios.md). Il livello dell'applicazione può accettare le modifiche di parametro e archiviarle in un database SQL. Il processo di Stream Analitica periodicamente una query per le modifiche dal database e rende accessibili i parametri di personalizzazione tramite un join dei dati di riferimento. Per altre informazioni su come usare i dati di riferimento per la personalizzazione dell'applicazione, vedere [i dati di riferimento SQL](sql-reference-data.md) e [join dei dati di riferimento](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Questo modello è anche utilizzabile per implementare un motore di regole in cui le soglie delle regole sono definite dai dati di riferimento. Per altre informazioni sulle regole, vedere [elaborare regole basate su soglie configurabili in Azure Stream Analitica](stream-analytics-threshold-based-rules.md).

![App i dati di riferimento ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Aggiungere le informazioni dettagliate in tempo reale di Machine Learning

Predefiniti di Azure i Analitica Stream [modello di rilevamento delle anomalie](stream-analytics-machine-learning-anomaly-detection.md) è un modo pratico per introdurre Machine Learning per l'applicazione in tempo reale. Per esigenze di un intervallo più ampio di Machine Learning, vedere [Analitica Stream di Azure si integra con assegnazione dei punteggi servizio dell'Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Per gli utenti avanzati che vogliono incorporare la stessa pipeline di Analitica di Stream, corsi di formazione online e di assegnazione dei punteggi, vedere questo esempio di come eseguire questa operazione con [regressione lineare](stream-analytics-high-frequency-trading.md).

![App di Machine Learning ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Quasi in tempo reale data warehouse

Un altro modello comune consiste in tempo reale data warehousing, acronimo di data warehouse di streaming. Oltre agli eventi che arrivano in IoT Hub e hub eventi dall'applicazione [Analitica Stream di Azure in esecuzione in dispositivi perimetrali IoT](stream-analytics-edge.md) può essere utilizzata per soddisfare la pulizia dei dati, riduzione dei dati e archivio dati e alle esigenze in avanti. Stream Analitica in esecuzione in dispositivi perimetrali IoT può gestire correttamente problemi di connettività e limitazione della larghezza di banda nel sistema. L'adattatore di output SQL utilizzabile per output in SQL Data Warehouse; Tuttavia, la velocità effettiva massima è limitata a 10 MB/s.

![ASA Data warehouse](media/stream-analytics-solution-patterns/datawarehousing.png)

È un modo per migliorare la velocità effettiva con alcuni compromesso di latenza per archiviare gli eventi nell'archiviazione Blob di Azure e quindi [importarli in SQL Data Warehouse con Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). È necessario riunirle output Stream Analitica nell'archiviazione BLOB e l'input dall'archivio blob a SQL Data Warehouse dal [archiviazione dei dati dal timestamp](stream-analytics-custom-path-patterns-blob-storage-output.md) e periodicamente l'importazione.

In questo modello di utilizzo, Azure Stream Analitica viene usato come motore ETL quasi in tempo reale. Gli eventi in arrivo sono appena continuamente trasformarli e archiviarli per il consumo analitica downstream.

![Velocità effettiva elevata ASA Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>L'archiviazione dei dati in tempo reale per analitica

La maggior parte delle attività di analitica e analisi scientifica dei dati si verificano ancora offline. I dati possono essere archiviati da Analitica Stream di Azure tramite Azure Data Lake Store Gen2 output e i formati di output Parquet. Questa funzionalità consente di rimuovere l'attrito per inserire dati direttamente in Azure Data Lake Analitica, Azure Databricks e Azure HDInsight. Azure Stream Analitica viene utilizzato come un motore ETL quasi in tempo reale in questa soluzione. È possibile esplorare i dati archiviati in Data Lake usando vari motori di calcolo.

![Analitica in linea di ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Usare i dati di riferimento per abilitare l'arricchimento dei

Arricchimento dei dati è spesso un requisito per i motori di ETL. Supporta l'arricchimento dei dati con Azure Stream Analitica [dati di riferimento](stream-analytics-use-reference-data.md) dal database SQL e archiviazione Blob di Azure. Arricchimento dei dati può essere eseguita per i dati di destinazione in Azure Data Lake e SQL Data Warehouse.

![Analitica offline ASA con arricchimento dei dati](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Rendere operative le informazioni dettagliate dai dati archiviati

Se si combina il modello di analitica in linea con l'applicazione quasi in tempo reale modello, è possibile creare un ciclo di feedback. Il ciclo di feedback consente all'applicazione di regolare automaticamente per la modifica di modelli nei dati. Il ciclo di feedback può essere semplice quanto la modifica del valore di soglia per gli avvisi, o complesse, ad esempio ripetizione del training modelli di Machine Learning. Per entrambi i processi ASA in esecuzione nel cloud e in dispositivi perimetrali IoT, è possibile utilizzare l'architettura della soluzione stessa.

![Operazionalizzazione insights ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Come monitorare i processi di ASA

Un processo Azure Stream Analitica può essere eseguito su 24 e 7 per l'elaborazione di eventi in ingresso in modo continuo in tempo reale. Il tempo di attività garantito è fondamentale per l'integrità dell'applicazione complessiva. Anche se Stream Analitica è l'unico servizio streaming analitica del settore che offre una [disponibilità garantita del 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), potrebbe essere addebitato comunque un certo livello di tempi di inattività. Nel corso degli anni, Stream Analitica ha introdotto le metriche, log e stati dei processi in modo da riflettere l'integrità dei processi. Tutti gli elementi vengono rilevati tramite il servizio monitoraggio di Azure e possono essere esportati ulteriormente in OMS. Per altre informazioni, vedere [Analitica Stream comprensione del processo di monitoraggio e su come monitorare le query](stream-analytics-monitoring.md).

![Monitoraggio di ASA](media/stream-analytics-solution-patterns/monitoring.png)

Esistono due aspetti chiave da monitorare:

- [Processo non riuscito dello stato](job-states.md)

    Prima di tutto, è necessario assicurarsi che l'esecuzione del processo. Senza il processo in esecuzione, non vengono generati alcuna nuova metrica o log. I processi è possono modificare uno stato di errore per vari motivi, tra cui un elevato livello di utilizzo delle unità di streaming (ad esempio, esaurendo le risorse).

- [Metriche di ritardo della filigrana](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Questa metrica indica la distanza dai bordi dietro l'elaborazione della pipeline è in tempo reale (secondi). Alcune del ritardo è attribuita alla logica di elaborazione inerente. Di conseguenza, il monitoraggio di tendenza al rialzo è molto più importante il valore assoluto di monitoraggio. Il ritardo di stato stazionario deve essere trattato dalla progettazione dell'applicazione, non nel monitoraggio o avvisi.

In caso di errore, i log attività e [log di diagnostica](stream-analytics-job-diagnostic-logs.md) sono i migliori articoli da avviare la ricerca di errori.

## <a name="build-resilient-and-mission-critical-applications"></a>Compilazione resilienti e applicazioni mission-critical

Indipendentemente dal contratto Analitica Stream di Azure e attenzione a come si esegue l'applicazione end-to-end, si verificano interruzioni. Se l'applicazione è mission-critical, è necessario per prepararti per poter risolvere normalmente interruzioni.

Per gli avvisi delle applicazioni, la cosa più importante è rilevare l'avviso successivo. È possibile scegliere di riavviare il processo dall'insieme corrente di tempo durante il ripristino, ignorando avvisi precedenti. La semantica di ora di inizio processo è per la prima volta di output, non al primo input. Con le versioni precedenti, l'input viene riavvolto una quantità appropriata di tempo per garantire che il primo output all'ora specificata è completa e corretta. Si non ottiene le aggregazioni parziali e attivare avvisi in modo imprevisto di conseguenza.

È anche possibile avviare output da alcuni quantità di tempo nel passato. I criteri di conservazione sia hub eventi e dell'IoT Hub contengono una quantità ragionevole di dati per consentire l'elaborazione degli ultimi. Il compromesso è la velocità con cui è possibile risincronizzare l'ora corrente e inizia a generare nuovi avvisi tempestivi. Dati perde il relativo valore rapidamente nel corso del tempo, pertanto è importante acquisire rapidamente all'ora corrente. Esistono due modi per acquisire rapidamente:

- Effettuare il provisioning di altre risorse (SU) quando si esegue l'aggiornamento.
- Riavviare ora corrente.

Il riavvio da corrente il tempo è semplice da eseguire, con il compromesso di lasciare uno spazio vuoto durante l'elaborazione. Il riavvio in questo modo risulta appropriato per gli avvisi di scenari, ma può risultare problematico per gli scenari di dashboard e un non-utilità di avvio per l'archiviazione e scenari di data warehousing.

Il provisioning di altre risorse possa velocizzare il processo, ma l'effetto della presenza di un aumento di velocità di elaborazione è complesso.

- Verificare che il processo è scalabile per un numero maggiore di unità di streaming. Non tutte le query sono scalabili. È necessario assicurarsi che la query viene [parallelizzata](stream-analytics-parallelization.md).

- Assicurarsi che non vi siano partizioni sufficienti nell'hub eventi a monte o l'IoT Hub che è possibile aggiungere altre unità elaborate (tu) predeterminate per scalare la velocità effettiva di input. Tenere presente che ogni unità Elaborata hub eventi superarne con una frequenza di output di 2 MB/s.

- Assicurarsi che si è effettuato il provisioning di risorse sufficienti nel sink di output (Database SQL, Cosmos DB), in modo che non limita il picco nell'output, che può essere talvolta causare il blocco del sistema.

La cosa più importante è prevedere la modifica di velocità di elaborazione, testare questi scenari prima di entrare in produzione e sia pronto per ridimensionare l'elaborazione correttamente durante la fase di ripristino di un errore.

Nello scenario estremo che siano tutti gli eventi in ingresso posticipato [è possibile all il ritardo degli eventi viene eliminati](stream-analytics-time-handling.md) se una finestra in arrivo in ritardo sono stati applicati al processo. L'eliminazione degli eventi può sembrare un comportamento misterioso all'inizio; Tuttavia, poiché si tratta di Analitica Stream è un motore di elaborazione in tempo reale, è previsto che gli eventi in ingresso da vicino il tempo di clock. È necessario eliminare gli eventi che violano tali vincoli.

### <a name="backfilling-process"></a>Processo di recupero delle informazioni

Per fortuna, il modello di archiviazione di dati precedente è utilizzabile per elaborare correttamente questi eventi in ritardo. L'idea è che il processo di archiviazione elabora gli eventi in ingresso nell'ora di arrivo e gli archivi di eventi in bucket il momento giusto in Blob di Azure o Azure Data Lake Store con loro ora dell'evento. Non è importante qual è l'ultima riceve un evento, non verrà mai eliminato. Visualizzata sempre nell'intervallo di tempo a destra. Durante il ripristino, è possibile rielaborare gli eventi archiviati e recupero delle informazioni dei risultati nell'archivio di scelta.

![Recupero delle informazioni ASA](media/stream-analytics-solution-patterns/backfill.png)

Il processo di recupero delle informazioni deve essere eseguita con un sistema, che probabilmente ha un modello di programmazione diverso rispetto a Azure Stream Analitica di elaborazione batch non in linea. Ciò significa che devi implementare nuovamente la logica di elaborazione intero.

Per il recupero delle informazioni, è comunque importante effettuare almeno temporaneamente il provisioning di risorse maggiore all'output di sink per gestire una velocità effettiva superiore rispetto allo stato stazionario esigenze di elaborazione.

|Scenari  |Riavviare ora a partire da  |Riavvia dall'ultimo arresto |Il riavvio da ora e recupero delle informazioni con gli eventi archiviati|
|---------|---------|---------|---------|
|**Dashboarding**   |Consente di creare gap    |OK per breve interruzione del servizio    |Utilizzo per prolungate |
|**Gli avvisi**   |Accettabile |OK per breve interruzione del servizio    |Non è necessario |
|**App di determinazione dell'origine evento** |Accettabile |OK per breve interruzione del servizio    |Utilizzo per prolungate |
|**Data warehousing**   |Perdita di dati  |Accettabile |Non è necessario |
|**Non in linea analitica**  |Perdita di dati  |Accettabile |Non è necessario|

## <a name="putting-it-all-together"></a>Riassumendo

Non è difficile immaginare che tutti i modelli di soluzioni indicato in precedenza possono essere combinati in un sistema end-to-end complesso. Il sistema combinato può includere dashboard, avvisi, applicazione di determinazione dell'origine evento, data warehouse e le funzionalità offline di analitica.

La chiave consiste nel progettare il sistema in modelli componibili, in modo che ogni sottosistema può essere compilato, testato, aggiornamento e ripristinare in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

È ora fornita un'ampia gamma di modelli di soluzioni che usano Azure Stream Analitica. È ora possibile approfondire ulteriormente l'argomento e creare il primo processo di Analisi di flusso:

* [Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md).
* [Creare un processo di Analisi di flusso tramite Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md).
