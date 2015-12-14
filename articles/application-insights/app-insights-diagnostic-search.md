<properties 
	pageTitle="Utilizzo della ricerca diagnostica" 
	description="Cercare e filtrare singoli eventi, richieste e traccie dei log." 
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
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# Uso di Ricerca diagnostica in Application Insights

Ricerca di diagnostica è il pannello in [Application Insights][start] che consente di trovare ed esplorare elementi singoli di telemetria, ad esempio visualizzazioni pagina, eccezioni o richieste Web. È possibile visualizzare le tracce del log e gli eventi codificati.

## Quando viene visualizzata Ricerca diagnostica?

È possibile aprire la ricerca diagnostica in modo esplicito:

![Aprire la ricerca diagnostica](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


Viene anche aperta quando si fa clic su alcuni grafici ed elementi della griglia. In questo caso, i filtri sono preimpostati per concentrarsi sul tipo di elemento selezionato.

Ad esempio, se l'applicazione è un servizio Web, il pannello Panoramica mostra un grafico del volume di richieste. Fare clic e si otterrà un grafico più dettagliato, con un elenco che mostra il numero di richieste effettuato per ogni URL. Fare clic su qualsiasi riga per ottenere un elenco delle singole richieste per quell'URL:

![Aprire la ricerca diagnostica](./media/app-insights-diagnostic-search/07-open-from-filters.png)


Il corpo principale della Ricerca diagnostica è un elenco di elementi di telemetria: richieste del server, visualizzazioni pagina, eventi personalizzati che sono stati codificati e così via. Nella parte superiore dell'elenco è disponibile un grafico di riepilogo che mostra il numero di eventi nel tempo.

Gli eventi vengono in genere visualizzati nella ricerca diagnostica prima che vengano visualizzati nell’esploratore di metriche. Anche se il pannello viene automaticamente aggiornato a intervalli, è possibile fare clic su Aggiorna se in attesa di un determinato evento.


> [AZURE.NOTE]Se l’app genera molti dati di telemetria (e si utilizza l’SDK ASP.NET versione 2.0.0-beta3 o successive), il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Tuttavia, gli eventi che fanno parte della stessa richiesta verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. [Informazioni sul campionamento](app-insights-sampling.md).


## Controllare i singoli elementi

Selezionare qualsiasi elemento di dati di telemetria per visualizzare i campi chiave e gli elementi correlati. Se si intende visualizzare il set completo di campi, fare clic su "...".

![Aprire la ricerca diagnostica](./media/app-insights-diagnostic-search/10-detail.png)

Per trovare il set completo di campi, usare stringhe normali (senza caratteri jolly). I campi disponibili dipendono dal tipo di dati di telemetria.

## I tipi di eventi sono i seguenti:

Aprire il pannello Filtro e scegliere i tipi di evento che si vuole visualizzare. Se, in seguito, si vogliono ripristinare i filtri con cui è stato aperto il pannello, fare clic su Reimposta.


![Scegliere il filtro e selezionare i tipi di telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)


I tipi di eventi sono i seguenti:

* **Traccia**: log di diagnostica con chiamate TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Richiesta**: richieste HTTP ricevute dall'applicazione server, tra cui pagine, script, immagini, file di stile e dati. Questi eventi vengono usati per creare grafici di panoramica di richieste e risposte.
* **Visualizzazione pagina**: i dati di telemetria inviati al client Web, usati per creare report di visualizzazioni pagine. 
* **Evento personalizzato**: se sono state inserite chiamate in TrackEvent() per [tenere traccia dell'utilizzo][track], è possibile cercarle qui.
* **Eccezione**: eccezioni non rilevate nel server e quelle che si registrano con TrackException().

## Filtrare in base ai valori delle proprietà

È possibile filtrare gli eventi in base ai valori delle relative proprietà. Le proprietà disponibili dipendono dai tipi di eventi selezionati.

Ad esempio, selezionare le richieste con un codice di risposta specifico.

![Espandere una proprietà e scegliere un valore](./media/app-insights-diagnostic-search/03-response500.png)

La mancata scelta dei valori di una determinata proprietà ha lo stesso effetto della scelta di tutti i valori. Viene disattivata l'applicazione dei filtri per quella proprietà.


### Limitare la ricerca

Si noti che il numero a destra dei valori di filtro mostra quante occorrenze sono incluse nel set filtrato corrente.

In questo esempio è evidente che la richiesta `Reports/Employees` produce la maggior parte dei 500 errori:

![Espandere una proprietà e scegliere un valore](./media/app-insights-diagnostic-search/04-failingReq.png)

Se si vuole vedere anche quali altri eventi si sono verificati durante questo periodo, è possibile controllare **Includi eventi con proprietà non definite**.

## Rimuovere il traffico di bot e test Web

Usare il filtro **Traffico reale o sintetico** e selezionare **Reale**.

È possibile anche filtrare in base a **Origine del traffico sintetico**.

## Esaminare le singole occorrenze

Aggiungere il nome della richiesta al filtro impostato e quindi è possibile esaminare le singole occorrenze dell'evento.

![Selezionare un valore](./media/app-insights-diagnostic-search/05-reqDetails.png)

Per gli eventi di richiesta i dettagli mostrano le eccezioni che si sono verificate durante l'elaborazione della richiesta.

Fare clic su un'eccezione per visualizzare i relativi dettagli, inclusa l'analisi dello stack.

![Fare clic su un'eccezione](./media/app-insights-diagnostic-search/06-callStack.png)

## Trovare gli eventi con la stessa proprietà

Trovare tutti gli elementi con lo stesso valore della proprietà:

![Fare clic con il pulsante destro del mouse su una proprietà](./media/app-insights-diagnostic-search/12-samevalue.png)

## Eseguire ricerca in base al valore della metrica

Ottenere il tempo di risposta di tutte le richiesta > 5s. I tempi sono rappresentati in tick: 10 000 tick = 1 ms.

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## Eseguire ricerche nei dati

È possibile cercare i termini in uno dei valori delle proprietà. Ciò è particolarmente utile se sono stati scritti [eventi personalizzati][track] con i valori della proprietà.

È possibile che si voglia impostare un intervallo di tempo, poiché le ricerche di un intervallo più breve sono più veloci.

![Aprire la ricerca diagnostica](./media/app-insights-diagnostic-search/appinsights-311search.png)

Eseguire la ricerca di termini, non di sottostringhe. I termini sono stringhe alfanumeriche che includono segni di punteggiatura quali '.' e '\_'. Ad esempio:

Termine|*non* corrisponde a|ma corrisponde a
---|---|---
ControllerHome.Info|info<br/>home|h*info<br/>home*
ÈLocale|locale<br/>è<br/>*local|isl*<br/>islocal<br/>i*l*
Nuovo ritardo|o r|nuovo<br/>ritardo<br/>n* AND r*


È possibile usare espressioni di ricerca quali le seguenti:

Query di esempio | Effetto 
---|---
lento|Individuazione di tutti gli eventi nell'intervallo di date i cui campi includono il termine "lento".
database??|Trova la corrispondenza con database01, databaseAB,...<br/>Il punto interrogativo (?) non è consentito all'inizio di un termine di ricerca.
database*|Trova la corrispondenza con database, database01, databaseNNNN<br/>L'asterisco (*) non è consentito all'inizio di un termine di ricerca.
mela AND banana|Individuazione di eventi che contengono entrambi i termini. Usare "AND" in lettere maiuscole, non "and".
mela OR banana<br/>mela banana|Individuazione degli eventi che contengono uno dei termini. Usare "OR", non "or".</br/>Forma breve.
mela NOT banana<br/>mela -banana|Individuare eventi che contengono un termine ma non l'altro.<br/>Forma breve.
me* AND banana-(uva pera)|Operatori logici e parentesi.
"Metric": 0 TO 500<br/>"Metric" : 500 TO * | Trovare gli eventi che contengono la misura denominata all'interno dell'intervallo di valori.


## Salvare la ricerca

Dopo aver impostato tutti i filtri desiderati, è possibile salvare la ricerca come preferita. Se si usa un account aziendale, è possibile scegliere se condividerlo con altri membri del team.

![Fare clic su Preferiti, impostare il nome e fare clic su Salva](./media/app-insights-diagnostic-search/08-favorite-save.png)


Per visualizzare nuovamente la ricerca, **andare al pannello Panoramica** e aprire Preferiti:

![Sezione Preferiti](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se è stato salvato con intervallo di tempo Relativo, il pannello riaperto presenterà i dati più recenti. Se è stato salvato con intervallo di tempo Assoluto,verranno visualizzati gli stessi dati ogni volta.


## Inviare altri dati di telemetria ad Application Insights

Oltre la telemetria predefinita inviata da Application Insights SDK, è possibile:

* Acquisire le tracce del log dal framework di registrazione preferito in [.NET][netlogs] o [Java][javalogs]. Ciò significa che è possibile cercare le tracce del log e metterle in correlazione con le visualizzazioni pagina, le eccezioni e altri eventi. 
* [Scrivere codice][track] per inviare eventi personalizzati, visualizzazioni pagina ed eccezioni. 

[Informazioni su come inviare i log e telemetria personalizzata ad Application Insights][trace].


## <a name="questions"></a>Domande e risposte

### <a name="limits"></a>Quanti dati vengono conservati?

Fino a 500 eventi al secondo da ciascuna applicazione. Gli eventi vengono conservati per sette giorni.

### Come è possibile visualizzare dati POST nelle richieste server?

I dati POST non vengono registrati automaticamente, ma è possibile usare [TrackTrace o chiamate di log][trace]. Inserire i dati POST nel parametro del messaggio. Non è possibile filtrare in base al messaggio allo stesso modo delle proprietà, ma il limite delle dimensioni è maggiore.

## <a name="add"></a>Passaggi successivi

* [Inviare log e dati di telemetria personalizzati ad Application Insights.][trace]
* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-spi-custom-events-metrics.md

 

<!---HONumber=AcomDC_1203_2015-->