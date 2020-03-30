---
title: Modelli di soluzioni di Analisi di flusso di Azure
description: Informazioni sui modelli di soluzione comuni per Analisi di flusso di Azure, ad esempio dashboarding, messaggistica di eventi, archivi dati, arricchimento dei dati di riferimento e monitoraggio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535783"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Modelli di soluzioni di Analisi di flusso di Azure

Come molti altri servizi in Azure, Analisi di flusso è consigliabile usare con altri servizi per creare una soluzione end-to-end più grande. Questo articolo illustra semplici soluzioni di Analisi di flusso di Azure e vari modelli architetturali. È possibile basarsi su questi modelli per sviluppare soluzioni più complesse. I modelli descritti in questo articolo possono essere utilizzati in un'ampia gamma di scenari. Esempi di modelli specifici dello scenario sono disponibili nelle [architetture delle soluzioni](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)di Azure.Examples of scenario-specific patterns are available on Azure solution architectures .

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Creare un processo di Analisi di flusso per potenziare l'esperienza di dashboarding in tempo realeCreate a Stream Analytics job to power real-time dashboarding experience

Con Analisi di flusso di Azure è possibile creare rapidamente dashboard e avvisi in tempo reale. Una soluzione semplice inserisce eventi dagli hub eventi o dall'hub IoT e alimenta il dashboard di Power BI con un set di dati di [streaming.](/power-bi/service-real-time-streaming) Per altre informazioni, vedere l'esercitazione dettagliata [Analizzare i dati delle telefonate con Analisi di flusso e visualizzare i risultati nel dashboard](stream-analytics-manage-job.md)di Power BI.

![Dashboard di ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Questa soluzione può essere compilata in pochi minuti dal portale di Azure.This solution can be built in just few minutes from Azure portal. Non è prevista una codifica estesa e il linguaggio SQL viene utilizzato per esprimere la logica di business.

Questo modello di soluzione offre la latenza più bassa dall'origine eventi al dashboard di Power BI in un browser. Analisi di flusso di Azure è l'unico servizio di Azure con questa funzionalità incorporata.

## <a name="use-sql-for-dashboard"></a>Usare SQL per il dashboardUse SQL for dashboard

Il dashboard di Power BI offre bassa latenza, ma non può essere usato per produrre report di Power BI completi. Un modello di report comune consiste nell'eseguire prima l'output dei dati in un database SQL. Usare quindi il connettore SQL di Power BI per eseguire query su SQL per i dati più recenti.

![Dashboard SQL di ASA](media/stream-analytics-solution-patterns/sqldashboard.png)

L'utilizzo del database SQL offre maggiore flessibilità, ma a scapito di una latenza leggermente superiore. Questa soluzione è ottimale per i processi con requisiti di latenza superiori a un secondo. Con questo metodo, è possibile ottimizzare le funzionalità di Power BI per suddividere ulteriormente i dati per i report e molte altre opzioni di visualizzazione. È inoltre possibile ottenere la flessibilità di utilizzare altre soluzioni di dashboard, ad esempio Tableau.You also a gain the flexibility of using other dashboard solutions, such as Tableau.

SQL non è un archivio dati ad alta velocità effettiva. La velocità effettiva massima per un database SQL da Analisi di flusso di Azure è attualmente di circa 24 MB/s.The maximum throughput to a SQL database from Azure Stream Analytics is currently around 24 MB/s. Se le origini eventi nella soluzione producono dati a una frequenza superiore, è necessario usare la logica di elaborazione in Analisi di flusso per ridurre la velocità di output a SQL. È possibile utilizzare tecniche quali il filtro, le aggregazioni con finestre, la corrispondenza dei modelli con join temporali e le funzioni analitiche. La velocità di output in SQL può essere ulteriormente ottimizzata usando le tecniche descritte nell'output di Analisi di flusso di Azure nel database SQL di Azure.The output rate to SQL can be further optimized using techniques described in [Azure Stream Analytics output to Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorpora informazioni dettagliate in tempo reale nella tua applicazione con la messaggistica di eventi

Il secondo uso più diffuso di Analisi di flusso è quello di generare avvisi in tempo reale. In questo modello di soluzione, la logica di business in Analisi di flusso può essere utilizzata per rilevare modelli o [anomalie temporali](stream-analytics-machine-learning-anomaly-detection.md) [e spaziali,](stream-analytics-geospatial-functions.md) quindi produrre segnali di avviso. Tuttavia, a differenza della soluzione dashboard in cui Analisi di flusso usa Power BI come endpoint preferito, è possibile usare diversi sink di dati intermedi. Questi sink includono Hub eventi, Bus di servizio e Funzioni di Azure.These sinks include Event Hubs, Service Bus, and Azure Functions. In qualità di generatore di applicazioni, è necessario decidere quale sink di dati funziona meglio per lo scenario.

La logica del consumer di eventi downstream deve essere implementata per generare avvisi nel flusso di lavoro aziendale esistente. Poiché è possibile implementare la logica personalizzata in Funzioni di Azure, Funzioni di Azure è il modo più rapido per eseguire questa integrazione. Un'esercitazione per l'uso della funzione di Azure come output per un processo di Analisi di flusso è disponibile in [Esegui funzioni di Azure dai processi di Analisi di flusso](stream-analytics-with-azure-functions.md)di Azure.A tutorial for using Azure Function as the output for a Stream Analytics job can be found in Run Azure Functions from Azure Stream Analytics jobs . Funzioni di Azure supporta anche vari tipi di notifiche, tra cui testo e posta elettronica. App per la logica può essere utilizzata anche per tale integrazione, con Hub di eventi tra Analisi di flusso e App per la logica.

![App di messaggistica eventi ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Gli hub eventi, d'altra parte, offrono il punto di integrazione più flessibile. Molti altri servizi, ad esempio Azure Data Explorer e Time Series Insights, possono usare eventi dagli hub eventi. I servizi possono essere connessi direttamente al sink hub eventi da Analisi di flusso di Azure per completare la soluzione. Hub eventi è anche il broker di messaggistica con velocità effettiva più elevata disponibile in Azure per tali scenari di integrazione.

## <a name="dynamic-applications-and-websites"></a>Applicazioni dinamiche e siti Web

È possibile creare visualizzazioni in tempo reale personalizzate, ad esempio la visualizzazione di dashboard o mappe, usando Analisi di flusso di Azure e il servizio SignalR di Azure.You can create custom real-time visualizations, such as dashboard or map visualization, using Azure Stream Analytics and Azure SignalR Service. Utilizzando SignalR, i client web possono essere aggiornati e mostrare contenuti dinamici in tempo reale.

![App dinamica ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporare informazioni dettagliate in tempo reale nell'applicazione tramite archivi dati

La maggior parte dei servizi Web e delle applicazioni Web oggi utilizza un modello di richiesta/risposta per servire il livello di presentazione. Il modello di richiesta/risposta è semplice da compilare e può essere facilmente scalato con un tempo di risposta basso utilizzando un frontend senza stato e archivi scalabili, come Cosmos DB.

Volume di dati elevato spesso crea colli di bottiglia delle prestazioni in un sistema basato su CRUD. Il modello di soluzione di [origine eventi](/azure/architecture/patterns/event-sourcing) viene utilizzato per risolvere i colli di bottiglia delle prestazioni. Anche i modelli temporali e le informazioni dettagliate sono difficili e inefficienti da estrarre da un archivio dati tradizionale. Le moderne applicazioni basate su dati ad alto volume spesso adottano un'architettura basata sul flusso di dati. Azure Stream Analytics come motore di calcolo per i dati in movimento è un perno in tale architettura.

![App ASA per l'approvvigionamento di eventi](media/stream-analytics-solution-patterns/eventsourcingapp.png)

In this solution pattern, events are processed and aggregated into data stores by Azure Stream Analytics. Il livello dell'applicazione interagisce con gli archivi dati utilizzando il modello di richiesta/risposta tradizionale. Grazie alla capacità di Analisi di flusso di elaborare un numero elevato di eventi in tempo reale, l'applicazione è altamente scalabile senza la necessità di irrobustirsi al livello dell'archivio dati. Il livello dell'archivio dati è essenzialmente una vista materializzata nel sistema. [L'output](stream-analytics-documentdb-output.md) di Analisi di flusso di Azure in Azure Cosmos DB descrive come Cosmos DB viene usato come output di Analisi di flusso.

Nelle applicazioni reali in cui la logica di elaborazione è complessa ed è necessario aggiornare determinate parti della logica in modo indipendente, più processi di Analisi di flusso possono essere composti insieme a Event Hub come intermediario event broker.

![App di origine di eventi complessi ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Questo modello migliora la resilienza e la gestibilità del sistema. Tuttavia, anche se Analisi di flusso garantisce esattamente una volta elaborazione, c'è una piccola possibilità che gli eventi duplicati possano atterrare negli hub eventi intermedi. È importante che il processo di analisi di flusso downstream dedupe scomporti gli eventi usando le chiavi logiche in una finestra di ricerca. Per altre informazioni sul recapito degli eventi, vedere Informazioni di riferimento sulle [garanzie di recapito degli eventi.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Usare i dati di riferimento per la personalizzazione dell'applicazioneUse reference data for application customization

La funzionalità dei dati di riferimento di Analisi flusso di Azure è progettata specificamente per la personalizzazione dell'utente finale, ad esempio soglia di avviso, regole di elaborazione e [recinti geografici.](geospatial-scenarios.md) Il livello dell'applicazione può accettare le modifiche ai parametri e archiviarle in un database SQL. Il processo analisi di flusso esegue periodicamente query per le modifiche dal database e rende i parametri di personalizzazione accessibili tramite un join di dati di riferimento. Per altre informazioni su come usare i dati di riferimento per la personalizzazione dell'applicazione, vedere Dati di [riferimento SQL](sql-reference-data.md) e join dati di [riferimento.](/stream-analytics-query/reference-data-join-azure-stream-analytics)

Questo modello può essere usato anche per implementare un motore regole in cui le soglie delle regole vengono definite dai dati di riferimento. Per altre informazioni sulle regole, vedere Elaborare regole configurabili basate su soglia in Analisi di flusso di Azure.For more information on rules, see [Process configurable threshold-based rules in Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![App per i dati di riferimento ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Aggiungi Machine Learning alle tue informazioni in tempo reale

Il modello di [rilevamento automatico](stream-analytics-machine-learning-anomaly-detection.md) incorporato di Azure Stream Analytics è un modo pratico per introdurre Machine Learning nell'applicazione in tempo reale. Per una gamma più ampia di esigenze di Machine Learning, vedere Azure Stream Analytics si integra con il servizio di [punteggio di Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

Per gli utenti avanzati che desiderano incorporare formazione e punteggio online nella stessa pipeline di Analisi di flusso, vedere questo esempio di come eseguire questa operazione con la [regressione lineare.](stream-analytics-high-frequency-trading.md)

![App ASA Machine Learning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Stoccaggio dati quasi in tempo reale

Un altro modello comune è il data warehousing in tempo reale, denominato anche data warehouse in streaming. Oltre agli eventi che arrivano agli hub eventi e all'hub IoT dall'applicazione, [Azure Stream Analytics in esecuzione su IoT Edge](stream-analytics-edge.md) può essere usato per soddisfare le esigenze di pulizia dei dati, riduzione dei dati e archiviazione dati e inoltro. Analisi di flusso in esecuzione su IoT Edge è in grado di gestire correttamente i problemi di connettività e limitazione della larghezza di banda nel sistema. L'adattatore di output SQL può essere utilizzato per l'output in SQL Data Warehouse; tuttavia, la velocità effettiva massima è limitata a 10 MB/s.

![Warehousing di dati ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Un modo per migliorare la velocità effettiva con alcuni compromessi di latenza consiste nell'archiviare gli eventi nell'archivio BLOB di Azure e quindi [importarli in SQL Data Warehouse con Polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). È necessario unire manualmente l'output da Analisi di flusso all'archiviazione BLOB e l'input dall'archiviazione BLOB al data warehouse SQL [archiviando i dati tramite timestamp](stream-analytics-custom-path-patterns-blob-storage-output.md) e importando periodicamente.

In questo modello di utilizzo, Analisi di flusso di Azure viene usato come motore ETL quasi in tempo reale. Gli eventi appena arrivati vengono continuamente trasformati e archiviati per l'utilizzo del servizio di analisi a valle.

![Warehousing di dati ad alta velocità effettiva ASA](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archiviazione dei dati in tempo reale per l'analisi

La maggior parte delle attività di analisi e analisi scientifica dei dati vengono comunque eseguite offline. I dati possono essere archiviati da Analisi di flusso di Azure tramite l'output di Azure Data Lake Store Gen2 e i formati di output di Parquet.Data can be archived by Azure Stream Analytics through Azure Data Lake Store Gen2 output and Parquet output formats. Questa funzionalità rimuove l'attrito per inserire i dati direttamente in Azure Data Lake Analytics, Azure Databricks e Azure HDInsight.This capability removes the friction to feed data directly into Azure Data Lake Analytics, Azure Databricks, and Azure HDInsight. Azure Stream Analytics viene usato come motore ETL quasi in tempo reale in questa soluzione. È possibile esplorare i dati archiviati in Data Lake usando vari motori di calcolo.

![Analisi offline ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Utilizzare i dati di riferimento per l'arricchimento

L'arricchimento dei dati è spesso un requisito per i motori ETL. Analisi di flusso di Azure supporta l'arricchimento dei dati con dati di riferimento provenienti sia dal database SQL che dall'archiviazione BLOB di Azure.Azure Stream Analytics supports data enrichment with [reference data](stream-analytics-use-reference-data.md) from both SQL database and Azure Blob storage. Data enrichment can be done for data landing in both Azure Data Lake and SQL Data Warehouse.

![Analisi offline ASA con arricchimento dei dati](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Rendere operativa le informazioni dai dati archiviati

Se si combina il modello di analisi offline con il modello di applicazione quasi in tempo reale, è possibile creare un ciclo di feedback. Il ciclo di feedback consente all'applicazione di regolare automaticamente per la modifica dei modelli nei dati. Questo ciclo di feedback può essere semplice come modificare il valore di soglia per gli avvisi o complesso come riqualificare i modelli di Machine Learning.This feedback loop can be as simple as changing the threshold value for alerting, or as an l' simple as to retraining Machine Learning models. La stessa architettura della soluzione può essere applicata sia ai processi ASA in esecuzione nel cloud che all'Edge Edge Edge.

![Operatività delle informazioni su ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Come monitorare i processi ASA

Un processo di Analisi di flusso di Azure può essere eseguito 24 ore su 24, 7 giorni su 7 per elaborare gli eventi in ingresso continuamente in tempo reale. La sua garanzia di tempo di attività è fondamentale per l'integrità dell'applicazione complessiva. Anche se Analisi di flusso è l'unico servizio di analisi di streaming nel settore che offre una garanzia di disponibilità del [99,9%,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)potresti comunque sostenere un certo livello di tempo di inattività. Nel corso degli anni, Analisi di flusso ha introdotto metriche, log e stati di lavoro per riflettere l'integrità dei processi. Tutti vengono visualizzati tramite il servizio Monitoraggio di Azure e possono essere ulteriormente esportati in OMS. Per ulteriori informazioni, vedere Informazioni sul monitoraggio dei processi di [Analisi di flusso e come monitorare le query.](stream-analytics-monitoring.md)

![Monitoraggio ASA](media/stream-analytics-solution-patterns/monitoring.png)

Ci sono due cose chiave da monitorare:

- [Stato processo non riuscito](job-states.md)

    Innanzitutto, è necessario assicurarsi che il processo sia in esecuzione. Senza il processo nello stato di esecuzione, non vengono generate nuove metriche o log. I processi possono passare a uno stato di errore per vari motivi, tra cui l'utilizzo di un livello di utilizzo elevato delle unità di registrazione, ovvero l'esaurimento delle risorse.

- [Metriche di ritardo della filigrana](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Questa metrica riflette la distanza rispetto alla pipeline di elaborazione nell'ora dell'orologio a muro (secondi). Parte del ritardo è attribuita alla logica di elaborazione intrinseca. Di conseguenza, il monitoraggio della tendenza crescente è molto più importante del monitoraggio del valore assoluto. Il ritardo dello stato costante deve essere risolto dalla progettazione dell'applicazione, non dal monitoraggio o dagli avvisi.

In caso di errore, i log attività e [i log](stream-analytics-job-diagnostic-logs.md) di diagnostica sono i modi migliori per iniziare a cercare errori.

## <a name="build-resilient-and-mission-critical-applications"></a>Crea applicazioni resilienti e mission-critical

Indipendentemente dalla garanzia del contratto di servizio di Azure Stream Analytics e dalla quantità di attenzione con cui si esegue l'applicazione end-to-end, si verificano interruzioni. Se l'applicazione è mission critical, è necessario essere preparati per le interruzioni al fine di ripristinare normalmente.

Per le applicazioni di avviso, la cosa più importante è rilevare l'avviso successivo. È possibile scegliere di riavviare il processo dall'ora corrente durante il ripristino, ignorando gli avvisi passati. La semantica dell'ora di inizio del processo è entro la prima ora di output, non la prima ora di input. L'ingresso viene riavvolto all'indietro per un periodo di tempo appropriato per garantire che il primo output al tempo specificato sia completo e corretto. Di conseguenza, non si otterranno aggregazioni parziali e gli avvisi verranno attivati in modo imprevisto.

È anche possibile scegliere di avviare l'output da una certa quantità di tempo nel passato. Sia gli hub eventi che i criteri di conservazione dell'hub IoT contengono una quantità ragionevole di dati per consentire l'elaborazione dal passato. Il compromesso è la velocità con cui è possibile raggiungere l'ora corrente e iniziare a generare nuovi avvisi tempestivi. I dati perdono rapidamente il suo valore nel tempo, quindi è importante aggiornare rapidamente il tempo corrente. Ci sono due modi per recuperare rapidamente:

- Effettuare il provisioning di più risorse (SU) quando si raggiunge il ritardo.
- Riavviare dall'ora corrente.

Riavviare dal momento corrente il tempo è semplice da fare, con il compromesso di lasciare un gap durante l'elaborazione. Il riavvio in questo modo potrebbe essere OK per gli scenari di avviso, ma può essere problematico per gli scenari di dashboard ed è un utente non di avviamento per gli scenari di archiviazione e data warehousing.

Il provisioning di più risorse può accelerare il processo, ma l'effetto di un aumento della velocità di elaborazione è complesso.

- Verificare che il processo sia scalabile a un numero maggiore di sus. Non tutte le query sono scalabili. È necessario assicurarsi che la query sia [parallelizzata.](stream-analytics-parallelization.md)

- Assicurarsi che siano presenti partizioni sufficienti negli hub eventi upstream o nell'hub IoT in cui è possibile aggiungere altre unità di velocità effettiva (TU) per ridimensionare la velocità effettiva di input. Tenere presente che ogni SERVER di gestione degli hub eventi raggiunge una velocità massima di 2 MB/s.

- Assicurarsi di aver eseguito il provisioning di risorse sufficienti nei sink di output (ad esempio, Database SQL, Cosmos DB), in modo che non limitino l'impennata dell'output, che a volte può causare il blocco del sistema.

La cosa più importante è anticipare la modifica della velocità di elaborazione, testare questi scenari prima di entrare in produzione ed essere pronti a ridimensionare correttamente l'elaborazione durante il tempo di ripristino in caso di errore.

Nello scenario estremo in cui gli eventi in ingresso sono tutti ritardati, [è possibile che tutti gli eventi ritardati vengano eliminati](stream-analytics-time-handling.md) se è stata applicata una finestra in ritardo in arrivo al processo. La caduta degli eventi può sembrare un comportamento misterioso all'inizio; Tuttavia, considerando che Stream Analytics è un motore di elaborazione in tempo reale, prevede che gli eventi in arrivo siano vicini all'ora dell'orologio a muro. Deve eliminare gli eventi che violano questi vincoli.

### <a name="lambda-architectures-or-backfill-process"></a>Architetture Lambda o processo di riempimento

Fortunatamente, il modello di archiviazione dei dati precedente può essere utilizzato per elaborare questi eventi in ritardo in modo normale. L'idea è che il processo di archiviazione elabora gli eventi in arrivo nell'ora di arrivo e archivigli gli eventi nel bucket di tempo corretto nel BLOB di Azure o nell'archivio di Azure Data Lake con l'ora dell'evento. Non importa quanto tardi arriva un evento, non sarà mai eliminato. Atterrerà sempre nel giusto intervallo temporale. Durante il recupero, è possibile rielaborare gli eventi archiviati e riempire i risultati nel negozio di scelta. È simile alla modalità di implementazione dei modelli lambda.

![Riempimento ASA](media/stream-analytics-solution-patterns/backfill.png)

Il processo di riempimento deve essere eseguito con un sistema di elaborazione batch offline, che molto probabilmente ha un modello di programmazione diverso da quello di Azure Stream Analytics.The fill process has to be done with an offline batch processing system, which likely has a different programming model than Azure Stream Analytics. Ciò significa che è necessario implementare nuovamente l'intera logica di elaborazione.

Per il riempimento, è comunque importante eseguire almeno temporaneamente il provisioning di più risorse nei sink di output per gestire una velocità effettiva superiore rispetto alle esigenze di elaborazione dello stato stabile.

|Scenari  |Riavvia da ora solo  |Riavvio dall'ora dell'ultimo arresto |Riavviare da ora: riempimento con gli eventi archiviati|
|---------|---------|---------|---------|
|**Dashboarding**   |Crea spazio    |OK per un'interruzione breve    |Utilizzare per una lunga interruzione |
|**Avvisi**   |Accettabile |OK per un'interruzione breve    |Non necessario |
|**App per l'origine eventi** |Accettabile |OK per un'interruzione breve    |Utilizzare per una lunga interruzione |
|**Data warehousing**   |Perdita di dati  |Accettabile |Non necessario |
|**Analisi offline**  |Perdita di dati  |Accettabile |Non necessario|

## <a name="putting-it-all-together"></a>Riassumendo

Non è difficile immaginare che tutti i modelli di soluzione di cui sopra possano essere combinati insieme in un complesso sistema end-to-end. Il sistema combinato può includere dashboard, avvisi, applicazioni di origine eventi, data warehousing e funzionalità di analisi offline.

La chiave è progettare il sistema in modelli componibili, in modo che ogni sottosistema possa essere costruito, testato, aggiornato e ripristinato in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

È ora che si è osservata una varietà di modelli di soluzione usando Analisi di flusso di Azure.You have now seen a variety of solution patterns using Azure Stream Analytics. È ora possibile approfondire ulteriormente l'argomento e creare il primo processo di Analisi di flusso:

* Creare un processo di [Analisi di flusso tramite il portale](stream-analytics-quick-create-portal.md)di Azure.
* [Creare un processo di Analisi di flusso tramite Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md).
