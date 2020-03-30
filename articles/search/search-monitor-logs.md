---
title: Raccogliere i dati del registroCollect log data
titleSuffix: Azure Cognitive Search
description: Raccogliere e analizzare i dati di log abilitando un'impostazione di diagnostica e quindi utilizzare kusto Query Language per esplorare i risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462361"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Raccogliere e analizzare i dati di log per Ricerca cognitiva di AzureCollect and analyze log data for Azure Cognitive Search

I log di diagnostica o operativi forniscono informazioni dettagliate sulle operazioni dettagliate di Ricerca cognitiva di Azure e sono utili per il monitoraggio dei processi del servizio e del carico di lavoro. Internamente, i log esistono nel back-end per un breve periodo di tempo, sufficiente per indagini e analisi se si crea un ticket di supporto. Tuttavia, se si desidera auto-direzione sui dati operativi, è necessario configurare un'impostazione di diagnostica per specificare dove vengono raccolte le informazioni di registrazione.

La configurazione dei log è utile per la diagnostica e la conservazione della cronologia operativa. Dopo aver abilitato la registrazione, è possibile eseguire query o creare report per l'analisi strutturata.

Nella tabella seguente vengono enumerate le opzioni per la raccolta e la persistenza dei dati.

| Risorsa | Utilizzo |
|----------|----------|
| [Area di lavoro Invia a Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Gli eventi e le metriche vengono inviati a un'area di lavoro di Log Analytics, su cui è possibile eseguire query nel portale per restituire informazioni dettagliate. Per un'introduzione, vedere Introduzione ai log di [Monitoraggio di AzureFor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) an introduction, see Get started with Azure Monitor logs |
| [Archivio con archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Gli eventi e le metriche vengono archiviati in un contenitore BLOB e archiviati in file JSON. I log possono essere piuttosto granulari (di ora/minuto), utili per la ricerca di un incidente specifico, ma non per l'analisi a tempo indeterminato. Usare un editor JSON per visualizzare un file di log non elaborato o Power BI per aggregare e visualizzare i dati di log.|
| [Flusso all'hub eventi](https://docs.microsoft.com/azure/event-hubs/) | Gli eventi e le metriche vengono trasmessi a un servizio Hub eventi di Azure.Events and metrics are streamed to an Azure Event Hubs service. Scegliere questa opzione come servizio di raccolta dati alternativo per i log di dimensioni molto grandi. |

Sia i log di Monitoraggio di Azure che l'archiviazione BLOB sono disponibili come servizio gratuito, in modo che sia possibile provarlo gratuitamente per tutta la durata della sottoscrizione di Azure.Both Azure Monitor logs and Blob storage are available as a free service so that you can try it out at no charge for the lifetime of your Azure subscription. L'iscrizione e l'uso di Application Insights sono gratuiti purché le dimensioni dei dati dell'applicazione non superino determinati limiti (vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/monitor/) per informazioni dettagliate).

## <a name="prerequisites"></a>Prerequisiti

Se si usa Log Analytics o Archiviazione di Azure, è possibile creare risorse in anticipo.

+ [Creare un'area di lavoro di analisi dei logCreate a log analytics workspace](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Creare un account di archiviazioneCreate a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati

Le impostazioni di diagnostica specificano la modalità di raccolta degli eventi e delle metriche registrati.

1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.

   ![Impostazioni di diagnostica](./media/search-monitor-usage/diagnostic-settings.png "Impostazioni di diagnostica")

1. Selezionare **: Aggiungi impostazione diagnostica**

1. Selezionare **Log Analytics**, selezionare l'area di lavoro e selezionare **OperationLogs** e **AllMetrics**.

   ![Configurare la raccolta dati](./media/search-monitor-usage/configure-storage.png "Configurare la raccolta dei dati")

1. Salvare l'impostazione.

1. Dopo aver abilitato la registrazione, utilizzare il servizio di ricerca per avviare la generazione di log e metriche. Ci vorrà del tempo prima che gli eventi e le metriche registrati diventino disponibili.

Per Log Analytics, ci saranno alcuni minuti prima che i dati siano disponibili, dopo di che è possibile eseguire query Kusto per restituire i dati. Per ulteriori informazioni, vedere [Monitorare le richieste](search-monitor-logs.md)di query .

Per l'archiviazione BLOB, è necessaria un'ora prima che i contenitori vengano visualizzati nell'archiviazione BLOB. È presente un BLOB ogni ora per ciascun contenitore. I contenitori vengono creati solo quando è presente un'attività da registrare o misurare. I dati che vengono copiati in un account di archiviazione vengono formattati come JSON e inseriti in due contenitori:

+ insights-logs-operationlogs: per i log relativi al traffico ricerca
+ insights-metrics-pt1m: per le metriche

## <a name="query-log-information"></a>Informazioni del log di query

Nei log di diagnostica due tabelle contengono log e metriche per Ricerca cognitiva di Azure: AzureDiagnostics e **AzureMetrics.In**diagnostic logs, two tables contain logs and metrics for Azure Cognitive Search: **AzureDiagnostics** and AzureMetrics .

1. In **Monitoraggio**selezionare **Registri**.

1. Immettere **AzureMetrics** nella finestra della query. Eseguire questa semplice query per acquisire familiarità con i dati raccolti in questa tabella. Scorrere la tabella per visualizzare metriche e valori. Si noti il numero di record nella parte superiore e se il servizio ha raccolto metriche per un certo periodo di tempo, è possibile modificare l'intervallo di tempo per ottenere un set di dati gestibile.

   ![Tabella AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabella AzureMetrics")

1. Immettere la query seguente per restituire un set di risultati tabulare.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Ripetere i passaggi precedenti, iniziando con **AzureDiagnostics** per restituire tutte le colonne a scopo informativo, seguito da una query più selettiva che estrae informazioni più interessanti.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabella AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabella AzureDiagnostics")

## <a name="log-schema"></a>Schema del log

Le strutture di dati che contengono dati di log di Ricerca cognitiva di Azure sono conformi allo schema seguente. 

Per l'archiviazione BLOB, ogni BLOB dispone di un oggetto radice denominato **record** contenente una matrice di oggetti di log. Ogni BLOB contiene record su tutte le operazioni eseguite nell'arco della stessa ora.

Nella tabella seguente è riportato un elenco parziale dei campi comuni alla registrazione diagnostica.

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| timeGenerated (tempogenerato) |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |resourceId in uso |
| operationName |string |"Query.Search" |Nome dell'operazione |
| operationVersion |string |"2019-05-06" |api-version usata |
| category |string |"OperationLogs" |constant |
| resultType |string |"Esito positivo" |Valori possibili: esito positivo o negativo |
| resultSignature |INT |200 |Codice risultato HTTP |
| durationMS |INT |50 |Durata dell'operazione in millisecondi |
| properties |object |Vedere la tabella seguente |Oggetto contenente dati specifici dell'operazione |

### <a name="properties-schema"></a>Schema delle proprietà

Le proprietà seguenti sono specifiche di Ricerca cognitiva di Azure.The properties below are specific to Azure Cognitive Search.

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes('content')/docs" |Endpoint dell'operazione |
| Documents_d |INT |42 |Numero di documenti elaborati |
| IndexName_s |string |"test-index" |Nome dell'indice associato all'operazione |
| Query_s |string |"?search-AzureSearch&$count'true&api-version" |Parametri della query |

## <a name="metrics-schema"></a>Schema delle metriche

Le metriche vengono acquisite per le richieste di query e misurate a intervalli di un minuto. Ogni metrica espone i valori minimi, massimi e medi al minuto. Per ulteriori informazioni, vedere [Monitorare le richieste](search-monitor-queries.md)di query .

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |l'ID della risorsa |
| metricName |string |"Latenza" |Nome della metrica |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| average |INT |64 |Il valore medio dei campioni non elaborati nell'intervallo di tempo metrico, nelle unità in secondi o in percentuale, a seconda della metrica. |
| minimum |INT |37 |Valore minimo dei campioni non elaborati nell'intervallo di tempo metrico, ovvero unità in secondi. |
| maximum |INT |78 |Valore massimo dei campioni non elaborati nell'intervallo di tempo metrico, ovvero unità in secondi.  |
| total |INT |258 |Valore totale dei campioni non elaborati nell'intervallo di tempo metrico, ovvero unità in secondi.  |
| count |INT |4 |Numero di metriche generate da un nodo al log entro l'intervallo di un minuto.  |
| timegrain |string |"PT1M" |Grana temporale della metrica in ISO 8601. |

È comune che le query vengano eseguite in millisecondi, pertanto solo le query che vengono misurate come secondi verranno visualizzate in metriche come QPS.

Per la metrica **Query di ricerca al secondo,** il valore minimo è il valore minimo per le query di ricerca al secondo registrato durante tale minuto. Lo stesso vale anche per il valore massimo. Il valore medio è l'aggregato nel corso dell'intero minuto. Ad esempio, entro un minuto, si potrebbe avere un modello come questo: un secondo di carico elevato che è il massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine un secondo con una sola query, che è il minimo.

Per Percentuale query di ricerca limitata , **minima,** massima, media e totale, tutte hanno lo stesso valore: la percentuale di query di ricerca limitata, rispetto al numero totale di query di ricerca durante un minuto.

## <a name="view-raw-log-files"></a>Visualizzare i file di registro non elaborati

L'archiviazione BLOB viene usata per l'archiviazione dei file di log. È possibile usare qualsiasi editor JSON per visualizzare il file di log. Se non ne è già disponibile uno, è consigliabile usare [Visual Studio Code](https://code.visualstudio.com/download).

1. Nel portale di Azure aprire l'account di archiviazione. 

2. Nel riquadro di spostamento a sinistra fare clic su **BLOB**. Dovrebbero essere disponibili i contenitori **insights-logs-operationlogs** e **insights-metrics-pt1m**. Questi contenitori vengono creati da Ricerca cognitiva di Azure quando i dati del log vengono esportati nell'archivio BLOB.

3. Scorrere la gerarchia di cartelle verso il basso fino a raggiungere il file con estensione JSON.  Usare il menu di scelta rapida per scaricare il file.

Dopo aver scaricato il file, aprirlo in un editor JSON per visualizzare il contenuto.

## <a name="next-steps"></a>Passaggi successivi

Se non l'hai già fatto, consulta le nozioni di base del monitoraggio del servizio di ricerca per scoprire la gamma completa di funzionalità di supervisione.

> [!div class="nextstepaction"]
> [Monitorare le operazioni e l'attività in Ricerca cognitiva di AzureMonitor operations and activity in Azure Cognitive Search](search-monitor-usage.md)