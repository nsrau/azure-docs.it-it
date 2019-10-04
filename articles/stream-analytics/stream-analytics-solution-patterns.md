---
title: Modelli di soluzioni di analisi di flusso di Azure
description: Informazioni sui diversi modelli di soluzioni comuni per analisi di flusso di Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cbc9ffe9510cf0888e8d8b62ea112b6517117eed
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173031"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Modelli di soluzioni di analisi di flusso di Azure

Analogamente a molti altri servizi in Azure, l'analisi di flusso è particolarmente usata con altri servizi per creare una soluzione end-to-end più ampia. Questo articolo illustra le semplici soluzioni di analisi di flusso di Azure e diversi modelli di architettura. È possibile basarsi su questi modelli per sviluppare soluzioni più complesse. I modelli descritti in questo articolo possono essere usati in un'ampia varietà di scenari. Esempi di modelli specifici dello scenario sono disponibili nelle [architetture delle soluzioni di Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Creare un processo di analisi di flusso per potenziare l'esperienza di dashboard in tempo reale

Con analisi di flusso di Azure è possibile creare rapidamente dashboard e avvisi in tempo reale. Una soluzione semplice inserisce gli eventi dall'hub eventi o dall'hub Internet e [inserisce il dashboard di Power bi con un set di dati di streaming](/power-bi/service-real-time-streaming). Per altre informazioni, vedere l'esercitazione dettagliata [analizzare i dati delle chiamate telefoniche con l'analisi di flusso e visualizzare i risultati in Power BI Dashboard](stream-analytics-manage-job.md).

![Dashboard Power BI ASA](media/stream-analytics-solution-patterns/pbidashboard.png)

Questa soluzione può essere compilata in pochi minuti da portale di Azure. Non è presente alcun codice esteso e viene usato il linguaggio SQL per esprimere la logica di business.

Questo modello di soluzione offre la latenza più bassa dall'origine evento al dashboard Power BI in un browser. Analisi di flusso di Azure è l'unico servizio di Azure con questa funzionalità incorporata.

## <a name="use-sql-for-dashboard"></a>Usare SQL per Dashboard

Il dashboard Power BI offre bassa latenza, ma non può essere usato per produrre report completi Power BI. Un modello di report comune è quello di inviare prima i dati a un database SQL. Usare quindi il connettore SQL di Power BI per eseguire query su SQL per i dati più recenti.

![Dashboard SQL di ASA](media/stream-analytics-solution-patterns/sqldashboard.png)

L'uso del database SQL offre una maggiore flessibilità, ma a scapito di una latenza leggermente più elevata. Questa soluzione è ottimale per i processi con requisiti di latenza maggiori di un secondo. Con questo metodo è possibile ottimizzare le funzionalità di Power BI per suddividere ulteriormente i dati per i report, nonché altre opzioni di visualizzazione. Si ottiene anche la flessibilità di usare altre soluzioni dashboard, ad esempio tableau.

SQL non è un archivio dati a velocità effettiva elevata. La velocità effettiva massima per un database SQL da analisi di flusso di Azure è attualmente di circa 24 MB/s. Se le origini eventi nella soluzione producono dati a una velocità più elevata, è necessario usare la logica di elaborazione in analisi di flusso per ridurre la velocità di output a SQL. È possibile utilizzare tecniche quali filtro, aggregazioni finestra, criteri di ricerca con join temporali e funzioni analitiche. La velocità di output per SQL può essere ulteriormente ottimizzata usando le tecniche descritte nell' [output di analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporare informazioni dettagliate in tempo reale sull'applicazione con la messaggistica degli eventi

Il secondo uso più comune di analisi di flusso è quello di generare avvisi in tempo reale. In questo modello di soluzione, la logica di business in analisi di flusso può essere usata per rilevare i [modelli temporali e spaziali o le](stream-analytics-geospatial-functions.md) [anomalie](stream-analytics-machine-learning-anomaly-detection.md), quindi generare segnali di avviso. Tuttavia, a differenza della soluzione dashboard in cui analisi di flusso USA Power BI come endpoint preferito, è possibile usare un numero di sink di dati intermedi. Questi sink includono Hub eventi, bus di servizio e funzioni di Azure. Il generatore di applicazioni deve decidere quale sink di dati funziona meglio per lo scenario in uso.

Per generare avvisi nel flusso di lavoro aziendale esistente, è necessario implementare la logica del consumer di eventi downstream. Poiché è possibile implementare la logica personalizzata in funzioni di Azure, funzioni di Azure rappresenta il modo più rapido per eseguire questa integrazione. Un'esercitazione per l'uso di funzioni di Azure come output per un processo di analisi di flusso è disponibile in [eseguire funzioni di Azure da processi di analisi di flusso di Azure](stream-analytics-with-azure-functions.md). Funzioni di Azure supporta anche vari tipi di notifiche, tra cui testo e posta elettronica. L'app per la logica può essere usata anche per questa integrazione, con hub eventi tra analisi di flusso e app per la logica.

![App messaggistica eventi ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Hub eventi, d'altra parte, offre il punto di integrazione più flessibile. Molti altri servizi, ad esempio Esplora dati di Azure e Time Series Insights possono usare gli eventi da Hub eventi. I servizi possono essere connessi direttamente al sink di hub eventi da analisi di flusso di Azure per completare la soluzione. Hub eventi è anche il broker di messaggistica con velocità effettiva più elevata disponibile in Azure per scenari di integrazione di questo tipo.

## <a name="dynamic-applications-and-websites"></a>Applicazioni e siti Web dinamici

È possibile creare visualizzazioni in tempo reale personalizzate, ad esempio dashboard o visualizzazione mappa, usando analisi di flusso di Azure e il servizio Azure SignalR. Con SignalR è possibile aggiornare i client Web e visualizzare il contenuto dinamico in tempo reale.

![App dinamica ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporare informazioni dettagliate in tempo reale sull'applicazione tramite archivi dati

La maggior parte dei servizi Web e delle applicazioni Web attualmente usa un modello di richiesta/risposta per gestire il livello di presentazione. Il modello di richiesta/risposta è semplice da compilare e può essere facilmente ridimensionato con tempi di risposta bassi usando un front-end senza stato e archivi scalabili, come Cosmos DB.

Un volume di dati elevato spesso crea colli di bottiglia delle prestazioni in un sistema basato su CRUD. Il [modello di soluzione Event sourcing](/azure/architecture/patterns/event-sourcing) viene usato per risolvere i colli di bottiglia delle prestazioni. I modelli temporali e le informazioni dettagliate sono anche difficili e inefficienti da estrarre da un archivio dati tradizionale. Le moderne applicazioni basate sui dati con volumi elevati spesso adottano un'architettura basata sul flusso di dati. Analisi di flusso di Azure come motore di calcolo per i dati in movimento è un fulcro di tale architettura.

![App sourcing eventi ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

In questo modello di soluzione, gli eventi vengono elaborati e aggregati in archivi dati da analisi di flusso di Azure. Il livello dell'applicazione interagisce con gli archivi dati usando il modello di richiesta/risposta tradizionale. Grazie alla capacità di analisi di flusso di elaborare un numero elevato di eventi in tempo reale, l'applicazione è altamente scalabile senza la necessità di eseguire il bulk del livello di archivio dati. Il livello di archivio dati è essenzialmente una vista materializzata nel sistema. L' [output di analisi di flusso di Azure in Azure Cosmos DB](stream-analytics-documentdb-output.md) descrive il modo in cui Cosmos DB viene usato come output di analisi di flusso.

Nelle applicazioni reali in cui la logica di elaborazione è complessa ed è necessario aggiornare determinate parti della logica in modo indipendente, è possibile comporre più processi di analisi di flusso insieme a hub eventi come broker di eventi intermediario.

![App di origine eventi complessi ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Questo modello migliora la resilienza e la gestibilità del sistema. Tuttavia, anche se l'analisi di flusso garantisce un'elaborazione esatta una volta, è possibile che gli eventi duplicati si trovino nell'hub eventi intermedio. È importante che il processo di analisi di flusso downstream deduplica gli eventi usando chiavi logiche in una finestra lookback. Per ulteriori informazioni sul recapito degli eventi, vedere riferimento per il [recapito degli eventi](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Usare i dati di riferimento per la personalizzazione dell'applicazione

La funzionalità dati di riferimento di analisi di flusso di Azure è stata progettata in modo specifico per la personalizzazione degli utenti finali, ad esempio soglia di avviso, regole di elaborazione e [georecinzioni](geospatial-scenarios.md). Il livello dell'applicazione può accettare le modifiche dei parametri e archiviarle in un database SQL. Il processo di analisi di flusso esegue periodicamente query per le modifiche dal database e rende accessibili i parametri di personalizzazione tramite un join dei dati di riferimento. Per altre informazioni su come usare i dati di riferimento per la personalizzazione dell'applicazione, vedere dati di riferimento [SQL](sql-reference-data.md) e [join di dati di riferimento](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Questo modello può essere usato anche per implementare un motore regole in cui le soglie delle regole sono definite dai dati di riferimento. Per altre informazioni sulle regole, vedere [elaborare regole basate su soglie configurabili in analisi di flusso di Azure](stream-analytics-threshold-based-rules.md).

![App dati di riferimento ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Aggiungi Machine Learning alle informazioni dettagliate in tempo reale

Il [modello di rilevamento anomalie](stream-analytics-machine-learning-anomaly-detection.md) predefinito di analisi di flusso di Azure è un modo pratico per introdurre machine learning all'applicazione in tempo reale. Per una gamma più ampia di Machine Learning esigenze, vedere [analisi di flusso di Azure si integra con il servizio di](stream-analytics-machine-learning-integration-tutorial.md)assegnazione dei punteggi di Azure Machine Learning.

Per gli utenti avanzati che vogliono incorporare i corsi di formazione online e assegnare punteggi alla stessa pipeline di analisi di flusso, vedere questo esempio di come eseguire questa operazione con la [regressione lineare](stream-analytics-high-frequency-trading.md).

![App Machine Learning ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Data warehousing quasi in tempo reale

Un altro modello comune è il data warehouse in tempo reale, detto anche data warehouse di streaming. Oltre agli eventi che arrivano all'hub eventi e all'hub delle cose dall'applicazione, [analisi di flusso di Azure in esecuzione in IOT Edge](stream-analytics-edge.md) può essere usato per soddisfare la pulizia dei dati, la riduzione dei dati e le esigenze di archiviazione dati e in avanti. Analisi di flusso in esecuzione in IoT Edge può gestire normalmente problemi di connettività e limitazione della larghezza di banda nel sistema. È possibile utilizzare l'adattatore di output SQL per restituire SQL Data Warehouse; Tuttavia, la velocità effettiva massima è limitata a 10 MB/s.

![Data warehousing ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Un modo per migliorare la velocità effettiva con un compromesso di latenza consiste nell'archiviare gli eventi nell'archiviazione BLOB di Azure e quindi [importarli in SQL data warehouse con la](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)modalità di base. È necessario unire manualmente l'output dall'analisi di flusso all'archiviazione BLOB e l'input dall'archiviazione BLOB a SQL Data Warehouse [archiviando i dati in base al timestamp](stream-analytics-custom-path-patterns-blob-storage-output.md) e importando periodicamente.

In questo modello di utilizzo, analisi di flusso di Azure viene usato come motore ETL quasi in tempo reale. Gli eventi di nuovo arrivo vengono continuamente trasformati e archiviati per l'utilizzo del servizio di analisi downstream.

![Data warehousing ad alta velocità effettiva ASA](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archiviazione dei dati in tempo reale per l'analisi

La maggior parte delle attività di data science e analisi si verifica ancora offline. I dati possono essere archiviati da analisi di flusso di Azure tramite Azure Data Lake Store formati di output Gen2 e parquet. Questa funzionalità consente di rimuovere l'attrito per inserire i dati direttamente in Azure Data Lake Analytics, Azure Databricks e Azure HDInsight. Analisi di flusso di Azure viene usato come motore ETL quasi in tempo reale in questa soluzione. È possibile esplorare i dati archiviati in Data Lake usando vari motori di calcolo.

![Analisi offline ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Usare i dati di riferimento per l'arricchimento

L'arricchimento dei dati è spesso un requisito per i motori ETL. Analisi di flusso di Azure supporta l'arricchimento dei dati con [i dati di riferimento](stream-analytics-use-reference-data.md) sia dal database SQL che dall'archiviazione BLOB di Azure. L'arricchimento dei dati può essere eseguito per l'atterraggio dei dati sia Azure Data Lake che SQL Data Warehouse.

![Analisi offline ASA con arricchimento dei dati](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Rendere operativo informazioni dettagliate dai dati archiviati

Se si combina il modello di analisi offline con il modello di applicazione quasi in tempo reale, è possibile creare un ciclo di feedback. Il ciclo di feedback consente all'applicazione di adattarsi automaticamente per modificare i modelli nei dati. Questo ciclo di feedback può essere semplice come modificare il valore di soglia per gli avvisi o complessi come la ripetizione del training dei modelli Machine Learning. È possibile applicare la stessa architettura della soluzione a entrambi i processi ASA in esecuzione nel cloud e in IoT Edge.

![Operatività di ASA Insights](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Come monitorare i processi ASA

È possibile eseguire un processo di analisi di flusso di Azure 24/7 per elaborare continuamente gli eventi in ingresso in tempo reale. La garanzia del tempo di esecuzione è fondamentale per l'integrità dell'applicazione complessiva. Anche se analisi di flusso è l'unico servizio di analisi di flusso nel settore che offre una [garanzia di disponibilità del 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), è comunque possibile che si verifichi un certo livello di tempo di inattività. Nel corso degli anni, analisi di flusso ha introdotto metriche, log e Stati del processo per riflettere l'integrità dei processi. Tutti i servizi vengono esposti tramite il servizio monitoraggio di Azure e possono essere esportati a OMS. Per altre informazioni, vedere informazioni sul [monitoraggio dei processi di analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md).

![Monitoraggio ASA](media/stream-analytics-solution-patterns/monitoring.png)

È necessario monitorare due aspetti principali:

- [Stato processo non riuscito](job-states.md)

    Prima di tutto, è necessario assicurarsi che il processo sia in esecuzione. Senza il processo nello stato in esecuzione, non vengono generate nuove metriche o log. I processi possono passare a uno stato di errore per diversi motivi, tra cui il livello di utilizzo elevato (ad esempio, l'esaurimento delle risorse).

- [Metrica ritardo filigrana](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Questa metrica riflette la distanza dalla pipeline di elaborazione in tempo reale (secondi). Parte del ritardo è attribuita alla logica di elaborazione intrinseca. Di conseguenza, il monitoraggio della tendenza crescente è molto più importante del monitoraggio del valore assoluto. Il ritardo dello stato stazionario dovrebbe essere risolto dalla progettazione dell'applicazione, non dal monitoraggio o dagli avvisi.

In caso di errore, i log attività e i [log di diagnostica](stream-analytics-job-diagnostic-logs.md) sono i punti migliori in cui iniziare la ricerca di errori.

## <a name="build-resilient-and-mission-critical-applications"></a>Creazione di applicazioni resilienti e mission-critical

Indipendentemente dalla garanzia del contratto di sistema di analisi di flusso di Azure e dall'accuratezza dell'esecuzione dell'applicazione end-to-end, si verificano interruzioni. Se l'applicazione è mission-critical, è necessario prepararsi per le interruzioni per poter eseguire il ripristino normalmente.

Per le applicazioni di avviso, l'aspetto più importante è quello di rilevare l'avviso successivo. È possibile scegliere di riavviare il processo dall'ora corrente durante il ripristino, ignorando gli avvisi precedenti. La semantica dell'ora di inizio del processo è la prima ora di output, non il primo tempo di input. L'input viene riavvolto per un periodo di tempo appropriato per garantire che il primo output al momento specificato sia completo e corretto. Di conseguenza, non si ottengono aggregazioni parziali e si attivano avvisi in modo imprevisto.

È anche possibile scegliere di avviare l'output da una certa quantità di tempo nel passato. Entrambi gli hub eventi e i criteri di conservazione dell'hub di gestione delle cose contengono una quantità ragionevole di dati per consentire l'elaborazione del passato. Il compromesso è la velocità con cui è possibile recuperare fino all'ora corrente e iniziare a generare nuovi avvisi tempestivi. I dati perdono il proprio valore rapidamente nel tempo, quindi è importante recuperare rapidamente l'ora corrente. Ci sono due modi per recuperare rapidamente:

- Effettuare il provisioning di altre risorse (SU) durante il recupero.
- Riavviare dall'ora corrente.

Il riavvio da Current è molto semplice, con il compromesso di lasciare un gap durante l'elaborazione. Il riavvio in questo modo potrebbe essere accettabile per gli scenari di avviso, ma può risultare problematico per gli scenari del dashboard e non è un dispositivo di avvio per gli scenari di archiviazione e di data warehousing.

Il provisioning di un maggior numero di risorse può velocizzare il processo, ma l'effetto di un aumento della velocità di elaborazione è complesso.

- Verificare che il processo sia scalabile a un numero maggiore di unità di streaming. Non tutte le query sono scalabili. È necessario assicurarsi che la query sia [parallela](stream-analytics-parallelization.md).

- Verificare che siano presenti partizioni sufficienti nell'hub eventi upstream o nell'hub Internet che è possibile aggiungere altre unità di velocità effettiva (TUs) per ridimensionare la velocità effettiva di input. Tenere presente che ogni hub eventi si Zadok a una velocità di output di 2 MB/s.

- Assicurarsi di avere effettuato il provisioning di risorse sufficienti nei sink di output (ad esempio, il database SQL, Cosmos DB), in modo da non limitare l'aumento dell'output, che talvolta può causare il blocco del sistema.

L'aspetto più importante è quello di anticipare la variazione della velocità di elaborazione, testare questi scenari prima di passare all'ambiente di produzione ed essere pronti a ridimensionare correttamente l'elaborazione durante il tempo di recupero dell'errore.

Nello scenario estremo di ritardo degli eventi in ingresso, è [possibile che tutti gli eventi ritardati vengano eliminati](stream-analytics-time-handling.md) se è stata applicata una finestra di arrivo in ritardo al processo. L'eliminazione degli eventi può sembrare un comportamento misterioso all'inizio; Tuttavia, considerando analisi di flusso è un motore di elaborazione in tempo reale, prevede che gli eventi in ingresso siano vicini al tempo di clock. Deve eliminare gli eventi che violano tali vincoli.

### <a name="lambda-architectures-or-backfill-process"></a>Architetture lambda o processo di recupero

Fortunatamente, il modello di archiviazione dei dati precedente può essere usato per elaborare normalmente questi eventi tardivi. L'idea è che il processo di archiviazione elabora gli eventi in ingresso nell'ora di arrivo e archivia gli eventi nell'intervallo di tempo corretto nel BLOB di Azure o Azure Data Lake Store con l'ora dell'evento. Non è importante la fine dell'arrivo di un evento, ma non verrà mai eliminato. Si troverà sempre nel bucket di tempo giusto. Durante il ripristino, è possibile rielaborare gli eventi archiviati e recuperare i risultati nell'archivio desiderato. Questa operazione è simile al modo in cui vengono implementati i modelli lambda.

![Recupero dell'ASA](media/stream-analytics-solution-patterns/backfill.png)

Il processo di recupero dati deve essere eseguito con un sistema di elaborazione batch offline, che probabilmente ha un modello di programmazione diverso da quello di analisi di flusso di Azure. Ciò significa che è necessario implementare di nuovo l'intera logica di elaborazione.

Per il riempimento, è comunque importante eseguire almeno temporaneamente il provisioning di più risorse nei sink di output per gestire una velocità effettiva maggiore rispetto alle esigenze di elaborazione dello stato stabile.

|Scenari  |Riavvia solo da ora  |Riavvia dall'ora dell'ultimo arresto |Riavvia da ora + recupero con eventi archiviati|
|---------|---------|---------|---------|
|**Dashboarding**   |Crea Gap    |OK per brevi interruzioni    |Usare per un'interruzione prolungata |
|**Invio di avvisi**   |Accettabile |OK per brevi interruzioni    |Non necessario |
|**App origine evento** |Accettabile |OK per brevi interruzioni    |Usare per un'interruzione prolungata |
|**Data warehousing**   |Perdita di dati  |Accettabile |Non necessario |
|**Analisi offline**  |Perdita di dati  |Accettabile |Non necessario|

## <a name="putting-it-all-together"></a>Riassumendo

Non è difficile immaginare che tutti i modelli di soluzione indicati in precedenza possano essere combinati insieme in un sistema end-to-end complesso. Il sistema combinato può includere dashboard, avvisi, applicazioni per l'approvvigionamento di eventi, data warehousing e funzionalità di analisi offline.

La chiave consiste nel progettare il sistema in schemi componibili, in modo che ogni sottosistema possa essere compilato, testato, aggiornato e ripristinato in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stata illustrata un'ampia gamma di modelli di soluzioni con analisi di flusso di Azure. È ora possibile approfondire ulteriormente l'argomento e creare il primo processo di Analisi di flusso:

* [Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md).
* [Creare un processo di Analisi di flusso tramite Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md).
