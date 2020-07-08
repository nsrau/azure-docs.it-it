---
title: Monitorare le query
titleSuffix: Azure Cognitive Search
description: Monitorare le metriche delle query per le prestazioni e la velocità effettiva. Raccogliere e analizzare gli input della stringa di query nei log delle risorse.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f6594bbeb9899a255d0c38b6a5b2a378388501b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552528"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorare le richieste di query in Azure ricerca cognitiva

Questo articolo illustra come misurare le prestazioni e il volume delle query usando le metriche e la registrazione delle risorse. Viene inoltre illustrato come raccogliere i termini di input utilizzati nelle query: informazioni necessarie quando è necessario valutare l'utilità e l'efficacia del corpus di ricerca.

I dati cronologici che vengono inseriti in metriche vengono conservati per 30 giorni. Per un periodo di conservazione più lungo o per creare report sui dati operativi e sulle stringhe di query, assicurarsi di abilitare un' [impostazione di diagnostica](search-monitor-logs.md) che specifichi un'opzione di archiviazione per rendere permanente le metriche e gli eventi registrati.

Le condizioni che ottimizzano l'integrità della misurazione dei dati includono:

+ Usare un servizio fatturabile, ovvero un servizio creato a livello Basic o standard. Il servizio gratuito è condiviso da più sottoscrittori, che introduce una certa quantità di volatilità quando i carichi cambiano.

+ Usare una singola replica e una partizione, se possibile, per creare un ambiente indipendente e isolato. Se si usano più repliche, le metriche delle query vengono calcolate in modo medio tra più nodi, il che può ridurre la precisione dei risultati. Analogamente, più partizioni comportano la suddivisione dei dati, con il potenziale che alcune partizioni potrebbero avere dati diversi se è in corso l'indicizzazione. Quando si ottimizzano le prestazioni delle query, un singolo nodo e una partizione offrono un ambiente più stabile per il test.

> [!Tip]
> Con codice e Application Insights aggiuntivi sul lato client, è anche possibile acquisire i dati click-through per ottenere informazioni più approfondite su ciò che attrae l'interesse degli utenti dell'applicazione. Per altre informazioni, vedere [Analisi del traffico di ricerca](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Volume query (query al secondo)

Il volume viene misurato come **query di ricerca al secondo** (query al secondo), una metrica incorporata che può essere segnalata come valore medio, conteggio, minimo o massimo di query eseguite in una finestra di un minuto. Gli intervalli di un minuto (TimeGrain = "PT1M") per le metriche sono corretti nel sistema.

In genere, le query vengono eseguite in millisecondi, quindi solo le query che misurano come secondi verranno visualizzate nelle metriche.

| Tipo di aggregazione | Descrizione |
|------------------|-------------|
| Media | Numero medio di secondi in un minuto durante i quali si è verificata l'esecuzione della query.|
| Conteggio | Numero di metriche emesse nel log entro l'intervallo di un minuto. |
| Massimo | Il numero più elevato di query di ricerca al secondo registrate nel corso di un minuto. |
| Minimo | Il numero più basso di query di ricerca al secondo registrate per un minuto.  |
| SUM | Somma di tutte le query eseguite entro i minuti.  |

Ad esempio, all'interno di un minuto è possibile che si disponga di un modello simile al seguente: un secondo di carico elevato, che rappresenta il valore massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine da un secondo con una sola query, che corrisponde al valore minimo.

Un altro esempio: se un nodo emette metriche 100, in cui il valore di ogni metrica è 40, "count" è 100, "sum" è 4000, "Average" è 40 e "Max" è 40.

## <a name="query-performance"></a>Prestazioni delle query

A livello di servizio, le prestazioni delle query vengono misurate come latenza di ricerca (tempo necessario per il completamento di una query) e query limitate eliminate in seguito alla contesa di risorse.

### <a name="search-latency"></a>Latenza di ricerca

| Tipo di aggregazione | Latenza | 
|------------------|---------|
| Media | Durata media delle query in millisecondi. | 
| Conteggio | Numero di metriche emesse nel log entro l'intervallo di un minuto. |
| Massimo | Query con esecuzione più lunga nell'esempio. | 
| Minimo | Query con esecuzione più breve nell'esempio.  | 
| Totale | Tempo totale di esecuzione di tutte le query nell'esempio, eseguite entro l'intervallo (un minuto).  |

Si consideri l'esempio seguente di metriche di **latenza di ricerca** : sono state campionate 86 query con una durata media di 23,26 millisecondi. Un valore minimo pari a 0 indica che alcune query sono state eliminate. Per il completamento della query con esecuzione più lunga sono necessari 1000 millisecondi. Il tempo di esecuzione totale è di 2 secondi.

![Aggregazioni di latenza](./media/search-monitor-usage/metrics-latency.png "Aggregazioni di latenza")

### <a name="throttled-queries"></a>Query limitate

Query limitate si riferiscono alle query eliminate anziché al processo. Nella maggior parte dei casi, la limitazione è una parte normale dell'esecuzione del servizio.  Non è necessariamente un'indicazione di un problema.

La limitazione si verifica quando il numero di richieste attualmente elaborate supera le risorse disponibili. È possibile riscontrare un aumento delle richieste limitate quando una replica viene esclusa dalla rotazione o durante l'indicizzazione. Le richieste di query e di indicizzazione vengono gestite dallo stesso set di risorse.

Il servizio determina se eliminare le richieste in base all'utilizzo delle risorse. La percentuale di risorse utilizzate in memoria, CPU e i/o su disco viene calcolata in base a un periodo di tempo. Se questa percentuale supera una soglia, tutte le richieste all'indice sono limitate fino a quando il volume di richieste non viene ridotto. 

A seconda del client, una richiesta limitata può essere indicata nei modi seguenti:

+ Un servizio restituisce un errore indicante che si sta inviando un numero eccessivo di richieste. Riprovare più tardi". 
+ Un servizio restituisce un codice di errore 503 che indica che il servizio non è al momento disponibile. 
+ Se si usa il portale (ad esempio, Esplora ricerche), la query viene eliminata in modo invisibile all'utente ed è necessario fare nuovamente clic su Cerca.

Per confermare le query limitate, usare metrica **query di ricerca limitate** . È possibile esplorare le metriche nel portale o creare una metrica per gli avvisi, come descritto in questo articolo. Per le query che sono state eliminate nell'intervallo di campionamento, utilizzare *Total* per ottenere la percentuale di query che non sono state eseguite.

| Tipo di aggregazione | Limitazione |
|------------------|-----------|
| Media | Percentuale di query eliminate nell'intervallo. |
| Conteggio | Numero di metriche emesse nel log entro l'intervallo di un minuto. |
| Massimo | Percentuale di query eliminate nell'intervallo.|
| Minimo | Percentuale di query eliminate nell'intervallo. |
| Totale | Percentuale di query eliminate nell'intervallo. |

Per le **query di ricerca limitate, percentuale**, minimo, massimo, medio e totale, hanno lo stesso valore: la percentuale di query di ricerca che sono state limitate, dal numero totale di query di ricerca in un minuto.

Nello screenshot seguente il primo numero è il conteggio (o il numero di metriche inviate al log). Le aggregazioni aggiuntive, visualizzate nella parte superiore o al passaggio del mouse sulla metrica, includono Average, Maximum e Total. In questo esempio non è stata eliminata alcuna richiesta.

![Aggregazioni limitate](./media/search-monitor-usage/metrics-throttle.png "Aggregazioni limitate")

## <a name="explore-metrics-in-the-portal"></a>Esplorare le metriche nel portale

Per esaminare rapidamente i numeri correnti, la scheda **monitoraggio** della pagina Panoramica servizio mostra tre metriche (**latenza di ricerca**, query di **ricerca al secondo (per unità di ricerca)**, **percentuale query di ricerca limitate**) su intervalli fissi misurati in ore, giorni e settimane, con l'opzione di modificare il tipo di aggregazione.

Per un'esplorazione più approfondita, aprire Esplora metriche dal menu **monitoraggio** in modo che sia possibile eseguire il livello, lo zoom avanti e la visualizzazione dei dati per esplorare le tendenze o le anomalie. Per altre informazioni su Esplora metriche, completare questa [esercitazione sulla creazione di un grafico delle metriche](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer).

1. Nella sezione monitoraggio selezionare **metriche** per aprire Esplora metriche con l'ambito impostato sul servizio di ricerca.

1. In metrica scegliere una voce dall'elenco a discesa ed esaminare l'elenco delle aggregazioni disponibili per un tipo preferito. L'aggregazione definisce il modo in cui i valori raccolti verranno campionati in ogni intervallo di tempo.

   ![Esplora metriche per la metrica query al secondo](./media/search-monitor-usage/metrics-explorer-qps.png "Esplora metriche per la metrica query al secondo")

1. Nell'angolo in alto a destra impostare l'intervallo di tempo.

1. Scegliere una visualizzazione. Il valore predefinito è un grafico a linee.

1. Livelli di aggregazione aggiuntivi scegliendo **Aggiungi metrica** e selezionando aggregazioni diverse.

1. Esegue lo zoom avanti in un'area di interesse sul grafico a linee. Posizionare il puntatore del mouse all'inizio dell'area, fare clic e tenendo premuto il pulsante sinistro del mouse, trascinare l'altro lato dell'area e rilasciare il pulsante. Il grafico ingrandirà tale intervallo di tempo.

## <a name="identify-strings-used-in-queries"></a>Identificare le stringhe usate nelle query

Quando si Abilita la registrazione delle risorse, il sistema acquisisce le richieste di query nella tabella **AzureDiagnostics** . Come prerequisito, è necessario avere già abilitato la [registrazione delle risorse](search-monitor-logs.md), specificando un'area di lavoro di log Analytics o un'altra opzione di archiviazione.

1. Nella sezione monitoraggio selezionare **registri** per aprire una finestra di query vuota in log Analytics.

1. Eseguire l'espressione seguente per eseguire una ricerca nella query. operazioni di ricerca, restituendo un set di risultati tabulare costituito dal nome dell'operazione, dalla stringa di query, dall'indice sottoposto a query e dal numero di documenti trovati. Le ultime due istruzioni escludono le stringhe di query costituite da una ricerca vuota o non specificata, su un indice di esempio, che riduce il rumore nei risultati.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Facoltativamente, impostare un filtro colonne in *Query_s* per eseguire la ricerca su una sintassi o una stringa specifica. Ad esempio, è possibile filtrare *è uguale a* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Stringhe di query registrate](./media/search-monitor-usage/log-query-strings.png "Stringhe di query registrate")

Sebbene questa tecnica funzioni per l'analisi ad hoc, la creazione di un report consente di consolidare e presentare le stringhe di query in un layout in modo più favorevole all'analisi.

## <a name="identify-long-running-queries"></a>Identificare le query con esecuzione prolungata

Aggiungere la colonna Duration per ottenere i numeri per tutte le query, non solo per quelli che vengono prelevati come metrica. L'ordinamento di questi dati consente di visualizzare le query che hanno più tempo per il completamento.

1. Nella sezione monitoraggio selezionare **log** per eseguire una query per informazioni sul log.

1. Eseguire la query seguente per restituire le query, ordinate in base alla durata in millisecondi. Le query con esecuzione prolungata sono nella parte superiore.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Ordina query per durata](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Ordina query per durata")

## <a name="create-a-metric-alert"></a>Creare un avviso per la metrica

Un avviso di metrica stabilisce una soglia in corrispondenza della quale si riceverà una notifica o si attiverà un'azione correttiva che è possibile definire in anticipo. 

Per un servizio di ricerca, è comune creare un avviso di metrica per la latenza di ricerca e le query limitate. Se si sa quando le query vengono eliminate, è possibile cercare i rimedi che riducono il carico o aumentano la capacità. Se, ad esempio, le query limitate aumentano durante l'indicizzazione, è possibile posticiparla fino a quando l'attività di query non è secondaria.

Quando si esegue il push dei limiti di una particolare configurazione della partizione di replica, è utile anche configurare gli avvisi per le soglie del volume di query (query al secondo).

1. Nella sezione monitoraggio selezionare **avvisi** , quindi fare clic su **+ nuova regola di avviso**. Verificare che il servizio di ricerca sia selezionato come risorsa.

1. In condizione fare clic su **Aggiungi**.

1. Configurare la logica del segnale. Per tipo di segnale, scegliere **metriche** , quindi selezionare il segnale.

1. Dopo aver selezionato il segnale, è possibile utilizzare un grafico per visualizzare i dati cronologici per una decisione consapevole su come procedere con la configurazione delle condizioni.

1. Scorrere quindi verso il basso fino alla logica di avviso. Per il modello di prova, è possibile specificare un valore artificialmente basso a scopo di test.

   ![Logica di avviso](./media/search-monitor-usage/alert-logic-qps.png "Logica di avviso")

1. Successivamente, specificare o creare un gruppo di azione. Si tratta della risposta da richiamare quando viene soddisfatta la soglia. Potrebbe trattarsi di una notifica push o di una risposta automatica.

1. Infine, specificare i dettagli dell'avviso. Denominare e descrivere l'avviso, assegnare un valore di gravità e specificare se creare la regola in uno stato abilitato o disabilitato.

   ![Dettagli dell'avviso](./media/search-monitor-usage/alert-details.png "Dettagli dell'avviso")

Se è stata specificata una notifica di posta elettronica, si riceverà un messaggio di posta elettronica da "Microsoft Azure" con una riga dell'oggetto "Azure: Activated Severity: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, esaminare le nozioni di base del monitoraggio del servizio di ricerca per informazioni sull'intera gamma di funzionalità di supervisione.

> [!div class="nextstepaction"]
> [Monitorare operazioni e attività in Azure ricerca cognitiva](search-monitor-usage.md)