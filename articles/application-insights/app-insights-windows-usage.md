<properties 
	pageTitle="Monitorare l'uso nelle app Windows Store e Phone con Application Insights" 
	description="Analizzare l'uso dell'app per dispositivo Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>

#  Monitorare l'uso nelle app Windows Store e Windows Phone con Application Insights

*Application Insights è disponibile in anteprima.*

Informazioni sul numero di utenti a disposizione e su quali pagine gli utenti stanno guardando nell'app. Application Insights offre dati predefiniti. Inserendo alcune righe di codice nell'app, sarà possibile avere altre informazioni sui percorsi usati dagli utenti nell'app e sugli obiettivi raggiunti mediante l'applicazione.

Se questa operazione non è stata ancora eseguita, aggiungere [Application Insights al progetto dell'app][windows] e ripubblicarlo.


## <a name="usage"></a>Tenere traccia dell'utilizzo

Dalla sequenza temporale Panoramica, fare clic sui grafici relativi agli utenti e alle sessioni per visualizzare altre analisi dettagliate.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* Viene tenuta traccia degli **utenti** in modo anonimo, in modo che venga considerato due volte lo stesso utente in dispositivi diversi.
* Il conteggio di una **sessione** viene eseguito quando l'app viene sospesa (per più di un breve intervallo, per evitare il conteggio di sospensioni accidentali).

#### Segmentazione

Segmentare un grafico per ottenere una suddivisione in base a una serie di criteri. Ad esempio, per visualizzare il numero di utenti che usa ogni versione dell'applicazione, aprire il grafico degli utenti e suddividere in segmenti in base alla versione dell'applicazione:

![Nel grafico degli utenti, passare a Segmentazione e scegliere Versione dell'applicazione](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### Visualizzazioni pagina

Per individuare i percorsi che usati dagli utenti nell'app, inserire la [telemetria delle visualizzazioni pagina][api] nel codice:

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Nel grafico delle visualizzazioni pagina e aprendo i dettagli, vedere i risultati:

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

Fare clic su qualsiasi pagina per visualizzare i dettagli di occorrenze specifiche.

#### Eventi personalizzati

Mediante l'inserimento di codice per l'invio di eventi personalizzati dall'app, è possibile rilevare il comportamento degli utenti e l'uso delle funzionalità e scenari,

ad esempio:

    telemetry.TrackEvent("GameOver");

i dati verranno visualizzati nella griglia Eventi personalizzati. È possibile vedere una visualizzazione aggregata in Esplora metriche o fare clic su qualsiasi evento per visualizzare occorrenze specifiche.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


È possibile aggiungere proprietà numeriche e di stringa a qualsiasi evento.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Fare clic per visualizzare tutte le occorrenze per visualizzare le proprietà dettagliate, incluse quelle definite.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

Per altre informazioni sugli eventi personalizzati, vedere [Riferimento API][api].

## Sessioni

Sessione è un concetto fondamentale in Application Insights, che consiste nell’associare tutti gli eventi di telemetria, quali arresti anomali o eventi personalizzati codificati manualmente con una specifica sessione utente.

Vengono raccolte informazioni di contesto complete su ciascuna sessione, ad esempio caratteristiche del dispositivo, posizione geografica, sistema operativo e così via.

Durante la [diagnosi dei problemi][diagnostic], è possibile individuare tutti i dati di telemetria relativi alla sessione in cui si verificato un problema, incluse tutte le richieste ed eventuali eventi, eccezioni o tracce registrati.

Le sessioni forniscono un misura adeguata della popolarità dei contesti, ad esempio dispositivo, sistema operativo o percorso. Visualizzando il numero di sessioni raggruppate per dispositivo, ad esempio, si ottiene un conteggio più preciso della frequenza con cui un dispositivo viene usato con l’app che contando le visualizzazioni di pagina. Tale input potrebbe essere utile per la valutazione di eventuali problemi specifici di un dispositivo.


#### Che cos'è una sessione?

Una sessione rappresenta un singolo incontro tra l'utente e l'applicazione. Nella sua forma più semplice, la sessione inizia quando un utente avvia l'app e termina quando l'utente esce dall'app. Per le app per dispositivi mobili, la sessione viene terminata quando l'app viene sospesa (spostata in background) per più di 20 secondi. Se l'app viene ripresa, viene avviata una nuova sessione. Naturalmente, un utente può avere più sessioni in un giorno o anche in una singola ora.

**Durata della sessione** è una metrica che rappresenta l'intervallo di tempo tra il primo e l’ultimo elemento di telemetria della sessione. (Non include il periodo di timeout).


**Conteggio sessioni** in un determinato intervallo viene definito come il numero di sessioni univoche con alcune attività durante questo intervallo. Quando si esamina un lungo intervallo di tempo, ad esempio il conteggio sessioni giornaliere della settimana precedente, in genere questo valore è equivalente al numero totale di sessioni.

Tuttavia, quando si analizzano intervalli di tempo più brevi, ad esempio il livello di dettaglio orario, una sessione lunga che si estende in più ore viene conteggiata per ciascuna ora in cui la sessione è stata attiva.

## Utenti e conteggi utenti

Ogni sessione utente è associata a un id utente univoco, generato durante l'uso dell'app e mantenuto nella memoria locale del dispositivo. Un utente che usa più dispositivi viene conteggiato più di una volta.

La metrica **conteggio utenti** in un determinato intervallo viene definita come il numero di utenti univoci con attività registrate durante questo intervallo. Di conseguenza, quando si imposta un intervallo di tempo, gli utenti con sessioni lunghe potrebbero essere conteggiati più volte e il livello di dettaglio essere inferiore a un'ora o circa.

**Nuovi utenti**: conteggia gli utenti le cui prime sessioni con l’app si sono verificate durante questo intervallo.


## <a name="debug"></a>Confronto tra la modalità Debug e Release

#### Debug

Se si compila in modalità di debug, gli eventi vengono inviati non appena vengono generati. Se si perde la connessione a Internet e quindi si chiude l'app prima di recuperare la connettività, la telemetria offline viene rimossa.

#### Release

Se si esegue la compilazione nella configurazione di rilascio, gli eventi vengono archiviati nel dispositivo e inviati alla ripresa dell'applicazione. I dati vengono inviati anche al primo uso dell'applicazione. Se dopo l'avvio la connettività Internet non è disponibile, la telemetria precedente, nonché quella per il ciclo di vita corrente, viene archiviata e inviata alla ripresa successiva.

## <a name="next"></a>Passaggi successivi

[Conoscere gli utenti][knowUsers]

[Altre informazioni su Esplora metriche][metrics]


[Risoluzione dei problemi][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=July15_HO4-->