---
title: 'Usare Analytics: il potente strumento di ricerca di Application Insights | Microsoft Docs'
description: 'Utilizzare Analytics: lo strumento di ricerca diagnostica incluso in Application Insights '
services: application-insights
documentationcenter: 
author: danhadari
manager: douge
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2389f1d785abc750dd165303f737a883b3f788d0
ms.openlocfilehash: 6232a80417cf4581f6c6cbe6c11418dc8d0c3407


---
# <a name="using-analytics-in-application-insights"></a>Uso di Analytics in Application Insights
L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analytics.

* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.

## <a name="open-analytics"></a>Aprire Analytics
Nella home page dell'app in Application Insights fare clic su Analytics.

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics-using/001.png)

L'esercitazione inline fornisce alcune informazioni su come procedere.

È disponibile una [panoramica più ampia qui](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Eseguire query sui dati di telemetria
### <a name="write-a-query"></a>Scrivere una query
![Visualizzazione schema](./media/app-insights-analytics-using/150.png)

Iniziare con i nomi delle tabelle elencate a sinistra oppure con l'operatore [range](app-insights-analytics-reference.md#range-operator) o [union](app-insights-analytics-reference.md#union-operator). Usare `|` per creare una pipeline di [operatori](app-insights-analytics-reference.md#queries-and-operators). 

IntelliSense suggerisce gli operatori e gli elementi delle espressioni che è possibile usare. Fare clic sull'icona informazioni o premere CTRL+BARRA SPAZIATRICE per una descrizione più dettagliata ed esempi di utilizzo di ogni elemento.

Vedere la [panoramica del linguaggio di Analytics](app-insights-analytics-tour.md) e le [informazioni di riferimento sul linguaggio](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Eseguire una query
![Esecuzione di una query](./media/app-insights-analytics-using/130.png)

1. Nelle query è possibile usare interruzioni di riga.
2. Posizionare il cursore all'interno o alla fine della query da eseguire.
3. Controllare l'intervallo di tempo della query. È possibile modificarlo o ignorarlo inserendo la propria clausola [`where...timestamp...`](app-insights-analytics-tour.md#time-range) nella query.
3. Fare clic su Vai per eseguire la query.
4. Non inserire righe vuote nella query. È possibile mantenere più query separate in un'unica scheda di query, separandole con righe vuote. Viene eseguita solo la query con il cursore.

### <a name="save-a-query"></a>Salvare una query
![Salvataggio di una query](./media/app-insights-analytics-using/140.png)

1. Salvare il file di query corrente.
2. Aprire un file di query salvato.
3. Creare un nuovo file di query.

## <a name="see-the-details"></a>Visualizzare i dettagli
Espandere una riga dei risultati per visualizzarne l'elenco completo delle proprietà. È possibile espandere ulteriormente qualsiasi proprietà che sia un valore strutturato, ad esempio le dimensioni personalizzate o l'elenco in pila di un'eccezione.

![Espandere una riga](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Disporre i risultati
È possibile ordinare, filtrare, impaginare e raggruppare i risultati restituiti dalla query.

> [!NOTE]
> Le operazioni di ordinamento, raggruppamento e filtro nel browser non eseguono nuovamente la query, ma riorganizzano i risultati restituiti dall'ultima query. 
> 
> Per eseguire queste attività nel server prima che vengano restituiti i risultati, scrivere la query usando gli operatori [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) e [where](app-insights-analytics-reference.md#where-operator).
> 
> 

Selezionare le colonne da visualizzare, trascinare le intestazioni di colonna per ridisporle e ridimensionare le colonne trascinandone i bordi.

![Disporre le colonne](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Ordinare e filtrare elementi
Ordinare i risultati facendo clic sull'intestazione di una colonna. Fare clic di nuovo per applicare l'ordinamento opposto. Fare clic una terza volta per ripristinare l'ordine originale restituito dalla query.

Usare l'icona del filtro per perfezionare la ricerca.

![Ordinare e filtrare le colonne](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Raggruppare elementi
Per ordinare più di una colonna, usare il raggruppamento. Abilitare il raggruppamento e quindi trascinare le intestazioni di colonna nello spazio sopra la tabella.

![Gruppo](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Mancano alcuni risultati?

Se si ritiene che non siano visualizzati tutti i risultati previsti, esistono un paio di possibili motivi.

* **Filtro intervallo di tempo**. Per impostazione predefinita, vengono visualizzati solo i risultati delle ultime 24 ore. È presente un filtro automatico che limita l'intervallo dei risultati recuperati dalle tabelle di origine. 

    È possibile tuttavia modificare il filtro intervallo di tempo usando il menu a discesa.

    In alternativa, è possibile ignorare l'intervallo automatico inserendo la propria clausola [`where  ... timestamp ...` ](app-insights-analytics-reference.md#where-operator) nella query. Ad esempio:

    `requests | where timestamp > ago('2d')`

* **Limite dei risultati**. È previsto un limite di 10.000 righe di risultati restituiti dal portale. Se il numero di risultati supera il limite, verrà visualizzato un avviso. In tal caso, l'ordinamento dei risultati nella tabella non permette sempre di visualizzare i primo o gli ultimi risultati effettivi. 

    È consigliabile evitare di raggiungere il limite. Usare il filtro intervallo di tempo oppure usare gli operatori, ad esempio:

  * [timestamp top 100 by](app-insights-analytics-reference.md#top-operator) 
  * [take 100](app-insights-analytics-reference.md#take-operator)
  * [summarize ](app-insights-analytics-reference.md#summarize-operator) 
  * [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)

Per visualizzare più di 10.000 righe, è consigliabile usare [Esportazione continua](app-insights-export-telemetry.md). Analytics è progettato per l'analisi e non per il recupero di dati non elaborati.

## <a name="diagrams"></a>Diagrammi
Selezionare il tipo di diagramma desiderato:

![Selezionare un tipo di diagramma](./media/app-insights-analytics-using/230.png)

Se sono presenti più colonne dei tipi corretti, è possibile scegliere gli assi X e Y e una colonna di dimensioni in base alla quale dividere i risultati.

Per impostazione predefinita, i risultati vengono inizialmente visualizzati in una tabella e si seleziona il diagramma manualmente. Per selezionare il diagramma, è possibile usare la [direttiva render](app-insights-analytics-reference.md#render-directive) alla fine di una query.

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard
Per aggiungere un diagramma o una tabella a uno dei [dashboard condivisi](app-insights-dashboards.md) , è sufficiente fare clic sulla puntina. Potrebbe essere necessario [aggiornare il pacchetto dei prezzi dell'app](app-insights-pricing.md) per attivare questa funzionalità. 

![Fare clic sulla puntina](./media/app-insights-analytics-using/pin-01.png)

Ciò significa che, quando si crea un dashboard per monitorare le prestazioni o l'utilizzo dei servizi Web, è possibile includere analisi piuttosto complesse insieme ad altre metriche. 

È possibile aggiungere una tabella al dashboard se contiene un massimo di quattro colonne. Verranno visualizzate solo le prime sette righe.

### <a name="dashboard-refresh"></a>Aggiornamento del dashboard
Il grafico aggiunto al dashboard viene aggiornato automaticamente eseguendo di nuovo la query ogni due ore circa.

### <a name="automatic-simplifications"></a>Semplificazioni automatiche

Quando si aggiunge a un grafico al dashboard, vengono applicate al grafico determinate semplificazioni.

**Restrizione di orario:** le query vengono automaticamente limitate agli ultimi 14 giorni. Si tratta dello stesso effetto che si ottiene quando la query include `where timestamp > ago(14d)`.

**Restrizione al numero di bin:** se si visualizza un grafico con numerosi bin discreti, in genere un grafico a barre, i bin meno popolati vengono automaticamente raggruppati in un unico bin "altri". Ad esempio, questa query:

    requests | summarize count_search = count() by client_CountryOrRegion

ha questo aspetto in Analisi:

![Grafico con una lunga coda](./media/app-insights-analytics-using/pin-07.png)

ma quando la si aggiunge un dashboard, è simile alla figura seguente:

![Grafico con bin limitati](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel
Dopo aver eseguito una query, è possibile scaricare un file con estensione csv. Fare clic su **Esporta in Excel**.

## <a name="export-to-power-bi"></a>Esportare in Power BI
Posizionare il cursore in una query e scegliere **Esporta in Power BI**.

![Esportazione da Analisi a Power BI](./media/app-insights-analytics-using/240.png)

Si esegue la query in Power BI. È possibile impostarla in modo che venga aggiornata secondo una pianificazione.

Con Power BI, è possibile creare i dashboard per raggruppare i dati da un'ampia gamma di origini.

[Altre informazioni sull'esportazione in Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Collegamento diretto

Ottenere un collegamento in **Esporta, Condividi collegamento** che è possibile inviare a un altro utente. Purché l'utente abbia [accesso al gruppo di risorse](app-insights-resources-roles-access-control.md), la query verrà aperta nell'interfaccia utente di Analytics.

Nel collegamento, il testo della query appare dopo "?q=", con compressione gzip e codifica in base&64;. È possibile scrivere codice per generare collegamenti diretti da fornire agli utenti. Tuttavia il modo consigliato per eseguire Analytics dal codice è tramite l'uso dell'[API REST](https://dev.applicationinsights.io/).


## <a name="automation"></a>Automazione

Usare l'[API  REST di accesso ai dati](https://dev.applicationinsights.io/) per eseguire le query di Analytics. [Ad esempio](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (con PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

A differenza dell'interfaccia utente di Analytics, l'API REST non aggiunge automaticamente limitazioni di timestamp alle query. Ricordarsi di aggiungere la propria clausola where per evitare di ricevere le risposte di grandi dimensioni.



## <a name="import-data"></a>Importa dati

È possibile importare i dati da un file CSV. Un utilizzo tipico consiste nell'importare i dati statici che è possibile unire alle tabelle dei dati di telemetria. 

Ad esempio, se nei dati di telemetria vengono identificati utenti autenticati tramite un alias o un ID offuscato, è possibile importare una tabella che esegue il mapping degli alias sui nomi reali. Eseguendo un join nei dati di telemetria della richiesta, è possibile identificare gli utenti in base ai nomi reali nei report di Analytics.

### <a name="define-your-data-schema"></a>Definire lo schema dei dati

1. Fare clic su **Impostazioni** (in alto a sinistra) e quindi su **Origini dati**. 
2. Aggiungere un'origine dati, seguendo le istruzioni. Viene chiesto di fornire un esempio dei dati che deve includere almeno dieci righe. È quindi possibile correggere lo schema.

Lo schema definisce un'origine dati che sarà possibile usare per importare le singole tabelle.

### <a name="import-a-table"></a>Importare una tabella

1. Aprire la definizione dell'origine dati dall'elenco.
2. Fare clic su "Carica" e seguire le istruzioni per caricare la tabella. Poiché l'operazione prevede una chiamata a un'API REST risulterà facile da automatizzare. 

La tabella è ora disponibile per l'utilizzo nelle query di Analytics. Verrà visualizzata in Analytics 

### <a name="use-the-table"></a>Usare la tabella

Si supponga che la definizione dell'origine dati sia denominata `usermap` e che abbia due campi, `realName` e `user_AuthenticatedId`. Poiché la tabella `requests` contiene anche un campo denominato `user_AuthenticatedId`, creare un join risulterà facile:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
La tabella di richieste risultante ha una colonna aggiuntiva, `realName`.

### <a name="import-from-logstash"></a>Importare da LogStash

Se si usa [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), è possibile usare Analytics per eseguire query nei log. Usare il [plug-in che invia pipe dei dati in Analytics](https://github.com/Microsoft/logstash-output-application-insights). 



[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]




<!--HONumber=Jan17_HO2-->


