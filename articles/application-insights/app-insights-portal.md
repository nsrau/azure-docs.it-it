<properties
	pageTitle="Utilizzo del portale Application Insights"
	description="Panoramica dell'analisi dell'uso con Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Utilizzo del portale Application Insights

Dopo aver [impostato Application Insights nel progetto](app-insights-overview.md), i dati di telemetria sull'utilizzo e le prestazioni dell'applicazione verranno visualizzati nella risorsa di Application Insights del progetto nel [portale Azure](https://portal.azure.com).

## Trovare la telemetria in Azure

Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Application Insights creata per la propria applicazione.

![Fare clic su Sfoglia, selezionare Application Insights, quindi fare clic sull'app.](./media/app-insights-portal/00-start.png)

La pagina della panoramica mostra alcuni dati di telemetria di base e include collegamenti ad altre informazioni. Il contenuto varia a seconda del tipo di app e può essere personalizzato.

## Pannello Panoramica dell'app

Il pannello (pagina) Panoramica dell'app mostra i grafici più importanti per il monitoraggio di prestazioni e utilizzo. Il contenuto varia a seconda del tipo di app e può essere personalizzato.


### Personalizzare il pannello Panoramica 

Scegliere gli elementi da visualizzare nella panoramica. In Personalizza è possibile inserire titoli di sezione, spostare riquadri e grafici, rimuovere elementi e aggiungere nuovi riquadri e grafici dalla raccolta.

![Fare clic su "..." e quindi su Personalizza. Spostare i grafici e i riquadri. Aggiungere i riquadri dalla raccolta.](./media/app-insights-portal/020-customize.png)


## Creare grafici e griglie personalizzati per le metriche

### Modifica di grafici e griglie

Per aggiungere un nuovo grafico al pannello:

![In Esplora metriche scegliere Aggiungi grafico](./media/app-insights-metrics-explorer/04-add.png)

Selezionare un grafico nuovo o esistente per modificare il contenuto visualizzato:

![Selezionare una o più metriche](./media/app-insights-metrics-explorer/08-select.png)

È possibile visualizzare più metriche in un grafico, anche se sono presenti restrizioni sulle combinazioni che è possibile visualizzare insieme. Non appena si sceglie una metrica, alcune vengono disabilitate.

Se si codificano [metriche personalizzate](app-insights-api-custom-events-metrics.md#track-metric) nell'app (chiamate a TrackMetric e TrackEvent), verranno elencate qui.

### Segmentare i dati

Selezionare un grafico o una griglia, passare a un raggruppamento e scegliere una proprietà per eseguire il raggruppamento in base a:

![Selezionare Raggruppamento e poi selezionare una proprietà in Raggruppa per](./media/app-insights-metrics-explorer/15-segment.png)

Se nell'app sono state codificate metriche personalizzate che includono [valori delle proprietà](app-insights-api-custom-events-metrics.md#properties), sarà possibile selezionare la proprietà nell'elenco.

Il grafico è troppo piccolo per dati segmentati? Modificarne l'altezza:

![Regolare il dispositivo di scorrimento](./media/app-insights-metrics-explorer/18-height.png)

### Filtrare i dati

Per visualizzare solo le metriche per un set di valori di proprietà selezionati:

![Fare clic su Filtro, espandere una proprietà e selezionare alcuni valori](./media/app-insights-metrics-explorer/19-filter.png)

Se non si seleziona alcun valore per una determinata proprietà, è come se si selezionassero tutti: non sono presenti filtri su quella proprietà.

Si noti il numero di eventi vicino a ogni valore della proprietà. Quando si selezionano i valori di una proprietà, i conteggi vicino ad altri valori di proprietà vengono modificati.

### Salvare il pannello delle metriche

Dopo aver creato alcuni grafici, è possibile salvarli come preferiti. È possibile scegliere se condividerlo con altri membri del team, se si usa un account aziendale.

![Scegliere Preferito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Per visualizzare nuovamente il pannello, **andare al pannello** e aprire Preferiti:

![Nel pannello Panoramica scegliere Preferiti](./media/app-insights-metrics-explorer/22-favorite-get.png)

Se si è scelto l'intervallo di tempo Relativo al momento del salvataggio, il pannello verrà aggiornato con le metriche più recenti. Se si è scelto l'intervallo di tempo Assoluto, verranno visualizzati gli stessi dati ogni volta.

### Reimpostare il pannello

Se si modifica un pannello ma poi si desidera tornare a quello impostato salvato in origine, fare clic su Reimposta.

![Nei pulsanti nella parte superiore di Esplora metriche](./media/app-insights-metrics-explorer/17-reset.png)

## Creazione di una pagina di ricerca

Aprire la ricerca diagnostica:

![Aprire la ricerca diagnostica](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Aprire il pannello Filtro e scegliere i tipi di evento che si vuole visualizzare. Se, in seguito, si vogliono ripristinare i filtri con cui è stato aperto il pannello, fare clic su Reimposta.

![Scegliere il filtro e selezionare i tipi di telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)

### Filtrare in base ai valori delle proprietà

È possibile filtrare gli eventi in base ai valori delle relative proprietà. Le proprietà disponibili dipendono dai tipi di eventi selezionati.

Ad esempio, selezionare le richieste con un codice di risposta specifico.

![Espandere una proprietà e scegliere un valore](./media/app-insights-diagnostic-search/03-response500.png)

La mancata scelta dei valori di una determinata proprietà ha lo stesso effetto della scelta di tutti i valori. Viene disattivata l'applicazione dei filtri per quella proprietà.


### Limitare la ricerca

Si noti che il numero a destra dei valori di filtro mostra quante occorrenze sono incluse nel set filtrato corrente.

In questo esempio è evidente che la richiesta `Reports/Employees` produce la maggior parte dei 500 errori:

![Espandere una proprietà e scegliere un valore](./media/app-insights-diagnostic-search/04-failingReq.png)

Se si vuole vedere anche quali altri eventi si sono verificati durante questo periodo, è possibile controllare **Includi eventi con proprietà non definite**.

### Salvare la ricerca

Dopo aver impostato tutti i filtri desiderati, è possibile salvare la ricerca come preferita. Se si usa un account aziendale, è possibile scegliere se condividerlo con altri membri del team.

![Fare clic su Preferiti, impostare il nome e fare clic su Salva](./media/app-insights-diagnostic-search/08-favorite-save.png)


Per visualizzare nuovamente la ricerca, **andare al pannello Panoramica** e aprire Preferiti:

![Sezione Preferiti](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se è stato salvato con intervallo di tempo Relativo, il pannello riaperto presenterà i dati più recenti. Se è stato salvato con intervallo di tempo Assoluto,verranno visualizzati gli stessi dati ogni volta.

<!---HONumber=Oct15_HO3-->