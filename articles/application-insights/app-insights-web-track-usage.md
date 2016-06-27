<properties 
	pageTitle="Analisi dell'utilizzo per applicazioni Web con Application Insights" 
	description="Panoramica dell'analisi dell'utilizzo per app Web con Application Insights" 
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
	ms.date="06/12/2016" 
	ms.author="awills"/>
 
# Analisi dell'utilizzo per applicazioni Web con Application Insights

Sapere in che modo viene usata l'applicazione consente di incentrare il lavoro di sviluppo sugli scenari più rilevanti per gli utenti e ottenere informazioni sugli obiettivi più facili o più difficili da raggiungere per gli utenti.

Visual Studio Application Insights offre due livelli di monitoraggio dell’utilizzo:

* **Dati relativi a utenti, sessioni e visualizzazioni di pagina**: predefiniti.  
* **Telemetria personalizzata**: è possibile [scrivere codice ][api] consente di monitorare gli utenti attraverso l'esperienza utente dell'app. 

## Configurazione

Aprire una risorsa Application Insights nel [portale di Azure](https://portal.azure.com), fare clic sul grafico dei caricamenti di pagina del Browser vuoto e seguire le istruzioni di installazione.

[Altre informazioni](app-insights-javascript.md)


## Verifica della popolarità dell'applicazione

Accedere al [portale di Azure][portal], passare alla risorsa dell'applicazione e fare clic su Utilizzo:

![](./media/app-insights-web-track-usage/14-usage.png)

* **Utenti:** numero di utenti attivi distinti nell'intervallo di tempo del grafico. 
* **Sessioni:** conteggio delle sessioni attive.
* **Visualizzazioni pagina**: conta il numero di chiamate a trackPageView(), in genere chiamato una volta in ogni pagina Web.

Fare clic su uno qualsiasi dei grafici per visualizzare altri dettagli. Si noti che è possibile modificare l'intervallo di tempo dei grafici.

### Ubicazione degli utenti

Nel pannello Utilizzo fare clic sul grafico Utenti per visualizzare maggiori dettagli:

![Nel pannello Utilizzo fare clic sul grafico Utenti.](./media/app-insights-web-track-usage/02-sessions.png)
 
### Browser o sistemi operativi usati dagli utenti

È possibile raggruppare (segmentare) i dati in base a una proprietà, ad esempio browser, sistema operativo o città:

![Selezionare un grafico che mostra una sola metrica, attivare il Raggruppamento e scegliere una proprietà.](./media/app-insights-web-track-usage/03-browsers.png)


## Sessioni

In Application Insights sessione è un concetto fondamentale che associa tutti gli eventi di telemetria, ad esempio richieste, visualizzazioni di pagina, eccezioni o eventi personalizzati codificati manualmente, a una specifica sessione utente.

Vengono raccolte informazioni di contesto complete su ogni sessione, ad esempio caratteristiche del dispositivo, posizione geografica, sistema operativo e così via.

Se si esegue la strumentazione del client e del server ([ASP.NET][greenbrown] o [J2EE][java]), gli SDK propagano l'id sessione tra client e server, in modo che gli eventi su entrambi i lati possano essere correlati.

Durante la [diagnosi dei problemi][diagnostic] è possibile individuare tutti i dati di telemetria relativi alla sessione in cui si è verificato un problema, incluse tutte le richieste ed eventuali eventi, eccezioni o tracce registrati.

Le sessioni forniscono indicazioni sulla popolarità di contesti, ad esempio dispositivo, sistema operativo o area geografica. Visualizzando il numero di sessioni raggruppate per dispositivo, ad esempio, si ottiene un conteggio più preciso della frequenza con cui un dispositivo viene usato con l’app rispetto alle visualizzazioni di pagina. Questa è un'informazione importante che consente di valutare gli eventuali problemi specifici di un dispositivo.


#### Informazioni sulla sessione

Una sessione rappresenta un singolo contatto tra l'utente e l'applicazione. Nella sua forma più semplice, la sessione inizia quando un utente avvia l'app e termina quando l'utente esce dall'app. Per le app Web, per impostazione predefinita la sessione termina dopo 30 minuti di inattività o dopo 24 ore di attività.

È possibile cambiare queste impostazioni predefinite modificando il frammento di codice seguente:

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs`: tempo, in millisecondi, per la scadenza della sessione a causa di inattività dell'utente. Impostazione predefinita: 30 minuti.
* `sessionExpirationMs`: lunghezza massima della sessione, in millisecondi. Se l'utente resta attivo dopo questo periodo di tempo, viene conteggiata un'altra sessione. Valore predefinito: 24 ore.

**Durata della sessione** è una [metrica][metrics] che registra l'intervallo di tempo tra il primo e l’ultimo elemento di telemetria della sessione. Non include il periodo di timeout.

Il **Conteggio delle sessioni** in un determinato intervallo viene definito come il numero di sessioni univoche che presentano alcune attività durante questo intervallo. Quando si esamina un lungo intervallo di tempo, ad esempio il conteggio di sessioni giornaliere della settimana precedente, in genere questo valore è equivalente al numero totale di sessioni.

Tuttavia, quando si analizzano intervalli di tempo più brevi, ad esempio il livello di dettaglio orario, una sessione lunga che si estende in più ore viene conteggiata per ogni ora in cui la sessione è stata attiva.

## Utenti e conteggi utenti


Ogni sessione utente è associata a un ID utente univoco.

Per impostazione predefinita, l'utente viene identificato mediante l'inserimento di un cookie. Un utente che usa più browser o dispositivi verrà conteggiato più di una volta. Vedere [utenti autenticati](#authenticated-users)


La metrica **Conteggio utenti** in un determinato intervallo viene definita come il numero di utenti univoci con attività registrate durante questo intervallo. Di conseguenza, è possibile che gli utenti associati a sessioni lunghe vengano conteggiati più volte se si imposta un intervallo di tempo in modo che la granularità sia inferiore a un'ora.

La metrica **Nuovi utenti** conteggia gli utenti le cui prime sessioni con l’app si sono verificate durante questo intervallo. Se viene usato il metodo predefinito di conteggio per utenti mediante cookie, vengono inclusi anche gli utenti che hanno cancellato i relativi cookie o che usano un nuovo dispositivo o browser per accedere all'app per la prima volta. ![Nel pannello Utilizzo fare clic sul grafico Utenti per esaminare i nuovi utenti.](./media/app-insights-web-track-usage/031-dual.png)

### Utenti autenticati

Se l'app Web consente agli utenti di accedere, è possibile ottenere un conteggio più preciso fornendo ad Application Insights un ID utente univoco. Questo valore non deve necessariamente essere il nome o lo stesso ID usato nell'app. Non appena l'app ha identificato l'utente, usare questo codice:


*JavaScript nel client*

      appInsights.setAuthenticatedUserContext(userId);

Se l'app raggruppa gli utenti in account, è anche possibile passare un identificatore per l'account.

      appInsights.setAuthenticatedUserContext(userId, accountId);

Gli ID utente e account non devono contenere spazi o i caratteri `,;=|`.


In [Esplora metriche](app-insights-metrics-explorer.md) è possibile creare un grafico di **utenti autenticati ** e **account**.

## Traffico sintetico

Il traffico sintetico include le richieste provenienti dai test di carico e disponibilità, dai crawler del motore di ricerca e da altri agenti.

Application Insights cerca di determinare e classificare automaticamente il traffico sintetico e di contrassegnarlo in modo appropriato. Nella maggior parte dei casi, il traffico sintetico non richiama JavaScript SDK, quindi questa attività è esclusa dal conteggio di utenti e sessioni.

Tuttavia, per i [test web][availability] di Application Insights, l'ID utente viene impostato automaticamente in base alla posizione POP e l'ID sessione viene impostato in base all'ID di esecuzione del test. Per impostazione predefinita, nei report predefiniti il traffico sintetico viene escluso tramite filtro. Ciò comporta quindi l'esclusione di questi utenti e sessioni. Quando il traffico sintetico è incluso, tuttavia, potrebbe provocare un lieve incremento nei conteggi complessivi di utenti e sessioni.
 
## Utilizzo delle pagine

Fare clic sul grafico di visualizzazioni pagina per ottenere una versione con dimensioni ingrandite insieme a una suddivisione delle pagine più popolari:


![Nel pannello Panoramica fare clic sul grafico Visualizzazioni pagina.](./media/app-insights-web-track-usage/05-games.png)
 
L'esempio precedente è tratto da un sito Web di giochi. Da quest'ultimo è immediatamente evidente quanto segue:

* L'utilizzo non è migliorato nell'ultima settimana. Potrebbe essere utile ottimizzare il motore di ricerca.
* Le pagine di giochi sono visualizzate da molte meno persone rispetto alla home page. Occorre capire perché la home page non invoglia gli utenti a giocare.
* "Crossword" è il gioco più popolare. È consigliabile dare la priorità a nuove idee e a miglioramenti in questo ambito.

## Rilevamento personalizzato

Si supponga che, invece di implementare ogni gioco in una pagina Web separata, si decida di eseguire il refactoring di tutti i giochi nella stessa app a singola pagina con la maggior parte delle funzionalità codificate come Javascript nella pagina Web. Ciò consente all'utente di passare rapidamente da un gioco all'altro o di avere a disposizione diversi giochi in un'unica pagina.

Tuttavia, si vuole comunque che Application Insights registri il numero di volte in cui ogni gioco viene aperto, esattamente come se fossero in pagine Web separate. La risposta è semplice: è sufficiente inserire una chiamata al modulo di telemetria nel codice JavaScript in cui si vuole registrare l'apertura di una nuova 'pagina':

	appInsights.trackPageView(game.Name);

## Eventi personalizzati

Scrivere dati di telemetria personalizzati per registrare eventi specifici. In particolare, in un'app a pagina singola è opportuno conoscere la frequenza con cui l'utente esegue determinate azioni o raggiunge determinati obiettivi:

    appInsights.trackEvent("GameEnd");

Ad esempio, per registrare le azioni di clic su un collegamento:

    <a href="target.htm" onclick="appInsights.trackEvent('linkClick');return true;">my link</a>


## Visualizzare i conteggi degli eventi personalizzati

Aprire Esplora metriche e aggiungere un grafico per mostrare gli eventi. Segmentare in base al nome:

![Selezionare un grafico che mostra solo una metrica. Attivare il Raggruppamento. Scegliere una proprietà. Non tutte le proprietà sono disponibili.](./media/app-insights-web-track-usage/06-eventsSegment.png)



## Esaminare eventi specifici

Per una migliore comprensione dell'andamento di una sessione tipica, è possibile concentrarsi su una sessione utente specifica che contiene un particolare tipo di evento.

In questo esempio viene codificato un evento personalizzato "NoGame" che viene chiamato se l'utente si disconnette senza effettivamente iniziare un gioco. Occorre capire perché un utente si comporta in questo modo. Forse se si esaminano alcune occorrenze specifiche, si otterranno delle indicazioni.

Gli eventi personalizzati ricevuti dall'app sono elencati per nome nel pannello Panoramica:


![Nel pannello Panoramica fare clic su uno dei tipi di evento personalizzato.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
Fare clic nell'evento di interesse e quindi selezionare un'occorrenza specifica recente:


![Nell'elenco sotto il grafico di riepilogo fare clic su un evento](./media/app-insights-web-track-usage/08-searchEvents.png)
 
Esaminare i dati di telemetria relativi alla sessione in cui si è verificato l'evento NoGame specifico.


![Fare clic su 'tutti i dati di telemetria per sessione'](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
Non si sono verificate eccezioni, quindi non sono stati eventuali errori a impedire all'utente di giocare.
 
È possibile escludere tramite filtro tutti i tipi di dati di telemetria, ad eccezione delle visualizzazioni pagina per la sessione corrente:


![](./media/app-insights-web-track-usage/10-filter.png)
 
Come si può vedere ora, questo utente si è connesso semplicemente per controllare i punteggi più recenti. Potrebbe essere utile sviluppare una storia utente che semplifichi questa operazione. È anche consigliabile implementare un evento personalizzato per segnalare quando si verifica questa storia specifica.

## Filtrare, cercare e segmentare i dati con proprietà
È possibile associare tag arbitrari e valori numerici agli eventi.
 

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

Associare le proprietà alle visualizzazioni pagine nello stesso modo:

*JavaScript nel client*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Nella Ricerca diagnostica è possibile visualizzare le proprietà facendo clic su una singola occorrenza di un evento.


![Nell'elenco di eventi aprire un evento e quindi fare clic su '...' per visualizzare altre proprietà](./media/app-insights-web-track-usage/11-details.png)
 
Usare il campo Ricerca per visualizzare le occorrenze di eventi con un valore della proprietà particolare.


![Digitare un valore nel campo Ricerca](./media/app-insights-web-track-usage/12-searchEvents.png)


## Test A | B

Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare tag differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A questo scopo, definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria inviato dall'applicazione, non solo ai messaggi personalizzati, ma anche ai dati di telemetria standard.

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

I singoli dati di telemetria possono eseguire l'override dei valori predefiniti.

È possibile impostare un inizializzatore universale in modo che tutti i nuovi TelemetryClient usino automaticamente il contesto.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Nell'inizializzatore di app, ad esempio Global.asax.cs, procedere come segue:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## Compilare - Misurare - Acquisire informazioni

Quando si usa l'analisi, questa diventa parte integrante del ciclo di sviluppo, non solo un'attività da prendere in considerazione per la risoluzione dei problemi. Ecco alcuni suggerimenti:

* Determinare la metrica chiave dell'applicazione. Stabilire se si vogliono prendere in considerazione tutti gli utenti possibili o è preferibile un piccolo gruppo di utenti molto soddisfatti oppure se si vogliono ottimizzare le visite o le vendite.
* Pianificare la misurazione di ogni storia. Quando si definisce una nuova storia o funzionalità utente oppure si pianifica l'aggiornamento di una storia o una funzionalità esistente, pensare sempre a come si misurerà la riuscita della modifica. Prima di iniziare a scrivere codice, chiedersi quale effetto avrà sulle metriche, se funziona e se si dovrà tenere traccia dei nuovi eventi. Naturalmente, quando la funzionalità è live, assicurarsi di esaminare l'analisi e agire in base ai risultati. 
* Correlare altre metriche alla metrica chiave. Ad esempio, se si aggiunge una funzionalità "Preferiti", si vorrà sapere con quale frequenza gli utenti aggiungono preferiti. Tuttavia, è forse più interessante sapere con quale frequenza tornano a consultare i preferiti. E, ancora più importante, è utile sapere se i clienti che usano i preferiti in definitiva effettuano più acquisti relativi al prodotto.
* Effettuare un test. Impostare uno switch di funzionalità che consente di visualizzare una nuova funzionalità solo ad alcuni utenti. Usare Application Insights per verificare se la nuova funzionalità viene usata nel modo previsto. Apportare modifiche, quindi rilasciarla per un pubblico più ampio.
* È essenziale comunicare con gli utenti. L'analisi non è sufficiente da sola, ma è complementare a una buona relazione con i clienti.


## Riferimenti

* [Uso dell'API - Panoramica][api]
* [Informazioni di riferimento sull'API JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## Video

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0615_2016-->