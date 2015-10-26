<properties 
	pageTitle="Analisi dell'utilizzo per applicazioni Web con Application Insights" 
	description="Panoramica dell'analisi dell'uso con Application Insights" 
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
	ms.date="08/24/2015" 
	ms.author="awills"/>
 
# Analisi dell'utilizzo per applicazioni Web con Application Insights

La conoscenza della modalità d'uso dell'applicazione consente di concentrare l'attenzione sugli scenari più importanti e ottenere informazioni sugli obiettivi più facili o più difficili da raggiungere per gli utenti.

Visual Studio Application Insights offre due livelli di monitoraggio dell’utilizzo:

* **Dati relativi a utenti, sessioni e visualizzazioni di pagina**: predefiniti.  
* **Telemetria personalizzata**: [scrittura di codice ][api] per monitorare gli utenti attraverso l'esperienza utente dell'app. 

## Configurazione

I dati di utilizzo di un'applicazione web vengono forniti dal browser del client.

#### Configurare una risorsa Application Insights 

Una risorsa Application Insights è un'area in Microsoft Azure in cui vengono analizzati e visualizzati i dati di telemetria dell'app. Una risorsa potrebbe essere stata già configurata per visualizzare i dati del lato server dell'app in [ASP.NET][greenbrown] o [J2EE][java]. In caso contrario, è possibile eseguire questa operazione ora.

In genere è consigliabile visualizzare i dati di utilizzo del client web nella stessa risorsa dei dati del server. In questo modo, è possibile correlare facilmente diagnostica e metrica dalle due estremità. Pertanto, se si dispone già di una risorsa, andare al passaggio successivo.

#### Inserire il codice nelle pagine web

Nella risorsa nel [portale di Azure][portal], aprire Avvio rapido per ottenere il frammento di codice per monitorare le pagine web.

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

Inserire il codice \_Layout.cshtml in una pagina master, ad esempio (in .NET), o in un file di inclusione per assicurarsi che il codice sia incluso in tutte le pagine.

Il frammento di codice include la chiave di strumentazione (iKey) che identifica la risorsa. Per inviare dati a una risorsa diversa, ad esempio durante i test, è sufficiente sostituire la iKey.

Pubblicare le pagine web o usarle in modalità debug, per generare alcuni dati di telemetria.


## Verifica della popolarità dell'applicazione

Accedere al [portale di Azure][portal], passare alla risorsa di applicazione e fare clic su Utilizzo:

![](./media/app-insights-web-track-usage/14-usage.png)

* **Utenti:** numero di utenti attivi distinti nell'intervallo di tempo del grafico. 
* **Sessioni:** conteggio delle sessioni attive.
* **Visualizzazioni pagina**: conta il numero di chiamate a trackPageView(), in genere chiamate una volta in ogni pagina Web.

Fare clic su uno qualsiasi dei grafici per visualizzare altri dettagli. Si noti che è possibile modificare l'intervallo di tempo dei grafici.

### Ubicazione degli utenti

Nel pannello Utilizzo fare clic sul grafico utenti per visualizzare maggiori dettagli:

![Nel pannello Utilizzo fare clic sul grafico utenti.](./media/app-insights-web-track-usage/02-sessions.png)
 
### Browser o sistemi operativi usati dagli utenti

Raggruppare (segmentare) i dati in base a una proprietà come browser, sistema operativo o città:

![Selezionare un grafico che mostra una sola metrica, passare su Raggruppamento e scegliere una proprietà.](./media/app-insights-web-track-usage/03-browsers.png)


## Sessioni

Sessione è un concetto fondamentale in Application Insights, che consiste nell’associare tutti gli eventi di telemetria, quali richieste, visualizzazioni di pagine, eccezioni o eventi personalizzati codificati manualmente con una specifica sessione utente.

Vengono raccolte informazioni di contesto complete su ciascuna sessione, ad esempio caratteristiche del dispositivo, posizione geografica, sistema operativo e così via.

Se si esegue la strumentazione del client e del server ([ASP.NET][greenbrown] o [J2EE][java]), gli SDK propagano l'id sessione tra client e server, in modo che gli eventi su entrambi i lati possano essere correlati.

Durante la [diagnosi dei problemi][diagnostic], è possibile individuare tutti i dati di telemetria relativi alla sessione in cui si verificato un problema, incluse tutte le richieste ed eventuali eventi, eccezioni o tracce registrati.

Le sessioni forniscono un misura adeguata della popolarità dei contesti, ad esempio dispositivo, sistema operativo o percorso. Visualizzando il numero di sessioni raggruppate per dispositivo, ad esempio, si ottiene un conteggio più preciso della frequenza con cui un dispositivo viene usato con l’app che contando le visualizzazioni di pagina. Tale input potrebbe essere utile per la valutazione di eventuali problemi specifici di un dispositivo.


#### Che cos'è una sessione?

Una sessione rappresenta un singolo incontro tra l'utente e l'applicazione. Nella sua forma più semplice, la sessione inizia quando un utente avvia l'app e termina quando l'utente esce dall'app. Per le app Web, per impostazione predefinita, la sessione termina dopo 30 minuti di inattività o dopo 24 ore di inattività.

È possibile modificare queste impostazioni predefinite modificando il frammento di codice:

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs`: il tempo, in millisecondi, per la scadenza della sessione a causa di inattività dell'utente. Impostazione predefinita: 30 minuti.
* `sessionExpirationMs`: la lunghezza massima della sessione, in millisecondi. Se l'utente resta attivo dopo questo periodo di tempo, viene conteggiata un'altra sessione. Valore predefinito: 24 ore.

**Durata della sessione** è una [metrica][metrics] che registra l'intervallo di tempo tra il primo e l’ultimo elemento di telemetria della sessione. (Non include il periodo di timeout).

**Conteggio sessioni** in un determinato intervallo viene definito come il numero di sessioni univoche con alcune attività durante questo intervallo. Quando si esamina un lungo intervallo di tempo, ad esempio il conteggio sessioni giornaliere della settimana precedente, in genere questo valore è equivalente al numero totale di sessioni.

Tuttavia, quando si analizzano intervalli di tempo più brevi, ad esempio il livello di dettaglio orario, una sessione lunga che si estende in più ore viene conteggiata per ciascuna ora in cui la sessione è stata attiva.

## Utenti e conteggi utenti


Ciascuna sessione utente è associata a un id utente univoco.

Per impostazione predefinita, l'utente viene identificato inserendo un cookie. Un utente che usa più browser o dispositivi viene conteggiato più di una volta. Vedere [utenti autenticati](#authenticated-users).


La metrica **conteggio utenti** in un determinato intervallo viene definita come il numero di utenti univoci con attività registrate durante questo intervallo. Di conseguenza, quando si imposta un intervallo di tempo, gli utenti con sessioni lunghe potrebbero essere conteggiati più volte e il livello di dettaglio essere inferiore a un'ora o circa.

**Nuovi utenti**: conteggia gli utenti le cui prime sessioni con l’app si sono verificate durante questo intervallo. Se viene usato il metodo predefinito di conteggio per utenti mediante cookie, vengono inclusi anche gli utenti che hanno cancellato i relativi cookie o che usano un nuovo dispositivo o browser per accedere all'app per la prima volta. ![Nel pannello Utilizzo fare clic sul grafico utenti per esaminare i nuovi utenti.](./media/app-insights-web-track-usage/031-dual.png)

### Utenti autenticati

Se l'app Web consente agli utenti di accedere, è possibile ottenere un conteggio più preciso specificando Application Insights con un ID utente univoco. Non deve necessariamente essere il nome o lo stesso ID usato nell'app. Non appena l'app ha identificato l'utente, usare questo codice:


*JavaScript nel client*

      appInsights.setAuthenticatedUserContext(userId);

Se l'app raggruppa gli utenti in account, è inoltre possibile passare un identificatore per l'account.

      appInsights.setAuthenticatedUserContext(userId, accountId);

Gli ID utente e account non devono contenere spazi o caratteri `,;=|`.


In [Esplora metriche](app-insights-metrics-explorer.md) è possibile creare un grafico di **utenti autenticati ** e **account**.

## Traffico sintetico

Traffico sintetico include le richieste di test di carico e disponibilità, crawler del motore di ricerca e altri agenti.

Application Insights cerca di determinare e classificare automaticamente il traffico sintetico e di contrassegnarlo in modo appropriato. Nella maggior parte dei casi, il traffico sintetico non richiama l’SDK JavaScript, per cui questa attività è esclusa dal conteggio di utenti e sessioni.

Tuttavia, per i [test web][availability] di Application Insights, l'id utente viene impostato automaticamente in base alla posizione POP e l’id sessione viene impostato in base all'id di esecuzione del test. Nei report predefiniti, il traffico sintetico viene filtrato per impostazione predefinita, per cui vengono esclusi utenti e sessioni. Tuttavia, quando è il traffico sintetico incluso, potrebbe verificarsi un piccolo aumento nei conteggi complessivi di utenti e sessioni.
 
## Uso delle pagine

Fare clic sul grafico di visualizzazioni pagina per ottenere una versione con dimensioni ingrandite insieme a una suddivisione delle pagine più diffuse:


![Nel pannello Panoramica fare clic sul grafico delle visualizzazioni Pagina](./media/app-insights-web-track-usage/05-games.png)
 
L'esempio precedente è tratto da un sito Web di giochi. Da quest'ultimo è immediatamente evidente quanto segue:

* L'uso non è stato migliorato nell'ultima settimana. Forse è opportuno definire l'ottimizzazione del motore di ricerca.
* Le pagine di giochi sono visualizzate da molte meno persone rispetto alla Home page. Perché la Home page non invoglia gli utenti a giocare?
* 'Crossword' è il gioco più diffuso. È opportuno dare la priorità a nuove idee e ai miglioramenti disponibili.

## Rilevamento personalizzato

Si supponga che, invece di implementare ogni gioco in una pagina Web separata, si decida di eseguire il refactoring di tutti i giochi nella stessa app a singola pagina con la maggior parte delle funzionalità codificate come Javascript nella pagina Web. Ciò consente all'utente di passare rapidamente da un gioco all'altro o di avere a disposizione diversi giochi in un'unica pagina.

Tuttavia, si vuole comunque che Application Insights registri il numero di volte in cui ogni gioco viene aperto, esattamente come se fossero in pagine Web separate. La risposta è semplice: inserire solo una chiamata nel modulo di telemetria in JavaScript in cui si vuole registrare l'apertura di una nuova 'pagina':

	appInsights.trackPageView(game.Name);

## Eventi personalizzati

Usare gli eventi personalizzati per . È possibile inviarli da app per dispositivi, pagine Web o un server Web:

*JavaScript*

    appInsights.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


Gli eventi personalizzati più frequenti sono elencati nel pannello Panoramica.

![Nel pannello Panoramica scorrere verso il basso e fare clic su Eventi personalizzati.](./media/app-insights-web-track-usage/04-events.png)

Fare clic sull'intestazione della tabella per visualizzare il numero totale di eventi. È possibile segmentare il grafico in base a vari attributi, ad esempio il nome dell'evento:

![Selezionare un grafico che mostra solo una metrica. Passare a Raggruppamento. Scegliere una proprietà. Non tutte le proprietà sono disponibili.](./media/app-insights-web-track-usage/06-eventsSegment.png)

La funzionalità particolarmente utile delle sequenze temporali è che è possibile correlare le modifiche con altre metriche ed eventi. Ad esempio, in alcuni casi, quando vengono riprodotti più giochi, è possibile aspettarsi anche un aumento dei giochi abbandonati. Ma l'aumento dei giochi abbandonati è sproporzionato, per cui si intende sapere se il carico elevato causa problemi ritenuti inaccettabili dagli utenti.

## Esaminare eventi specifici

Per una migliore comprensione dell'andamento di una sessione tipica, è possibile concentrarsi su una sessione utente specifica che contiene un particolare tipo di evento.

In questo esempio viene codificato un evento personalizzato "NoGame" che viene chiamato se l'utente si disconnette senza effettivamente iniziare un gioco. Perché un utente si comporta in questo modo? Forse se si esaminano alcune occorrenze specifiche, si otterranno delle indicazioni.

Gli eventi personalizzati ricevuti dall'app sono elencati per nome nel pannello Panoramica:


![Nel pannello Panoramica fare clic su uno dei tipi di evento personalizzato.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
Fare clic nell'evento di interesse e quindi selezionare un'occorrenza specifica recente:


![Nell'elenco sotto il grafico di riepilogo, fare clic su un evento](./media/app-insights-web-track-usage/08-searchEvents.png)
 
Verranno ora esaminati i dati di telemetria relativi alla sessione in cui si è verificato l'evento NoGame specifico.


![Fare clic su 'tutti i dati di telemetria per sessione'](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
Non si sono verificate eccezioni, per cui non sono stati eventuali errori a impedire all'utente di giocare.
 
È possibile filtrare tutti i tipi di dati di telemetria, ad eccezione delle visualizzazioni pagina per la sessione corrente:


![](./media/app-insights-web-track-usage/10-filter.png)
 
A questo punto è possibile vedere che questo utente esegue l'accesso semplicemente per controllare i punteggi più recenti. Forse si deve valutare l'opportunità di sviluppare la storia di un utente per facilitare la comprensione. Si deve implementare un evento personalizzato per segnalare quando si verifica questa storia specifica.

## Filtrare, cercare e segmentare i dati con le proprietà
È possibile allegare tag arbitrari e valori numerici agli eventi.
 

*JavaScript nel client*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# nel server*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB nel server*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Allegare le proprietà alle visualizzazioni pagine nello stesso modo:

*JavaScript nel client*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Nella Ricerca diagnostica è possibile visualizzare le proprietà facendo clic su una singola occorrenza di un evento.


![Nell'elenco di eventi aprire un evento e quindi fare clic su '...' per visualizzare ulteriori proprietà](./media/app-insights-web-track-usage/11-details.png)
 
Usare il campo Ricerca per visualizzare le occorrenze di eventi con un valore della proprietà particolare.


![Digitare un valore nel campo Ricerca](./media/app-insights-web-track-usage/12-searchEvents.png)


## Test A | B

Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare tag differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A tale scopo definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria che l'applicazione invia, non solo ai messaggi personalizzati, ma anche alla telemetria standard.

Nel portale Application Insights sarà quindi possibile filtrare e raggruppare (segmentare) i dati sui tag, in modo da confrontare versioni diverse.

*C# nel server*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*VB nel server*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

I singoli dati di telemetria possono sostituire i valori predefiniti.

È possibile impostare un inizializzatore universale in modo che tutti i nuovi TelemetryClient usino automaticamente il contesto.

```C#

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

Nell'inizializzatore di app, ad esempio Global.asax.cs, procedere come segue:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## Compilare - Misurare - Acquisire informazioni

Quando si usa l'analisi, questa diventa parte integrante del ciclo di sviluppo, non solo un'attività da prendere in considerazione per la risoluzione dei problemi. Di seguito sono riportati alcuni suggerimenti:

* Determinare la metrica chiave dell'applicazione. Si vogliono prendere in considerazione tutti gli utenti possibili o è preferibile un piccolo gruppo di utenti molto soddisfatti? Si vogliono ottimizzare le visite o le vendite?
* Pianificare la misurazione di ogni storia. Quando si rappresenta una nuova storia o funzionalità utente oppure si pianifica di aggiornarne una esistente, pensare sempre a come si misurerà la riuscita della modifica. Prima di avviare il codice, chiedersi quale effetto avrà sulle metriche, se funziona e se si dovrà tenere traccia dei nuovi eventi. Naturalmente, quando la funzionalità è disponibile, assicurarsi di esaminare l'analisi e agire sui risultati. 
* Correlare altre metriche alla metrica chiave. Ad esempio, se si aggiunge una funzionalità "Preferiti", si vuole sapere con quale frequenza gli utenti aggiungono i preferiti. Tuttavia, forse è più interessante sapere con quale frequenza tornano a consultare i preferiti. E, ancora più importante, i clienti, che usano i preferiti, in definitiva fanno più acquisti relativi al prodotto?
* Test Canary. Impostare uno switch di funzionalità che consenta di visualizzare una nuova funzionalità solo ad alcuni utenti. Usare Application Insights per verificare se la nuova funzionalità viene usata nel modo previsto. Apportare modifiche, quindi rilasciarla per un pubblico più ampio.
* Comunicare con gli utenti L'analisi non è sufficiente da sola, ma è complementare per mantenere una buona relazione con i clienti.


## Riferimenti

* [Uso dell'API - Panoramica][api]
* [Informazioni di riferimento all'API JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## Video

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO3-->