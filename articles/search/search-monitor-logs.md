---
title: Raccogli dati di log
titleSuffix: Azure Cognitive Search
description: Raccogliere e analizzare i dati di log abilitando un'impostazione di diagnostica e quindi usare il linguaggio di query kusto per esplorare i risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 192591dedb0b5519fdcecde8c8683be87237c828
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127822"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Raccogliere e analizzare i dati di log per Azure ricerca cognitiva

I log di diagnostica o operativi forniscono informazioni approfondite sulle operazioni dettagliate di Azure ricerca cognitiva e sono utili per il monitoraggio dei processi del servizio e del carico di lavoro. Internamente, i log esistono nel back-end per un breve periodo di tempo, sufficiente per indagini e analisi se si crea un ticket di supporto. Tuttavia, se si desidera la direzione automatica sui dati operativi, è necessario configurare un'impostazione di diagnostica per specificare la posizione in cui vengono raccolte le informazioni di registrazione.

La configurazione dei log è utile per la diagnostica e la conservazione della cronologia operativa. Dopo aver abilitato la registrazione, è possibile eseguire query o creare report per l'analisi strutturata.

Nella tabella seguente sono elencate le opzioni per la raccolta e la conservazione dei dati.

| Risorsa | Utilizzo |
|----------|----------|
| [Invia a area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Gli eventi e le metriche vengono inviati a un'area di lavoro Log Analytics, su cui è possibile eseguire query nel portale per restituire informazioni dettagliate. Per informazioni introduttive, vedere Introduzione [ai log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archivia con archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Gli eventi e le metriche vengono archiviati in un contenitore BLOB e archiviati in file JSON. I log possono essere abbastanza granulari (in base all'ora/minuto), utili per la ricerca di un evento imprevisto specifico, ma non per l'analisi aperta. Usare un editor JSON per visualizzare un file di log non elaborato o Power BI per aggregare e visualizzare i dati di log.|
| [Flusso a hub eventi](https://docs.microsoft.com/azure/event-hubs/) | Gli eventi e le metriche vengono trasmessi a un servizio Hub eventi di Azure. Scegliere questa opzione come servizio di raccolta dati alternativo per i log di dimensioni molto grandi. |

I log di monitoraggio di Azure e l'archiviazione BLOB sono disponibili come servizio gratuito, in modo che sia possibile provarli senza costi aggiuntivi per la durata della sottoscrizione di Azure. L'iscrizione e l'uso di Application Insights sono gratuiti purché le dimensioni dei dati dell'applicazione non superino determinati limiti (vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/monitor/) per informazioni dettagliate).

## <a name="prerequisites"></a>Prerequisiti

Se si usa Log Analytics o archiviazione di Azure, è possibile creare le risorse in anticipo.

+ [Creare un'area di lavoro di log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati

Le impostazioni di diagnostica specificano come vengono raccolti gli eventi e le metriche registrate.

1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.

   ![Impostazioni di diagnostica](./media/search-monitor-usage/diagnostic-settings.png "Impostazioni di diagnostica")

1. Selezionare **+ Aggiungi impostazione di diagnostica**

1. Controllare **log Analytics**, selezionare l'area di lavoro e selezionare **OperationLogs** e **AllMetrics**.

   ![Configurare la raccolta dati](./media/search-monitor-usage/configure-storage.png "Configurare la raccolta dei dati")

1. Salvare l'impostazione.

1. Dopo aver abilitato la registrazione, usare il servizio di ricerca per iniziare a generare log e metriche. Prima che le metriche e gli eventi registrati diventino disponibili, l'operazione verrà eseguita.

Per Log Analytics, saranno necessari alcuni minuti prima che i dati siano disponibili, dopo i quali è possibile eseguire query kusto per restituire i dati. Per altre informazioni, vedere [monitorare le richieste di query](search-monitor-logs.md).

Per l'archiviazione BLOB, è necessaria un'ora prima che i contenitori vengano visualizzati nell'archivio BLOB. È presente un BLOB ogni ora per ciascun contenitore. I contenitori vengono creati solo quando è presente un'attività da registrare o misurare. I dati che vengono copiati in un account di archiviazione vengono formattati come JSON e inseriti in due contenitori:

+ insights-logs-operationlogs: per i log relativi al traffico ricerca
+ insights-metrics-pt1m: per le metriche

## <a name="query-log-information"></a>Informazioni sul log di query

Due tabelle contengono log e metriche per ricerca cognitiva di Azure: **AzureDiagnostics** e **AzureMetrics**.

1. In **monitoraggio**selezionare **log**.

1. Immettere **AzureMetrics** nella finestra query. Eseguire questa semplice query per acquisire familiarità con i dati raccolti in questa tabella. Scorrere la tabella per visualizzare le metriche e i valori. Si noti il numero di record nella parte superiore e se il servizio raccoglie le metriche per un certo periodo di tempo, potrebbe essere necessario modificare l'intervallo di tempo per ottenere un set di dati gestibile.

   ![Tabella AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabella AzureMetrics")

1. Immettere la query seguente per restituire un set di risultati tabulare.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Ripetere i passaggi precedenti, a partire da **AzureDiagnostics** per restituire tutte le colonne a scopo informativo, seguito da una query più selettiva che estrae informazioni più interessanti.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabella AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabella AzureDiagnostics")

## <a name="log-schema"></a>Schema del log

Le strutture di dati che contengono dati di log di Azure ricerca cognitiva sono conformi allo schema riportato di seguito. 

Per l'archiviazione BLOB, ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti log. Ogni BLOB contiene record su tutte le operazioni eseguite nell'arco della stessa ora.

La tabella seguente è un elenco parziale dei campi comuni alla registrazione delle risorse.

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| timeGenerated |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| resourceId |stringa |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |resourceId in uso |
| operationName |stringa |"Query.Search" |Nome dell'operazione |
| operationVersion |stringa |"2019-05-06" |api-version usata |
| category |stringa |"OperationLogs" |constant |
| resultType |stringa |"Esito positivo" |Valori possibili: esito positivo o negativo |
| resultSignature |INT |200 |Codice risultato HTTP |
| durationMS |INT |50 |Durata dell'operazione in millisecondi |
| properties |oggetto |Vedere la tabella seguente |Oggetto contenente dati specifici dell'operazione |

### <a name="properties-schema"></a>Schema delle proprietà

Le proprietà seguenti sono specifiche per ricerca cognitiva di Azure.

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| Description_s |stringa |"GET /indexes('content')/docs" |Endpoint dell'operazione |
| Documents_d |INT |42 |Numero di documenti elaborati |
| IndexName_s |stringa |"test-index" |Nome dell'indice associato all'operazione |
| Query_s |stringa |"? Search = AzureSearch&$count = true&API-Version = 2019-05-06" |Parametri della query |

## <a name="metrics-schema"></a>Schema delle metriche

Le metriche vengono acquisite per le richieste di query e misurate in intervalli di un minuto. Ogni metrica espone i valori minimi, massimi e medi al minuto. Per altre informazioni, vedere [monitorare le richieste di query](search-monitor-queries.md).

| Nome | Type | Esempio | Note |
| --- | --- | --- | --- |
| resourceId |stringa |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ID risorsa |
| metricName |stringa |"Latenza" |Nome della metrica |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| average |INT |64 |Valore medio degli esempi non elaborati nell'intervallo di tempo della metrica, unità in secondi o percentuale, a seconda della metrica. |
| minimum |INT |37 |Valore minimo degli esempi non elaborati nell'intervallo di tempo della metrica, unità in secondi. |
| maximum |INT |78 |Valore massimo degli esempi non elaborati nell'intervallo di tempo della metrica, unità in secondi.  |
| total |INT |258 |Il valore totale degli esempi non elaborati nell'intervallo di tempo della metrica, unità in secondi.  |
| count |INT |4 |Numero di metriche emesse da un nodo al log entro l'intervallo di un minuto.  |
| timegrain |stringa |"PT1M" |Granularità temporale della metrica in ISO 8601. |

In genere, le query vengono eseguite in millisecondi, quindi solo le query che vengono misurate come secondi verranno visualizzate in metriche come query al secondo.

Per la metrica **query di ricerca al secondo** , il valore minimo è il valore minimo per le query di ricerca al secondo registrate durante tale minuto. Lo stesso vale anche per il valore massimo. Il valore medio è l'aggregato nel corso dell'intero minuto. Ad esempio, all'interno di un minuto è possibile che si disponga di un modello simile al seguente: un secondo di carico elevato, che rappresenta il valore massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine da un secondo con una sola query, che corrisponde al valore minimo.

Per le **query di ricerca limitate, percentuale**, minimo, massimo, medio e totale, hanno lo stesso valore: la percentuale di query di ricerca che sono state limitate, dal numero totale di query di ricerca in un minuto.

## <a name="view-raw-log-files"></a>Visualizza file di log non elaborati

L'archiviazione BLOB viene utilizzata per l'archiviazione dei file di log. È possibile usare qualsiasi editor JSON per visualizzare il file di log. Se non ne è già disponibile uno, è consigliabile usare [Visual Studio Code](https://code.visualstudio.com/download).

1. Nel portale di Azure aprire l'account di archiviazione. 

2. Nel riquadro di spostamento a sinistra fare clic su **BLOB**. Dovrebbero essere disponibili i contenitori **insights-logs-operationlogs** e **insights-metrics-pt1m**. Questi contenitori vengono creati da Azure ricerca cognitiva quando i dati di log vengono esportati nell'archivio BLOB.

3. Scorrere la gerarchia di cartelle verso il basso fino a raggiungere il file con estensione JSON.  Usare il menu di scelta rapida per scaricare il file.

Dopo aver scaricato il file, aprirlo in un editor JSON per visualizzare il contenuto.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, esaminare le nozioni di base del monitoraggio del servizio di ricerca per informazioni sull'intera gamma di funzionalità di supervisione.

> [!div class="nextstepaction"]
> [Monitorare operazioni e attività in Azure ricerca cognitiva](search-monitor-usage.md)