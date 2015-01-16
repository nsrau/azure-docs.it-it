<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="Monitorare gli eventi di utilizzo e le metriche nell'app Web con Application Insights" description="Inserire alcune righe di codice per scoprire le attività svolte dagli utenti con il sito Web." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# Monitorare gli eventi di utilizzo e le metriche personalizzati nell'app Web

*Application Insights è disponibile in anteprima.*

Inserire delle righe di codice nell'applicazione Web per scoprire come viene usata dagli utenti. È possibile monitorare eventi, metriche e visualizzazioni pagine. Sono visualizzati i grafici e le tabelle di dati, aggregati per tutti gli utenti. 

> [AZURE.NOTE] Attualmente non è disponibile l'esperienza utente completa. È possibile inviare gli eventi e le metriche personalizzati ad Application Insights e cercare i dati di telemetria non elaborati in [Ricerca diagnostica][diagnostic]. Tuttavia, non è ancora possibile visualizzare i grafici statistici del digest, disponibili prossimamente.

<!-- Sample pic -->

* [Monitoraggio client e server](#clientServer)
* [Prima di iniziare](#prep)
* [Monitorare le metriche](#metrics)
* [Monitorare gli eventi](#events)
* [Monitorare le visualizzazioni pagine](#pageViews)
* [Filtrare, cercare e segmentare i dati con le proprietà](#properties)
* [Combinare metriche ed eventi](#measurements)
* [Impostare i valori predefiniti delle proprietà](#defaults)
* [Definire più contesti](#contexts)
* [Attivare e disattivare la telemetria](#disable)
* [Passaggi successivi](#next)



## <a name="clientServer"></a> Monitoraggio client e server

È possibile inviare i dati di telemetria dal lato client (pagina Web) o server dell'app o da entrambi.

Le API client e server sono molto simili. È possibile inviare gli stessi tipi di telemetria dai Web browser degli utenti e dal server Web. La differenza consiste nell'ambito dei dati che è possibile inviare.

* Il monitoraggio nel client Web è particolarmente utile se si hanno pagine Web attive ed elaborate con numerosi elementi JavaScript. Ad esempio, è possibile monitorare la frequenza con cui gli utenti fanno clic su un determinato pulsante o quanto spesso si verificano errori di convalida.
* Il monitoraggio nel server Web è più utile per il monitoraggio delle metriche e degli eventi aziendali, ad esempio il valore del carrello di un cliente o il numero di ordini abbandonati.

In un'applicazione Web ASP.NET tipica si ha la chiamata JavaScript predefinita a trackPageView() nella pagina master Web ed è possibile aggiungere altre chiamate per monitorare gli eventi e le metriche nel codice server. Se il codice sul lato client è sufficientemente elaborato è anche possibile aggiungere alcune chiamate per monitorare gli eventi e le metriche nel client.


## <a name="prep"></a>Prima di iniziare

Completare queste operazioni, se non è stato ancora fatto:

* Per ottenere i dati di telemetria da un'app Web ASP.NET:
    [Aggiungere Application Insights al progetto][greenbrown]
    Nel codice del server Web includere:
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Configurare l'analisi di utilizzo Web][usage]. Il codice di inizializzazione JavaScript deve essere incluso in tutte le pagine Web in cui si vuole scrivere il codice di monitoraggio oppure in una pagina master. 
    Se funziona, i dati vengono visualizzati nel pannello Panoramica in Analisi dell'utilizzo.

Quando si esegue l'app nel computer di sviluppo in modalità di debug, i risultati vengono visualizzati in pochi secondi in Application Insights. Quando si distribuisce l'app, è richiesto più tempo per spostare i dati nella pipeline dal server e dai client.

<!--
## <a name="metrics"></a> Monitorare le metriche

Non sono richieste altre operazioni per ottenere i dati di utilizzo di base, ad esempio le visualizzazioni pagine. Tuttavia, è possibile scrivere alcune righe di codice per scoprire in che modo l'app viene usata dagli utenti.

Ad esempio, se l'app è un gioco, si potrebbe voler sapere il punteggio medio raggiunto dagli utenti per capire se il grado di difficoltà aumenta o diminuisce in una versione successiva.

Per monitorare una metrica, ossia un valore numerico come un punteggio, inserire una riga di script come la seguente in una posizione appropriata nell'app:

JavaScript nel client

    appInsights.trackMetric("Alerts", notifications.Count);

C# nel server

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB nel server

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Testare l'app e usarla per eseguire la chiamata trackMetric().


Quindi, andare nell'applicazione in Application Insights e fare clic nel riquadro [Metriche][metrics]. Selezionare la metrica per visualizzare i primi risultati.


Il grafico mostra la media recente dei valori registrati da tutti gli utenti. 


(A tal proposito: le metriche non sono ottimizzate per la diagnosi dei problemi. Per informazioni su questo argomento, vedere [Registrazione diagnostica][diagnostic]). -->


## <a name="events"></a>Monitorare gli eventi

Gli eventi indicano la frequenza di un'occorrenza, con una media basata su tutti gli utenti. Ad esempio, si vuole sapere la frequenza con cui gli utenti completano il gioco. Nel codice che termina il gioco, inserire una riga come la seguente:

JavaScript nel client

    appInsights.trackEvent("EndOfGame");

C# nel server
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB nel server


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Se si inviano i dati di telemetria dal client e dal server, assicurarsi che gli eventi abbiano nomi diversi.


## <a name="pageViews"></a>Visualizzazioni pagine (solo client)

Per impostazione predefinita, lo script di inizializzazione nell'intestazione della pagina Web registra una visualizzazione pagina, assegnando all'evento il nome con l'URL relativo della pagina. Queste chiamate forniscono le statistiche di utilizzo della pagina di base. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### Dati pagina personalizzati

Se si vuole, è possibile modificare la chiamata per cambiare il nome o inserire chiamate aggiuntive. Ad esempio, se l'app Web con una singola pagina visualizza più schede, è possibile registrare una visualizzazione pagina quando l'utente passa a una scheda diversa. Ad esempio:

JavaScript nel client:

    appInsights.trackPageView("tab1");

Se si hanno più schede in pagine HTML diverse, è possibile specificare anche l'URL:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Filtrare, cercare e segmentare i dati con le proprietà

È possibile allegare le proprietà e le misure a eventi, visualizzazioni pagine e altri dati di telemetria. 

Le **proprietà** sono valori di stringa che è possibile usare per filtrare i dati di telemetria nei report di utilizzo. Ad esempio, se l'app comprende più giochi, è possibile allegare il nome del gioco a ogni evento per vedere quali sono i giochi più diffusi.

Le **misure** sono valori numerici da cui è possibile estrarre delle statistiche nei report di utilizzo.


JavaScript nel client

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# nel server

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB nel server

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Allegare le proprietà alle visualizzazioni pagine nello stesso modo:

JavaScript nel client

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Assicurarsi di non registrare informazioni personali identificabili nelle proprietà.


## Visualizzazioni pagine ed eventi con durata

È possibile allegare dati relativi alla durata agli eventi e alle visualizzazioni pagine. Al posto delle chiamate a trackEvent o trackPageView, usare queste chiamate:

JavaScript nel client

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Usare la stessa stringa come primo parametro nelle chiamate di avvio e di arresto.

## <a name="defaults"></a>Impostare i valori predefiniti delle proprietà (non nel client Web)

È possibile impostare i valori predefiniti in TelemetryContext. Questi valori vengono allegati a tutte le metriche e gli eventi inviati dal contesto. 
    

C# nel server

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB nel server

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
I singoli dati di telemetria possono sostituire i valori predefiniti.

Per passare tra i gruppi di valori predefiniti delle proprietà, impostare più contesti.



## <a name="next"></a>Passaggi successivi


[Cercare eventi e log][diagnostic]

[Risoluzione dei problemi][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




