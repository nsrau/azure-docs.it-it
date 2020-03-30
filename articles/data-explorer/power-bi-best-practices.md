---
title: Procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Azure Data Explorer
description: In questo articolo vengono fornite le procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Azure Data Explorer.In this article, you learn best practices for using Power BI to query and visualize Azure Data Explorer data.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251739"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Azure Data Explorer

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. [Power BI](https://docs.microsoft.com/power-bi/) è una soluzione di analisi business che consente di visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Azure Data Explorer offre tre opzioni per la connessione ai dati in Power BI. Usare il [connettore incorporato](power-bi-connector.md), [importare una query da Azure Data Explorer in Power BI](power-bi-imported-query.md)o usare una query [SQL.](power-bi-sql-query.md) Questo articolo fornisce suggerimenti per l'esecuzione di query e la visualizzazione dei dati di Azure Data Explorer con Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Procedure consigliate per l'uso di Power BI 

Quando si utilizzano terabyte di dati non elaborati aggiornati, seguire queste linee guida per mantenere aggiornati e aggiornati i dashboard e i report di Power BI:

* **Viaggio leggero:** in Power BI sono riportati solo i dati necessari per i report. Per un'analisi interattiva approfondita, usare l'interfaccia utente Web di Azure Data Explorer ottimizzata per l'esplorazione ad hoc con Kusto Query Language.For deep interactive [analysis,](web-query-data.md) use the Azure Data Explorer Web UI that is optimized for ad-hoc exploration with the Kusto Query Language.

* **Modello composito:** usare [un modello composito](https://docs.microsoft.com/power-bi/desktop-composite-models) per combinare dati aggregati per dashboard di primo livello con dati non elaborati operativi filtrati. È possibile definire chiaramente quando utilizzare i dati non elaborati e quando utilizzare una vista aggregata. 

* **Modalità di importazione e modalità DirectQuery:** usare la modalità di **importazione** per l'interazione di set di dati più piccoli. Usare la modalità **DirectQuery** per set di dati di grandi dimensioni e aggiornati di frequente. Ad esempio, creare tabelle delle dimensioni utilizzando la modalità **di importazione** poiché sono di piccole dimensioni e non cambiano spesso. Impostare l'intervallo di aggiornamento in base alla frequenza prevista degli aggiornamenti dei dati. Creare tabelle dei fatti usando la modalità **DirectQuery** poiché queste tabelle sono di grandi dimensioni e contengono dati non elaborati. Usare queste tabelle per presentare dati filtrati tramite il [drill-through di](https://docs.microsoft.com/power-bi/desktop-drillthrough)Power BI.

* **Parallelismo:** Azure Data Explorer è una piattaforma di dati scalabile linearmente, pertanto è possibile migliorare le prestazioni del rendering del dashboard aumentando il parallelismo del flusso end-to-end come segue:Parallelism – Azure Data Explorer is a linearly scalable data platform, therefore, you can improve the performance of dashboard rendering by increasing the parallelism of the end-to-end flow as follows:

   * Aumentare il numero di [connessioni simultanee in DirectQuery in Power BI.](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)

   * Utilizzare [una coerenza debole per migliorare il parallelismo.](/azure/kusto/concepts/queryconsistency) Ciò può avere un impatto sull'aggiornamento dei dati.

* **Filtri dei dati effettivi:** usare [i filtri dei dati di sincronizzazione](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) per impedire il caricamento dei report prima di essere pronti. Dopo aver strutturato il set di dati, aver posizionato tutti gli oggetti visivi e contrassegnato tutti i filtri dei dati, è possibile selezionare il filtro dei dati di sincronizzazione per caricare solo i dati necessari.

* Usare i **filtri:** usare il maggior numero possibile di filtri di Power BI per concentrare la ricerca di Azure Data Explorer sulle partizioni di dati pertinenti.

* **Elementi visivi efficienti:** selezionare gli oggetti visivi più performanti per i dati.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Suggerimenti per l'uso del connettore di Azure Data Explorer per Power BI per eseguire query sui datiTips for using the Azure Data Explorer connector for Power BI to query data

La sezione seguente include suggerimenti e suggerimenti per l'uso del linguaggio di query di Kusto con Power BI. Usare il [connettore](power-bi-connector.md) di Azure Data Explorer per Power BI per visualizzare i datiUse the Azure Data Explorer connector for Power BI to visualize data

### <a name="complex-queries-in-power-bi"></a>Query complesse in Power BI

Le query complesse sono più facilmente espresse in Kusto che in Power Query. Devono essere implementati come [funzioni Kusto](/azure/kusto/query/functions)e richiamati in Power BI. Questo metodo è necessario quando `let` si usa DirectQuery con istruzioni nella query Kusto.This method is required when using **DirectQuery** with statements in your Kusto query. Poiché Power BI unisce `let` due query e le istruzioni `join` non possono essere usate con l'operatore, possono verificarsi errori di sintassi. Pertanto, salvare ogni parte del join come funzione Kusto e consentire a Power BI di unire queste due funzioni.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Come simulare un operatore data-ora relativoHow to simulate a relative date-time operator

Power BI non contiene un operatore data-ora *relativo,* ad `ago()`esempio .
Per `ago()`simulare , `DateTime.FixedLocalNow()` usare `#duration` una combinazione di e le funzioni di Power BI.

Invece di questa `ago()` query utilizzando l'operatore:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Utilizzare la query equivalente seguente:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Raggiungere i limiti delle query Kusto 

Per impostazione predefinita, le query Kusto restituiscono fino a 500.000 righe o 64 MB, come descritto in [Limiti di query.](/azure/kusto/concepts/querylimits) È possibile eseguire l'override di queste impostazioni predefinite usando le **opzioni avanzate** nella finestra di connessione di Azure Data Explorer (Kusto):You can override these defaults by using Advanced options in the Azure Data Explorer **(Kusto)** connection window:

![opzioni avanzate](media/power-bi-best-practices/advanced-options.png)

Queste opzioni emettono [le istruzioni set](/azure/kusto/query/setstatement) con la query per modificare i limiti di query predefiniti:

  * Il numero di **record del risultato della query limite** genera un`set truncationmaxrecords`
  * **Limita dimensione dei dati dei risultati della query in Byte** genera un`set truncationmaxsize`
  * **Disabilitare il troncamento del set di risultati** genera un`set notruncation`

### <a name="using-query-parameters"></a>Utilizzo dei parametri di query

È possibile utilizzare i parametri di [query](/azure/kusto/query/queryparametersstatement) per modificare la query in modo dinamico. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Utilizzo di un parametro di query nei dettagli della connessioneUsing a query parameter in the connection details

Utilizzare un parametro di query per filtrare le informazioni nella query e ottimizzare le prestazioni della query.
 
Nella finestra **Modifica query,** **Editor avanzato** **Home** > 

1. Individuare la sezione seguente della query:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Ad esempio:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Sostituire la parte pertinente della query con il parametro. Suddividere la query in più parti e concatenarle utilizzando una e commerciale (&) insieme al parametro .

   Ad esempio, nella query precedente, prenderemo `State == 'ALABAMA'` la parte e `State == '` `'` la dividiamo in: `State` e inseriremo il parametro tra di loro:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Se la query contiene virgolette, codificarle correttamente. Ad esempio, la query seguente: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   apparirà **nell'Editor avanzato** come segue con due virgolette:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Deve essere sostituito con la query seguente con tre virgolette:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Usare un parametro di query nei passaggi della queryUse a query parameter in the query steps

È possibile utilizzare un parametro di query in qualsiasi passaggio di query che lo supporta. Ad esempio, filtrare i risultati in base al valore di un parametro.

![filtrare i risultati utilizzando un parametro](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Non usare l'utilità di pianificazione dell'aggiornamento dati di Power BI per inviare comandi di controllo a Kusto

Power BI include un'utilità di pianificazione dell'aggiornamento dati che può eseguire periodicamente query su un'origine dati. Questo meccanismo non deve essere usato per pianificare i comandi di controllo per Kusto perché Power BI presuppone che tutte le query siano di sola lettura.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI può inviare solo brevi query a&lt;Kusto

Se l'esecuzione di una query in Power BI genera il seguente errore: _"DataSource.Error: Web.Contents non è riuscito a ottenere il contenuto da..."_ la query è probabilmente più di 2000 caratteri. Power BI usa **PowerQuery** per eseguire query su Kusto inviando una richiesta HTTP GET che codifica la query come parte dell'URI recuperato. Pertanto, le query Kusto emesse da Power BI sono limitate alla lunghezza massima di un URI di richiesta (2000 caratteri, meno piccolo offset). In alternativa, è possibile definire una [funzione archiviata](/azure/kusto/query/schema-entities/stored-functions) in Kusto e fare in modo che Power BI usi tale funzione nella query.

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare i dati con il connettore Esplora dati di Azure per Power BI](power-bi-connector.md)




