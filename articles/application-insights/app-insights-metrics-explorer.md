<properties 
	pageTitle="Esaminare le metriche in Application Insights" 
	description="Come interpretare i grafici in Esplora metriche e come personalizzare i pannelli di Esplora metriche." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2016" 
	ms.author="awills"/>
 
# Esaminare le metriche in Application Insights

Le metriche in [Application Insights][start] sono valori e conteggi di eventi misurati, inviati nei dati di telemetria dall'applicazione. Consentono di rilevare problemi di prestazioni e osservare le tendenze nella modalità di uso dell'applicazione. Esiste una vasta gamma di metriche standard ed è anche possibile creare metriche ed eventi personalizzati.

Il conteggio delle metriche e degli eventi viene visualizzato nei grafici dei valori aggregati, ad esempio somme, medie o conteggi.

Ecco un grafico di esempio:

![Aprire il pannello Panoramica dell'applicazione nel portale di Azure](./media/app-insights-metrics-explorer/01-overview.png)

Alcuni grafici sono segmentati: l'altezza totale del grafico in qualsiasi punto è la somma delle metriche visualizzate. Per impostazione predefinita, la legenda mostra le quantità più grandi.

Le linee tratteggiate indicano il valore della metrica relativo alla settimana precedente.



## Intervallo di tempo

È possibile modificare l'intervallo di tempo coperto dai grafici o dalle griglie in tutti i pannelli.

![Aprire il pannello Panoramica dell'applicazione nel portale di Azure](./media/app-insights-metrics-explorer/03-range.png)


Se si è in attesa di alcuni dati non ancora visualizzati, fare clic su Aggiorna. I grafici si aggiornano a intervalli regolari, ma gli intervalli sono più lunghi per gli intervalli di tempo maggiori. In modalità di rilascio è possibile che ai dati sia necessario un po' di tempo per superare la pipeline di analisi in un grafico.

Per ingrandire una parte di un grafico, trascinare il puntatore sulla parte specifica e fare clic sul simbolo di lente di ingrandimento:

![Trascinare il puntatore su una parte di un grafico.](./media/app-insights-metrics-explorer/12-drag.png)



## Granularità e valori dei punti

Posizionare il mouse sul grafico per visualizzare i valori delle metriche in quel punto.

![Posizionare il mouse su un grafico](./media/app-insights-metrics-explorer/02-focus.png)

Il valore della metrica in un punto particolare viene aggregato in base all'intervallo di campionamento precedente.

L'intervallo di campionamento o "granularità" è visibile nella parte superiore del pannello.

![Intestazione di un pannello.](./media/app-insights-metrics-explorer/11-grain.png)

È possibile modificare la granularità nel pannello Intervallo di tempo:

![Intestazione di un pannello.](./media/app-insights-metrics-explorer/grain.png)

Le granularità disponibili dipendono dall'intervallo di tempo selezionato. Le granularità esplicite costituiscono alternative alla granularità "automatica" per l'intervallo di tempo.

## Esplora metriche

Fare clic su qualsiasi grafico nel pannello Panoramica per visualizzare un set più dettagliato di griglie e grafici correlati. È possibile modificare questi grafici e griglie per concentrarsi sui dettagli a cui si è interessati

oppure è possibile fare clic sul pulsante Esplora metriche nell'intestazione del pannello Panoramica.

Ad esempio, fare clic sul grafico Richieste non riuscite dell'app Web:

![Nel pannello Panoramica fare clic su un grafico](./media/app-insights-metrics-explorer/14-trix.png)


## Significato delle cifre

La legenda sul lato mostra di solito per impostazione predefinita il valore aggregato per il periodo del grafico. Se si passa il mouse sul grafico, viene visualizzato il valore in questo punto.

Ogni punto dati del grafico è una funzione di aggregazione dei valori di dati ricevuti nell'intervallo di campionamento o nella "granularità" precedente. La granularità viene visualizzata nella parte superiore del pannello e varia in base alla scala cronologica complessiva del grafico.

Le metriche possono essere aggregate in modi diversi:

 * **Somma**: esegue la somma dei valori dei punti dati ricevuti tramite l'intervallo di campionamento o il periodo del grafico.
 * **Media**: divide la somma per il numero di punti dati ricevuti tramite l'intervallo.
 * **Unica**: i conteggi vengono usati per contare gli utenti e gli account. Per tutto l'intervallo di campionamento, o per il periodo del grafico, la figura mostra il numero di utenti diversi visualizzato in quel momento.


È possibile modificare il metodo di aggregazione:

![Selezionare il grafico e quindi selezionare Aggregazione](./media/app-insights-metrics-explorer/05-aggregation.png)

Il metodo predefinito per ogni metrica viene visualizzato quando si crea un nuovo grafico:

![Deselezionare tutte le metriche per visualizzare le impostazioni predefinite](./media/app-insights-metrics-explorer/06-total.png)



## Modifica di grafici e griglie

Per aggiungere un nuovo grafico al pannello:

![In Esplora metriche scegliere Aggiungi grafico](./media/app-insights-metrics-explorer/04-add.png)

Selezionare **Modifica** su un grafico nuovo o esistente per modificare il contenuto visualizzato:

![Selezionare una o più metriche](./media/app-insights-metrics-explorer/08-select.png)

È possibile visualizzare più metriche in un grafico, anche se sono presenti restrizioni sulle combinazioni che è possibile visualizzare insieme. Non appena si sceglie una metrica, alcune vengono disabilitate.

Eventuali [metriche personalizzate][track] codificate nell'app, ad esempio chiamate a TrackMetric e TrackEvent, verranno elencate qui.

## Segmentare i dati

È possibile suddividere una metrica per la proprietà, ad esempio eseguire un confronto delle visualizzazioni di una pagina sui client con sistemi operativi differenti.

Selezionare un grafico o una griglia, attivare il raggruppamento e scegliere una proprietà in base a cui eseguire il raggruppamento:

![Attivare il raggruppamento e poi selezionare una proprietà in Raggruppa per](./media/app-insights-metrics-explorer/15-segment.png)

> [AZURE.NOTE] Quando si utilizza la funzione di raggruppamento, i grafici ad area e a barre forniscono una visualizzazione in pila, che risulta ottimale se il metodo di aggregazione selezionato è Somma. Ma se il tipo di aggregazione selezionato è Media, è consigliabile scegliere i tipi di visualizzazione a righe o a griglia.

Se eventuali [metriche personalizzate][track] codificate nell'app includono i valori delle proprietà, sarà possibile selezionare la proprietà nell'elenco.

Se il grafico è troppo piccolo per dati segmentati, modificarne l'altezza:


![Regolare il dispositivo di scorrimento](./media/app-insights-metrics-explorer/18-height.png)


## Filtrare i dati

Per visualizzare solo le metriche per un set di valori di proprietà selezionati:

![Fare clic su Filtro, espandere una proprietà e selezionare alcuni valori](./media/app-insights-metrics-explorer/19-filter.png)

Se non si seleziona alcun valore per una determinata proprietà, è come se si selezionassero tutti i valori, ovvero alla proprietà non verrà applicato alcun filtro.

Si noti il numero di eventi vicino a ogni valore della proprietà. Quando si selezionano i valori di una proprietà, i conteggi vicino ad altri valori di proprietà vengono modificati.

I filtri si applicano a tutti i grafici in un pannello. Se si desidera applicare filtri diversi a grafici differenti, creare e salvare pannelli di metriche diversi. Se si desidera, è possibile aggiungere sul dashboard grafici di pannelli diversi, in modo da visualizzarli uno accanto all'altro.


### Rimuovere il traffico di bot e test Web

Usare il filtro **Traffico reale o sintetico** e selezionare **Reale**.

È possibile anche filtrare in base a **Origine del traffico sintetico**.

### Per aggiungere proprietà all'elenco di filtri

È possibile che si voglia filtrare i dati di telemetria in base a una categoria personalizzata. Ad esempio, è possibile che si dividano gli utenti in categorie diverse e si voglia segmentare i dati in base a queste categorie.

[Creare proprietà personalizzate](app-insights-api-custom-events-metrics.md#properties). Impostazione in un [Inizializzatore di telemetria](app-insights-api-custom-events-metrics.md#telemetry-initializers) affinché venga visualizzato in tutti i dati di telemetria - compresa la telemetria standard inviata dai diversi moduli SDK.


## Modificare il tipo di grafico

Si noti che è possibile passare dalle griglie ai grafici e viceversa:

![Selezionare una griglia o un grafico e quindi scegliere un tipo di grafico](./media/app-insights-metrics-explorer/16-chart-grid.png)

## Salvare il pannello delle metriche

Dopo aver creato alcuni grafici, è possibile salvarli come preferiti. È possibile scegliere se condividerlo con altri membri del team, se si usa un account aziendale.

![Scegliere Preferito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Per visualizzare nuovamente il pannello, **andare al pannello** e aprire Preferiti:

![Nel pannello Panoramica scegliere Preferiti](./media/app-insights-metrics-explorer/22-favorite-get.png)

Se si è scelto l'intervallo di tempo Relativo al momento del salvataggio, il pannello verrà aggiornato con le metriche più recenti. Se si è scelto l'intervallo di tempo Assoluto, verranno visualizzati gli stessi dati ogni volta.

## Reimpostare il pannello

Se si modifica un pannello ma poi si vuole tornare a quello salvato in origine, fare clic su Reimposta.

![Nei pulsanti nella parte superiore di Esplora metriche](./media/app-insights-metrics-explorer/17-reset.png)

<a name="live-metrics-stream"></a>
## Flusso di metriche live: metriche istantanee per un monitoraggio dettagliato

Flusso di metriche live mostra le metriche dell’applicazione in questo esatto momento, con una latenza quasi in tempo reale di 1 secondo. Ciò è molto utile quando si sta rilasciando una nuova compilazione e si desidera assicurarsi che tutto funzioni come previsto oppure si sta indagando su un evento imprevisto in tempo reale.

![Nel pannello Panoramica fare clic su Flusso attivo](./media/app-insights-metrics-explorer/live-stream.png)

A differenza di Esplora metriche, Flusso di metriche live consente di visualizzare un set fisso di metriche. I dati vengono mantenuti solo intanto che si trovano nel grafico, poi vengono eliminati.

Flusso di metriche live è disponibile con Application Insights SDK per ASP.NET, versione 2.1.0 o successiva.

## Impostare gli avvisi

Per ricevere tramite posta elettronica una notifica relativa a valori insoliti di una metrica, aggiungere un avviso. È possibile scegliere di inviare il messaggio di posta elettronica agli amministratori di account o a indirizzi di posta elettronica specifici.

![In Esplora metriche scegliere Regole di avviso, Aggiungi avviso](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Altre informazioni sugli avvisi][alerts].

## Eseguire l'esportazione in Excel

È possibile esportare in un file di Excel i dati delle metriche visualizzati in Esplora metriche. I dati esportati includono i dati di tutti i grafici e le tabelle visualizzati nel portale.


![In Esplora metriche scegliere Regole di avviso, Aggiungi avviso](./media/app-insights-metrics-explorer/31-export.png)

I dati di ogni grafico o tabella vengono esportati in un foglio separato nel file di Excel.

Gli elementi esportati corrispondono a quelli visualizzati. È pertanto necessario modificare l'intervallo di tempo o i filtri se si vuole modificare l'intervallo di dati esportato. Per le tabelle, se è visibile il comando **Carica altro**, sarà possibile fare clic su di esso prima di fare clic su Esporta per esportare una quantità maggiore di dati.

*L'opzione Esporta attualmente funziona solo per Internet Explorer e Chrome. In futuro verrà aggiunto il supporto per altri browser.*

## Esportazione continua

Se si vuole che i dati vengano esportati in modo continuo per poterli elaborare esternamente, considerare la possibilità di usare l'[esportazione continua](app-insights-export-telemetry.md).

### Power BI

Per visualizzazione dei dati ancora più avanzate, è possibile [esportare in Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## Analytics

[Analytics](app-insights-analytics.md) è un modo più versatile per analizzare i dati di telemetria usando un linguaggio di query avanzato. Usare l'opzione se si desidera combinare o calcolare i risultati delle metriche oppure per eseguire un'analisi approfondita delle prestazioni recenti dell'applicazione. Usare Esplora metriche se si desiderano l'aggiornamento automatico, i grafici sul dashboard e gli avvisi.

## Risoluzione dei problemi

*All'interno del grafico non vengono visualizzati dati.*

* I filtri si applicano a tutti i grafici del pannello. Assicurarsi che, mentre ci si concentra su un grafico, non sia stato impostato un filtro che escluda tutti i dati di un altro grafico.

    Se si desidera impostare filtri diversi nei vari grafici, creare grafici in diversi pannelli e salvarli come Preferiti separati. Se si desidera, è possibile impostarli sul dashboard in modo da visualizzarli uno accanto all'altro.

* Se si raggruppa un grafico per una proprietà non definita sulla metrica, il grafico sarà vuoto. Provare a lasciare il campo "Raggruppa in base a" vuoto o scegliere una proprietà di raggruppamento diversa.
* I dati sulle prestazioni (CPU, velocità di IO e così via) sono disponibili per i servizi Web Java, app desktop di Windows, [app e servizi Web IIS se si installa Status Monitor](app-insights-monitor-performance-live-website-now.md) e [servizi cloud di Azure](app-insights-azure.md). I dati non sono disponibili per i siti Web di Azure.



## Passaggi successivi

* [Monitoraggio dell'utilizzo con Application Insights](app-insights-overview-usage.md)
* [Uso di Ricerca diagnostica](app-insights-diagnostic-search.md)


<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md

 

<!---HONumber=AcomDC_0713_2016-->