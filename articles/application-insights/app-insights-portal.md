<properties
	pageTitle="Utilizzo del portale Application Insights"
	description="Dopo la configurazione dell'app per l'invio della telemetria ad Application Insights, questa guida illustra come spostarsi all'interno del portale."
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
	ms.date="11/23/2015"
	ms.author="awills"/>

# Utilizzo del portale Application Insights

Dopo aver [impostato Application Insights nel progetto](app-insights-overview.md), i dati di telemetria sull'utilizzo e le prestazioni dell'applicazione verranno visualizzati nella risorsa di Application Insights del progetto nel [portale Azure](https://portal.azure.com).

## Trovare i dati di telemetria

Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Application Insights creata per la propria applicazione.

![Fare clic su Sfoglia, selezionare Application Insights, quindi fare clic sull'app.](./media/app-insights-portal/00-start.png)

La pagina della panoramica mostra alcuni dati di telemetria di base e include collegamenti ad altre informazioni. Il contenuto varia a seconda del tipo di app e può essere personalizzato.



## Intervallo di tempo

È possibile modificare l'intervallo di tempo coperto dai grafici o dalle griglie in tutti i pannelli.

![Aprire il pannello Panoramica dell'applicazione nel portale di Azure](./media/app-insights-portal/03-range.png)


Se si è in attesa di alcuni dati non ancora visualizzati, fare clic su Aggiorna. I grafici si aggiornano a intervalli, ma gli intervalli sono più lunghi per gli intervalli di tempo maggiori. In modalità di rilascio è possibile che ai dati sia necessario un po' di tempo per superare la pipeline di analisi in un grafico.

Per ingrandire una parte di un grafico, trascinare sulla parte e fare clic sul simbolo di lente di ingrandimento:

![Eseguire il trascinamento della parte di un grafico.](./media/app-insights-portal/12-drag.png)



## Granularità e valori dei punti

Posizionare il mouse sul grafico per visualizzare i valori delle metriche in quel punto.

![Posizionare il mouse su un grafico](./media/app-insights-portal/02-focus.png)

Il valore della metrica in un punto particolare è aggregato al sopra dell'intervallo di campionamento precedente.

L'intervallo di campionamento o "granularità" è visibile nella parte superiore del pannello.

![L'intestazione di un pannello.](./media/app-insights-portal/11-grain.png)

È possibile modificare la granularità nel pannello Intervallo di tempo:

![L'intestazione di un pannello.](./media/app-insights-portal/grain.png)

Le granularità disponibili dipendono dall'intervallo di tempo selezionato. Le granularità esplicite costituiscono alternative alla granularità "automatica" per l'intervallo di tempo.

## Pannello Panoramica dell'app

Il pannello (pagina) Panoramica dell'app mostra un riepilogo della metrica di diagnostica chiave dell'app e permette di accedere alle altre funzionalità del portale.

Fare clic su:

* **Qualsiasi grafico o riquadro** per visualizzare altri dettagli.
* **Diagnostica** per passare alle pagine predefinite delle altre metriche.
* **Esplora metriche** per creare pagine di metriche personalizzate.
* **Cerca** per analizzare specifiche istanze di eventi, ad esempio richieste, eccezioni o tracce di log.


![Route principali per visualizzare i dati di telemetria](./media/app-insights-portal/010-oview.png)


### Personalizzare il pannello Panoramica 

Scegliere gli elementi da visualizzare nella panoramica. In Personalizza è possibile inserire titoli di sezione, spostare riquadri e grafici, rimuovere elementi e aggiungere nuovi riquadri e grafici dalla raccolta.

![Fare clic su Edit. Spostare i grafici e i riquadri. Aggiungere i riquadri dalla raccolta. Fare quindi clic su Fine.](./media/app-insights-portal/020-customize.png)

### Personalizzare il dashboard di Azure


Il dashboard del portale di Azure è la home page visualizzata quando si accede per la prima volta al portale. Qui è possibile raggruppare i riquadri (gruppi di grafici) da più risorse.

Per aggiungere un riquadro dal pannello Panoramica di Application Insights al dashboard del portale, selezionare l'intestazione del riquadro e quindi "...".

Per un dashboard più completo, usare [Power BI](https://azure.microsoft.com/blog/application-insights-content-pack-for-power-bi/) per visualizzare la telemetria.

## Pannelli delle metriche

Quando si fa clic nel pannello Panoramica per ottenere altri dettagli, si usa Esplora metriche (anche se è presente un pannello più specifico).

Si può anche usare il pulsante Esplora metriche per creare un nuovo pannello, che è possibile modificare e quindi salvare.


![Nel pannello Panoramica fare clic su Metrica](./media/app-insights-portal/16-metrics.png)

### Modifica di grafici e griglie

Per aggiungere un nuovo grafico al pannello:

![In Esplora metriche scegliere Aggiungi grafico](./media/app-insights-portal/04-add.png)

Selezionare un grafico nuovo o esistente per modificare il contenuto visualizzato:

![Selezionare una o più metriche](./media/app-insights-portal/08-select.png)

È possibile visualizzare più metriche in un grafico, anche se sono presenti restrizioni sulle combinazioni che è possibile visualizzare insieme. Non appena si sceglie una metrica, alcune vengono disabilitate.

Se si codificano [metriche personalizzate](app-insights-api-custom-events-metrics.md#track-metric) nell'app (chiamate a TrackMetric e metriche associate a chiamate a TrackEvent), verranno elencate qui.

### Segmentare i dati

Selezionare un grafico o una griglia, passare a un raggruppamento e scegliere una proprietà per eseguire il raggruppamento in base a:

![Selezionare Raggruppamento e poi selezionare una proprietà in Raggruppa per](./media/app-insights-portal/15-segment.png)

Se si codificano [metriche personalizzate](app-insights-api-custom-events-metrics.md#properties) nell'app e si includono i valori delle proprietà, sarà possibile selezionare la proprietà nell'elenco.

Il grafico è troppo piccolo per dati segmentati? Modificarne l'altezza:

![Regolare il dispositivo di scorrimento](./media/app-insights-portal/18-height.png)

### Filtrare i dati

Per visualizzare solo le metriche per un set di valori di proprietà selezionati:

![Fare clic su Filtro, espandere una proprietà e selezionare alcuni valori](./media/app-insights-portal/19-filter.png)

Se non si seleziona alcun valore per una determinata proprietà, è come se si selezionassero tutti: non sono presenti filtri su quella proprietà.

Si noti il numero di eventi vicino a ogni valore della proprietà. Quando si selezionano i valori di una proprietà, i conteggi vicino ad altri valori di proprietà vengono modificati.

### Salvare il pannello delle metriche

Dopo aver creato alcuni grafici, è possibile salvarli come preferiti. È possibile scegliere se condividerlo con altri membri del team, se si usa un account aziendale.

![Scegliere Preferito](./media/app-insights-portal/21-favorite-save.png)

Per visualizzare nuovamente il pannello, **andare al pannello** e aprire Preferiti:

![Nel pannello Panoramica scegliere Preferiti](./media/app-insights-portal/22-favorite-get.png)

Se si è scelto l'intervallo di tempo Relativo al momento del salvataggio, il pannello verrà aggiornato con le metriche più recenti. Se si è scelto l'intervallo di tempo Assoluto, verranno visualizzati gli stessi dati ogni volta.

### Reimpostare il pannello

Se si modifica un pannello ma poi si desidera tornare a quello impostato salvato in origine, fare clic su Reimposta.

![Nei pulsanti nella parte superiore di Esplora metriche](./media/app-insights-portal/17-reset.png)

## Ricerca

La ricerca visualizza i singoli eventi, ad esempio visualizzazioni pagina, richieste, eccezioni, tracce di log ed eventi personalizzati. Non mostra le metriche aggregate o le istanze della chiamata a TrackMetric().

> [AZURE.NOTE]Se l'app genera molti dati di telemetria (e si usa ASP.NET SDK versione 2.0.0-beta3 o successiva), il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Tuttavia, gli eventi che fanno parte della stessa richiesta verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. [Informazioni sul campionamento](app-insights-sampling.md).

Aprire la ricerca diagnostica:

![Aprire la ricerca diagnostica](./media/app-insights-portal/01-open-Diagnostic.png)

Aprire il pannello Filtro e scegliere i tipi di evento che si vuole visualizzare. Se, in seguito, si vogliono ripristinare i filtri con cui è stato aperto il pannello, fare clic su Reimposta.

![Scegliere il filtro e selezionare i tipi di telemetria](./media/app-insights-portal/02-filter-req.png)

### Filtrare in base ai valori delle proprietà

È possibile filtrare gli eventi in base ai valori delle relative proprietà. Le proprietà disponibili dipendono dai tipi di eventi selezionati.

Ad esempio, selezionare le richieste con un codice di risposta specifico.

![Espandere una proprietà e scegliere un valore](./media/app-insights-portal/03-response500.png)

La mancata scelta dei valori di una determinata proprietà ha lo stesso effetto della scelta di tutti i valori. Viene disattivata l'applicazione dei filtri per quella proprietà.

> [AZURE.NOTE]Se l'app genera molti dati di telemetria, il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Gli eventi che fanno parte della stessa operazione verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. [Informazioni sul campionamento](app-insights-sampling.md).


### Limitare la ricerca

Si noti che il numero a destra dei valori di filtro mostra quante occorrenze sono incluse nel set filtrato corrente.

In questo esempio è evidente che la richiesta `Reports/Employees` produce la maggior parte dei 500 errori:

![Espandere una proprietà e scegliere un valore](./media/app-insights-portal/04-failingReq.png)

Se si vuole vedere anche quali altri eventi si sono verificati durante questo periodo, è possibile controllare **Includi eventi con proprietà non definite**.

### Salvare la ricerca

Dopo aver impostato tutti i filtri desiderati, è possibile salvare la ricerca come preferita. Se si usa un account aziendale, è possibile scegliere se condividerlo con altri membri del team.

![Fare clic su Preferiti, impostare il nome e fare clic su Salva](./media/app-insights-portal/08-favorite-save.png)


Per visualizzare nuovamente la ricerca, **andare al pannello Panoramica** e aprire Preferiti:

![Sezione Preferiti](./media/app-insights-portal/22-favorite-get.png)

Se è stato salvato con intervallo di tempo Relativo, il pannello riaperto presenterà i dati più recenti. Se è stato salvato con intervallo di tempo Assoluto,verranno visualizzati gli stessi dati ogni volta.

<!---HONumber=AcomDC_1203_2015-->