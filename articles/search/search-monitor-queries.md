---
title: Monitorare le query
titleSuffix: Azure Cognitive Search
description: Monitorare le metriche delle query per le prestazioni e la velocità effettiva. Raccogliere e analizzare gli input delle stringhe di query nei log di diagnostica.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462523"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorare le richieste di query in Ricerca cognitiva di AzureMonitor query requests in Azure Cognitive Search

Questo articolo illustra come misurare le prestazioni e il volume delle query usando le metriche e la registrazione diagnostica. Viene inoltre illustrato come raccogliere i termini di input utilizzati nelle query, ovvero le informazioni necessarie quando è necessario valutare l'utilità e l'efficacia del corpo di ricerca.

I dati cronologici che vengono inseriti nelle metriche vengono conservati per 30 giorni. Per una conservazione più lunga o per creare report sui dati operativi e sulle stringhe di query, assicurarsi di abilitare [un'impostazione di diagnostica](search-monitor-logs.md) che specifichi un'opzione di archiviazione per rendere persistenti gli eventi e le metriche registrati.

Le condizioni che massimizzano l'integrità della misurazione dei dati includono:

+ Usare un servizio fatturabile (un servizio creato a livello Basic o Standard). Il servizio gratuito è condiviso da più abbonati, che introduce una certa quantità di volatilità come spostamento dei carichi.

+ Utilizzare una singola replica e partizione, se possibile, per creare un ambiente indipendente e indipendente. Se si usano più repliche, la media delle metriche di query viene mediata su più nodi, riducendo così la precisione dei risultati. Analogamente, più partizioni indicano che i dati sono divisi, con il potenziale che alcune partizioni potrebbero avere dati diversi se anche l'indicizzazione è in corso. Quando si ottimizzano le prestazioni delle query, un singolo nodo e una partizione forniscono un ambiente più stabile per il test.

> [!Tip]
> Con codice lato client aggiuntivo e Application Insights, è anche possibile acquisire i dati clickthrough per informazioni più approfondite su ciò che attira l'interesse degli utenti dell'applicazione. Per altre informazioni, vedere [Analisi del traffico di ricerca](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Volume di query (QPS)

Il volume viene misurato come query di **ricerca al secondo** (QPS), una metrica incorporata che può essere segnalata come valori medi, count, minimum o massimi di query eseguite entro un intervallo di un minuto. Gli intervalli di un minuto (TimeGrain - "PT1M") per le metriche sono fissi all'interno del sistema.

È comune che le query vengano eseguite in millisecondi, pertanto nelle metriche verranno visualizzate solo le query che vengono misurate come secondi.

| Tipo di aggregazione | Descrizione |
|------------------|-------------|
| Media | Numero medio di secondi all'interno di un minuto durante il quale si è verificata l'esecuzione della query.|
| Conteggio | Il numero di metriche generate nel log entro l'intervallo di un minuto. |
| Massimo | Il numero più alto di query di ricerca al secondo registrate in un minuto. |
| Minima | Il numero minimo di query di ricerca al secondo registrate in un minuto.  |
| SUM | Somma di tutte le query eseguite entro il minuto.  |

Ad esempio, entro un minuto, si potrebbe avere un modello come questo: un secondo di carico elevato che è il massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine un secondo con una sola query, che è il minimo.

Un altro esempio: se un nodo genera 100 metriche, dove il valore di ogni metrica è 40, "Count" è 100, "Sum" è 4000, "Average" è 40 e "Max" è 40.

## <a name="query-performance"></a>Prestazioni delle query

Le prestazioni delle query a livello di servizio vengono misurate come latenza di ricerca (quanto tempo richiede il completamento di una query) e query limitate che sono state eliminate in seguito a conflitti di risorse.

### <a name="search-latency"></a>Latenza di ricerca

| Tipo di aggregazione | Latenza | 
|------------------|---------|
| Media | Durata media della query in millisecondi. | 
| Conteggio | Il numero di metriche generate nel log entro l'intervallo di un minuto. |
| Massimo | Query con esecuzione più lunga dell'esempio. | 
| Minima | Query in esecuzione più breve nell'esempio.  | 
| Totale | Tempo di esecuzione totale di tutte le query nell'esempio, in esecuzione entro l'intervallo (un minuto).  |

Si consideri l'esempio seguente di metriche di latenza di **ricerca:** 86 query sono state campionate, con una durata media di 23,26 millisecondi. Un minimo di 0 indica che alcune query sono state eliminate. Il completamento della query con esecuzione più lunga ha richiesto 1000 millisecondi. Il tempo di esecuzione totale è stato di 2 secondi.

![Aggregazioni di latenzaLatency aggregations](./media/search-monitor-usage/metrics-latency.png "Aggregazioni di latenzaLatency aggregations")

### <a name="throttled-queries"></a>Query limitate

Le query limitate si riferiscono alle query che vengono eliminate anziché al processo. Nella maggior parte dei casi, la limitazione delle richieste è una parte normale dell'esecuzione del servizio.  Non è necessariamente un'indicazione che c'è qualcosa che non va.

La limitazione si verifica quando il numero di richieste attualmente elaborate supera le risorse disponibili. È possibile che si verifichi un aumento delle richieste limitate quando una replica viene estroattiva o durante l'indicizzazione. Sia le richieste di query che le richieste di indicizzazione vengono gestite dallo stesso set di risorse.

Il servizio determina se eliminare le richieste in base all'utilizzo delle risorse. La percentuale di risorse utilizzate in memoria, CPU e I/O su disco viene mediata in un periodo di tempo. Se questa percentuale supera una soglia, tutte le richieste all'indice vengono limitate fino a quando il volume di richieste non viene ridotto. 

A seconda del client, una richiesta limitata può essere indicata nei seguenti modi:

+ Un servizio restituisce un errore "Si stanno inviando troppe richieste. Riprovare più tardi". 
+ Un servizio restituisce un codice di errore 503 che indica che il servizio non è attualmente disponibile. 
+ Se si utilizza il portale (ad esempio, Search Explorer), la query viene eliminata automaticamente e sarà necessario fare di nuovo clic su Cerca.

Per verificare la limitazione delle query, usare la metrica Query di **ricerca limitate.** È possibile esplorare le metriche nel portale o creare una metrica di avviso come descritto in questo articolo. Per le query eliminate all'interno dell'intervallo di campionamento, utilizzare *Totale* per ottenere la percentuale di query non eseguite.

| Tipo di aggregazione | Limitazione |
|------------------|-----------|
| Media | Percentuale di query eliminate all'interno dell'intervallo. |
| Conteggio | Il numero di metriche generate nel log entro l'intervallo di un minuto. |
| Massimo | Percentuale di query eliminate all'interno dell'intervallo.|
| Minima | Percentuale di query eliminate all'interno dell'intervallo. |
| Totale | Percentuale di query eliminate all'interno dell'intervallo. |

Per Percentuale query di ricerca limitata , **minima,** massima, media e totale, tutte hanno lo stesso valore: la percentuale di query di ricerca limitata, rispetto al numero totale di query di ricerca durante un minuto.

Nella schermata seguente, il primo numero è il conteggio (o il numero di metriche inviate al log). Le aggregazioni aggiuntive, visualizzate nella parte superiore o al passaggio del mouse sulla metrica, includono media, massima e totale. In questo esempio non sono state richieste di eliminazione.

![Aggregazioni limitateThrottled aggregations](./media/search-monitor-usage/metrics-throttle.png "Aggregazioni limitateThrottled aggregations")

## <a name="explore-metrics-in-the-portal"></a>Esplorare le metriche nel portaleExplore metrics in the portal

Per un rapido sguardo ai numeri correnti, la scheda **Monitoraggio** nella pagina Panoramica del servizio mostra tre metrie (**Latenza di ricerca**, Query di ricerca al secondo (per unità di **ricerca),** **Percentuale**query di ricerca limitata ) su intervalli fissi misurati in ore, giorni e settimane, con la possibilità di modificare il tipo di aggregazione.

Per un'esplorazione più approfondita, aprire Esplora metriche dal menu **Monitoraggio** in modo da poter sovrapporre, ingrandire e visualizzare i dati per esplorare tendenze o anomalie. Per ulteriori informazioni su Esplora metriche, completare questa [esercitazione sulla creazione di un grafico delle metriche.](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)

1. Nella sezione Monitoraggio selezionare **Metriche** per aprire Esplora metriche con l'ambito impostato per il servizio di ricerca.

1. In Metrica scegliere uno dall'elenco a discesa ed esaminare l'elenco delle aggregazioni disponibili per un tipo preferito. L'aggregazione definisce il modo in cui i valori raccolti verranno campionati in ogni intervallo di tempo.

   ![Esplora metriche per la metrica QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Esplora metriche per la metrica QPS")

1. Nell'angolo in alto a destra, impostare l'intervallo di tempo.

1. Scegliere una visualizzazione. L'impostazione predefinita è un grafico a linee.

1. Sovrapporre aggregazioni aggiuntive scegliendo **Aggiungi metrica** e selezionando aggregazioni diverse.

1. Ingrandire un'area di interesse nel grafico a linee. Posizionare il puntatore del mouse all'inizio dell'area, fare clic e tenendo premuto il pulsante sinistro del mouse, trascinare l'altro lato dell'area e rilasciare il pulsante. Il grafico ingrandirà tale intervallo di tempo.

## <a name="identify-strings-used-in-queries"></a>Identificare le stringhe utilizzate nelle query

Quando si abilita la registrazione diagnostica, il sistema acquisisce le richieste di query nella tabella **AzureDiagnostics.When** you enable diagnostic logging, the system captures query requests in the AzureDiagnostics table. Come prerequisito, è necessario aver già abilitato [la registrazione diagnostica](search-monitor-logs.md), specificando un'area di lavoro di log analytics o un'altra opzione di archiviazione.

1. Nella sezione Monitoraggio selezionare Registri per aprire una finestra di query vuota in Log Analytics.Under the Monitoring section, select **Logs** to open up an empty query window in Log Analytics.

1. Eseguire l'espressione seguente per eseguire la ricerca nelle operazioni Query.Search, restituendo un set di risultati tabulare costituito dal nome dell'operazione, dalla stringa di query, dall'indice sottoposto a query e dal numero di documenti trovati. Le ultime due istruzioni escludono le stringhe di query costituite da una ricerca vuota o non specificata, su un indice di esempio, che riduce il rumore nei risultati.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Facoltativamente, impostare un filtro di colonna in *base ai Query_s* per eseguire la ricerca in base a una stringa o una sintassi specifica. Ad esempio, è possibile filtrare è *uguale a* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Stringhe di query registrate](./media/search-monitor-usage/log-query-strings.png "Stringhe di query registrate")

Sebbene questa tecnica funzioni per l'analisi ad hoc, la creazione di un report consente di consolidare e presentare le stringhe di query in un layout più favorevole all'analisi.

## <a name="identify-long-running-queries"></a>Identificare le query a esecuzione prolungata

Aggiungere la colonna duration per ottenere i numeri per tutte le query, non solo quelle che vengono prelevate come metrica. L'ordinamento di questi dati mostra le query che richiedono più tempo per essere completate.

1. Nella sezione Monitoraggio selezionare **Registri** in cui eseguire query per le informazioni del log.

1. Eseguire la query seguente per restituire le query, ordinate per durata in millisecondi. Le query più longeve si trovano nella parte superiore.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Ordinare le query per durata](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Ordinare le query per durata")

## <a name="create-a-metric-alert"></a>Creare un avviso per la metrica

Un avviso metrico stabilisce una soglia in corrispondenza della quale si riceverà una notifica o si attiverà un'azione correttiva definita in anticipo. 

Per un servizio di ricerca, è comune creare un avviso metrico per la latenza di ricerca e le query limitate. Se si conosce quando le query vengono eliminate, è possibile cercare rimedi che riducono il carico o aumentano la capacità. Ad esempio, se le query limitate aumentano durante l'indicizzazione, è possibile posticiporlo fino a quando l'attività di query non diminuisce.

Quando si superano i limiti di una particolare configurazione di partizione di replica, è utile impostare avvisi per le soglie del volume di query (QPS).

1. Nella sezione Monitoraggio selezionare **Avvisi** e quindi fare clic su **Nuova regola di avviso.** Assicurarsi che il servizio di ricerca sia selezionato come risorsa.

1. In Condizione fare clic su **Aggiungi**.

1. Configurare la logica del segnale. Per il tipo di segnale, scegliere **le metriche** e quindi selezionare il segnale.

1. Dopo aver selezionato il segnale, è possibile utilizzare un grafico per visualizzare i dati cronologici per una decisione informata su come procedere con l'impostazione delle condizioni.

1. Scorrere quindi verso il basso fino a Logica di avviso. Per il proof-of-concept, è possibile specificare un valore artificialmente basso a scopo di test.

   ![Logica di avviso](./media/search-monitor-usage/alert-logic-qps.png "Logica di avviso")

1. Specificare o creare quindi un gruppo di azioni. Questa è la risposta da richiamare quando viene raggiunta la soglia. Potrebbe essere una notifica push o una risposta automatica.

1. Infine, specificare i dettagli dell'avviso. Assegnare un nome e descrivi l'avviso, assegnare un valore di gravità e specificare se creare la regola in uno stato abilitato o disabilitato.

   ![Dettagli dell'avviso](./media/search-monitor-usage/alert-details.png "Dettagli dell'avviso")

Se è stata specificata una notifica tramite posta elettronica, si riceverà un messaggio di `<your rule name>`posta elettronica da "Microsoft Azure" con una riga dell'oggetto "Azure: Gravità attivata: 3".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Passaggi successivi

Se non l'hai già fatto, consulta le nozioni di base del monitoraggio del servizio di ricerca per scoprire la gamma completa di funzionalità di supervisione.

> [!div class="nextstepaction"]
> [Monitorare le operazioni e l'attività in Ricerca cognitiva di AzureMonitor operations and activity in Azure Cognitive Search](search-monitor-usage.md)