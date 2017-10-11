---
title: Esaminare le metriche in Azure Application Insights | Microsoft Docs
description: Come interpretare i grafici in Esplora metriche e come personalizzare i pannelli di Esplora metriche.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: bwren
ms.openlocfilehash: a13500284caab79bbe221060ccf3d925ffb1fab5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="exploring-metrics-in-application-insights"></a>Esaminare le metriche in Application Insights
Le metriche in [Application Insights][start] sono valori e conteggi di eventi misurati, inviati nei dati di telemetria dall'applicazione. Consentono di rilevare problemi di prestazioni e osservare le tendenze nella modalità di uso dell'applicazione. Esiste una vasta gamma di metriche standard ed è anche possibile creare metriche ed eventi personalizzati.

Il conteggio delle metriche e degli eventi viene visualizzato nei grafici dei valori aggregati, ad esempio somme, medie o conteggi.

Ecco un esempio di set di grafici:

![](./media/app-insights-metrics-explorer/01-overview.png)

I grafici delle metriche sono disponibili ovunque nel portale di Application Insights. Nella maggior parte dei casi possono essere personalizzati ed è possibile aggiungere altri grafici al pannello. Nel pannello Panoramica fare clic per visualizzare grafici più dettagliati (con titoli come "Server") oppure fare clic su **Esplora metriche** per aprire un nuovo pannello in cui è possibile creare grafici personalizzati.

## <a name="time-range"></a>Intervallo di tempo
È possibile modificare l'intervallo di tempo coperto dai grafici o dalle griglie in tutti i pannelli.

![Aprire il pannello Panoramica dell'applicazione nel portale di Azure](./media/app-insights-metrics-explorer/03-range.png)

Se si è in attesa di alcuni dati non ancora visualizzati, fare clic su Aggiorna. I grafici si aggiornano a intervalli regolari, ma gli intervalli sono più lunghi per gli intervalli di tempo maggiori. Ai dati potrebbe essere necessario un po' di tempo per superare la pipeline di analisi in un grafico.

Per ingrandire una parte di un grafico, trascinare sulla parte:

![Trascinare il puntatore su una parte di un grafico.](./media/app-insights-metrics-explorer/12-drag.png)

Fare clic sul pulsante Annulla zoom per ripristinarlo.

## <a name="granularity-and-point-values"></a>Granularità e valori dei punti
Posizionare il mouse sul grafico per visualizzare i valori delle metriche in quel punto.

![Posizionare il mouse su un grafico](./media/app-insights-metrics-explorer/02-focus.png)

Il valore della metrica in un punto particolare viene aggregato in base all'intervallo di campionamento precedente.

L'intervallo di campionamento o "granularità" è visibile nella parte superiore del pannello.

![Intestazione di un pannello.](./media/app-insights-metrics-explorer/11-grain.png)

È possibile modificare la granularità nel pannello Intervallo di tempo:

![Intestazione di un pannello.](./media/app-insights-metrics-explorer/grain.png)

Le granularità disponibili dipendono dall'intervallo di tempo selezionato. Le granularità esplicite costituiscono alternative alla granularità "automatica" per l'intervallo di tempo.


## <a name="editing-charts-and-grids"></a>Modifica di grafici e griglie
Per aggiungere un nuovo grafico al pannello:

![In Esplora metriche scegliere Aggiungi grafico](./media/app-insights-metrics-explorer/04-add.png)

Selezionare **Modifica** su un grafico nuovo o esistente per modificare il contenuto visualizzato:

![Selezionare una o più metriche](./media/app-insights-metrics-explorer/08-select.png)

È possibile visualizzare più metriche in un grafico, anche se sono presenti restrizioni sulle combinazioni che è possibile visualizzare insieme. Non appena si sceglie una metrica, alcune vengono disabilitate.

Eventuali [metriche personalizzate][track] codificate nell'app (chiamate a TrackMetric e TrackEvent) vengono elencate qui.

## <a name="segment-your-data"></a>Segmentare i dati
È possibile suddividere una metrica per la proprietà, ad esempio eseguire un confronto delle visualizzazioni di una pagina sui client con sistemi operativi differenti.

Selezionare un grafico o una griglia, attivare il raggruppamento e scegliere una proprietà in base a cui eseguire il raggruppamento:

![Attivare il raggruppamento e poi selezionare una proprietà in Raggruppa per](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Quando si utilizza la funzione di raggruppamento, i grafici ad area e a barre forniscono una visualizzazione in pila, che risulta ottimale se il metodo di aggregazione selezionato è Somma. Ma se il tipo di aggregazione selezionato è Media, è consigliabile scegliere i tipi di visualizzazione a righe o a griglia.
>
>

Se si codificano [metriche personalizzate][track] nell'app e si includono valori di proprietà, sarà possibile selezionare le proprietà da questo elenco.

Il grafico è troppo piccolo per dati segmentati? modificarne l'altezza:

![Regolare il dispositivo di scorrimento](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipi di aggregazione
La legenda sul lato mostra di solito per impostazione predefinita il valore aggregato per il periodo del grafico. Se si passa il mouse sul grafico, viene visualizzato il valore in questo punto.

Ogni punto dati del grafico è una funzione di aggregazione dei valori di dati ricevuti nell'intervallo di campionamento o nella "granularità" precedente. La granularità viene visualizzata nella parte superiore del pannello e varia in base alla scala cronologica complessiva del grafico.

Le metriche possono essere aggregate in modi diversi:

* **Conteggio** è un conteggio degli eventi ricevuti nell'intervallo di campionamento. Viene utilizzato per gli eventi come le richieste. Le variazioni in altezza del grafico indicano variazioni della frequenza con cui si verificano gli eventi. Ma si noti che il valore numerico cambia quando si modifica l'intervallo di campionamento.
* **Somma** : esegue la somma dei valori dei punti dati ricevuti tramite l'intervallo di campionamento o il periodo del grafico.
* **Media** : divide la somma per il numero di punti dati ricevuti tramite l'intervallo.
* **Unica** : i conteggi vengono usati per contare gli utenti e gli account. Per tutto l'intervallo di campionamento, o per il periodo del grafico, la cifra mostra il numero di utenti diversi visualizzato in quel momento.
* **%** - le versioni in percentuale di ogni aggregazione vengono utilizzate solo con i grafici segmentati. Il totale raggiunge sempre il 100% e il grafico mostra il contributo relativo dei diversi componenti di un totale.

    ![Aggregazione in percentuale](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Modificare il tipo di aggregazione

![Modificare il grafico, quindi selezionare Aggregazione](./media/app-insights-metrics-explorer/05-aggregation.png)

Il metodo predefinito per ciascuna metrica viene visualizzato quando si crea un nuovo grafico o quando si deselezionano tutte le metriche:

![Deselezionare tutte le metriche per visualizzare le impostazioni predefinite](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Bloccare l'asse Y 
Per impostazione predefinita, in un grafico i valori dell'asse Y sono visualizzati a partire da zero fino ai valori massimi dell'intervallo di dati, per offrire una rappresentazione visiva del quantum dei valori. In alcuni casi tuttavia, più che il quantum, può essere interessante esaminare le modifiche secondarie ai valori. Per personalizzazioni di questo tipo, usare la funzionalità di modifica dell'intervallo dell'asse Y per bloccare il valore minimo o massimo dell'asse Y nel punto desiderato.
Fare clic sulla casella di controllo "Impostazioni avanzate" per visualizzare le impostazioni dell'intervallo dell'asse Y.

![Fare clic su Impostazioni avanzate, selezionare l'intervallo Personalizzato e specificare i valori minino e massimo](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrare i dati
Per visualizzare solo le metriche per un set di valori di proprietà selezionati:

![Fare clic su Filtro, espandere una proprietà e selezionare alcuni valori](./media/app-insights-metrics-explorer/19-filter.png)

Se non si seleziona alcun valore per una determinata proprietà, è come se si selezionassero tutti i valori, ovvero alla proprietà non verrà applicato alcun filtro.

Si noti il numero di eventi vicino a ogni valore della proprietà. Quando si selezionano i valori di una proprietà, i conteggi vicino ad altri valori di proprietà vengono modificati.

I filtri si applicano a tutti i grafici in un pannello. Se si desidera applicare filtri diversi a grafici differenti, creare e salvare pannelli di metriche diversi. Se si desidera, è possibile aggiungere sul dashboard grafici di pannelli diversi, in modo da visualizzarli uno accanto all'altro.

### <a name="remove-bot-and-web-test-traffic"></a>Rimuovere il traffico di bot e test Web
Usare il filtro **Traffico reale o sintetico** e selezionare **Reale**.

È possibile anche filtrare in base a **Origine del traffico sintetico**.

### <a name="to-add-properties-to-the-filter-list"></a>Per aggiungere proprietà all'elenco di filtri
È possibile che si voglia filtrare i dati di telemetria in base a una categoria personalizzata. Ad esempio, è possibile che si dividano gli utenti in categorie diverse e si voglia segmentare i dati in base a queste categorie.

[Creare proprietà personalizzate](app-insights-api-custom-events-metrics.md#properties). Impostazione in un [Inizializzatore di telemetria](app-insights-api-custom-events-metrics.md#defaults) affinché venga visualizzato in tutti i dati di telemetria - compresa la telemetria standard inviata dai diversi moduli SDK.

## <a name="edit-the-chart-type"></a>Modificare il tipo di grafico
Si noti che è possibile passare dalle griglie ai grafici e viceversa:

![Selezionare una griglia o un grafico e quindi scegliere un tipo di grafico](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Salvare il pannello delle metriche
Dopo aver creato alcuni grafici, è possibile salvarli come preferiti. È possibile scegliere se condividerlo con altri membri del team, se si usa un account aziendale.

![Scegliere Preferito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Per visualizzare nuovamente il pannello, **andare al pannello** e aprire Preferiti:

![Nel pannello Panoramica scegliere Preferiti](./media/app-insights-metrics-explorer/22-favorite-get.png)

Se si è scelto l'intervallo di tempo Relativo al momento del salvataggio, il pannello verrà aggiornato con le metriche più recenti. Se si è scelto l'intervallo di tempo Assoluto, verranno visualizzati gli stessi dati ogni volta.

## <a name="reset-the-blade"></a>Reimpostare il pannello
Se si modifica un pannello ma poi si vuole tornare a quello salvato in origine, fare clic su Reimposta.

![Nei pulsanti nella parte superiore di Esplora metriche](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Flusso metriche attive

Per una visualizzazione molto più immediata dei dati di telemetria, aprire [flusso live](app-insights-live-stream.md). La visualizzazione della maggior parte delle metriche richiede alcuni minuti, a causa del processo di aggregazione. Al contrario, le metriche attive sono ottimizzate per bassa latenza. 

## <a name="set-alerts"></a>Impostazione di avvisi
Per ricevere tramite posta elettronica una notifica relativa a valori insoliti di una metrica, aggiungere un avviso. È possibile scegliere di inviare il messaggio di posta elettronica agli amministratori di account o a indirizzi di posta elettronica specifici.

![In Esplora metriche scegliere Regole di avviso, Aggiungi avviso](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Altre informazioni sugli avvisi][alerts].


## <a name="continuous-export"></a>Esportazione continua
Se si vuole che i dati vengano esportati in modo continuo per poterli elaborare esternamente, considerare la possibilità di usare l' [esportazione continua](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Per visualizzazione dei dati ancora più avanzate, è possibile [esportare in Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analytics
[Analytics](app-insights-analytics.md) è un modo più versatile per analizzare i dati di telemetria usando un linguaggio di query avanzato. Usare l'opzione per combinare o calcolare i risultati delle metriche oppure per eseguire un'analisi approfondita delle prestazioni recenti dell'applicazione. 

Da un grafico di metriche è possibile fare clic sull'icona di Analytics per passare direttamente alla query di Analytics equivalente.

## <a name="troubleshooting"></a>Risoluzione dei problemi
*All'interno del grafico non vengono visualizzati dati.*

* I filtri si applicano a tutti i grafici del pannello. Assicurarsi che, mentre ci si concentra su un grafico, non sia stato impostato un filtro che escluda tutti i dati di un altro grafico.

    Se si desidera impostare filtri diversi nei vari grafici, creare grafici in diversi pannelli e salvarli come Preferiti separati. Se si desidera, è possibile impostarli sul dashboard in modo da visualizzarli uno accanto all'altro.
* Se si raggruppa un grafico per una proprietà non definita sulla metrica, il grafico sarà vuoto. Provare a lasciare il campo "Raggruppa in base a" vuoto o scegliere una proprietà di raggruppamento diversa.
* I dati sulle prestazioni (CPU, velocità di IO e così via) sono disponibili per servizi Web Java, app desktop di Windows, [app Web IIS se si installa Status Monitor](app-insights-monitor-performance-live-website-now.md) e [servizi cloud di Azure](app-insights-azure.md). I dati non sono disponibili per i siti Web di Azure.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](app-insights-web-track-usage.md)
* [Uso di Ricerca diagnostica](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
