---
title: Analisi dell&quot;utilizzo per applicazioni Web con Azure Application Insights | Microsoft docs
description: Panoramica dell&quot;analisi dell&quot;utilizzo con Application Insights
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Analisi dell'utilizzo per applicazioni Web con Application Insights
Sapere in che modo viene usata l'applicazione consente di incentrare il lavoro di sviluppo sugli scenari più rilevanti per gli utenti e ottenere informazioni sugli obiettivi più facili o più difficili da raggiungere.

[Azure Application Insights](app-insights-overview.md) offre due livelli di monitoraggio dell'utilizzo:

* **Dati relativi a utenti, sessioni e visualizzazioni di pagina** : predefiniti.  
* **Telemetria personalizzata**: è possibile [scrivere codice](app-insights-api-custom-events-metrics.md) per monitorare gli utenti attraverso l'esperienza utente dell'app. 


## <a name="get-started"></a>Introduzione

La migliore esperienza viene ottenuta tramite l'installazione di Application Insights nel codice server dell'app e nelle pagine Web. I componenti client e server dell'app inviano dati di telemetria al portale di Azure per l'analisi.

1. **Codice server:** installare il modulo appropriato per l'app [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) o per [altre](app-insights-platforms.md) app.

    * *Non si vuole installare il codice server? [Creare una risorsa di Azure Application Insights](app-insights-create-new-resource.md).*

2. **Codice della pagina Web:** aprire il [Portale di Azure](https://portal.azure.com), aprire la risorsa di Application Insights per l'app e quindi aprire **Introduzione > Monitoraggio e diagnosi dell'applicazione lato client**. 

    ![Copiare lo script nell'intestazione della pagina master web.](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **Ottenere dati di telemetria:** eseguire il progetto in modalità di debug per alcuni minuti e quindi cercare i risultati nel pannello Panoramica in Application Insights.

    Pubblicare l'app per monitorare le prestazioni dell'app ed esaminare le operazioni eseguite dagli utenti con l'app.

## <a name="usage-analysis-out-of-the-box"></a>Analisi dell'utilizzo predefinita
Aprire il pannello Utilizzo.

![Grafici utenti, sessioni e visualizzazioni pagine](./media/app-insights-web-track-usage/14-usage.png)

Passare il mouse nella parte vuota su un grafico per visualizzare i conteggi in un momento specifico. In caso contrario, i numeri indicano il valore aggregato, ad esempio una media, un totale o un numero di utenti definito durante il periodo.

Che cosa visualizzano i grafici?

* **Utenti:** numero di utenti attivi distinti nell'intervallo di tempo del grafico. Nelle applicazioni Web gli utenti vengono conteggiati usando i cookie. Una persona che usa diversi browser, cancella i cookie o usa la funzionalità di privacy verrà conteggiata più volte.
* Gli **utenti autenticati** vengono conteggiati se è stata inserita una chiamata a [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) nel codice.
* **Sessioni:** conteggio delle sessioni attive. Una sessione Web viene conteggiata dopo 30 minuti di inattività. 
* **Visualizzazioni pagina** : conta il numero di chiamate a trackPageView(), in genere chiamato una volta in ogni pagina Web.

Fare clic su uno qualsiasi dei grafici per visualizzare altri dettagli. Si noti che è possibile modificare l'intervallo di tempo dei grafici.

### <a name="where-do-my-users-live"></a>Ubicazione degli utenti
Fare clic su una parte vuota del grafico Utenti per aprire un pannello che visualizza altri dettagli:

![Nel pannello Utilizzo fare clic sul grafico Utenti.](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>Browser o sistemi operativi usati dagli utenti

Fare clic su **Modifica** nel grafico Utenti e raggruppare (segmentare) i dati tramite una proprietà, ad esempio Browser, Sistema operativo o Città: 

![Selezionare un grafico che mostra una sola metrica, attivare il Raggruppamento e scegliere una proprietà.](./media/app-insights-web-track-usage/03-browsers.png)

Per visualizzare l'elenco completo dei conteggi, passare al tipo di grafico **griglia**. È anche possibile scegliere di visualizzare altre metriche:

![Grafico a griglia a più colonne](./media/app-insights-web-track-usage/multi-column-grid.png)

Per i tipi di grafico, è possibile raggruppare in base a una proprietà o selezionare più metriche, ma non entrambi. Questo grafico confronta due metriche, gli utenti e [gli utenti autenticati](app-insights-api-custom-events-metrics.md#authenticated-users). 

![Selezionare un grafico, cercare e selezionare o deselezionare le metriche.](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>Visualizzazioni pagina
Nel pannello Utilizzo, fare clic nel grafico di visualizzazioni delle pagine per ottenere una versione più dettagliata con una suddivisione delle pagine più popolari:

![Nel pannello Panoramica fare clic sul grafico Visualizzazioni pagina.](./media/app-insights-web-track-usage/05-games.png)

L'esempio precedente proviene da un sito Web di giochi. Dai grafici, è possibile immediatamente notare quanto segue:

* L'utilizzo non è migliorato nell'ultima settimana. Potrebbe essere utile ottimizzare il motore di ricerca.
* La pagina Tennis è la pagina del gioco più popolare. È opportuno quindi migliorare questa pagina.
* In media, gli utenti visitano la pagina Tennis circa tre volte alla settimana. Il numero delle sessioni è tre volte superiore al numero di utenti.
* La maggior parte degli utenti visita il sito durante la settimana lavorativa negli Stati Uniti e nell'orario di lavoro. Si dovrebbe forse aggiungere un pulsante "Nascondi rapidamente" nella pagina Web.
* Le [annotazioni](app-insights-annotations.md) nel grafico indicano quando sono state distribuite le nuove versioni del sito Web. Nessuna delle distribuzioni recenti ha avuto un effetto visibile sull'utilizzo.

## <a name="custom-tracking"></a>Rilevamento personalizzato
Si supponga che, invece di implementare ogni gioco in una pagina Web separata, si decida di eseguire il refactoring di tutti i giochi nella stessa app a singola pagina con la maggior parte delle funzionalità codificate come Javascript nella pagina Web. Ciò consente all'utente di passare rapidamente da un gioco all'altro o di avere a disposizione diversi giochi in un'unica pagina.

Tuttavia, si vuole comunque che Application Insights registri il numero di volte in cui ogni gioco viene aperto, esattamente come se fossero in pagine Web separate. La risposta è semplice: è sufficiente inserire una chiamata al modulo di telemetria nel codice JavaScript in cui si vuole registrare l'apertura di una nuova 'pagina':

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
Questa chiamata simula i dati di telemetria registrati da una visualizzazione pagina.  Tuttavia, non sempre si vuole combinare i messaggi con le visualizzazioni pagina. Usare invece gli eventi personalizzati. È possibile inviarli da pagine Web o da un server Web:


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

I [dati di telemetria personalizzati inseriti nel codice server o Web](app-insights-api-custom-events-metrics.md#trackevent) possono essere usati in diversi modi per capire come viene usata l'applicazione.

Per visualizzare gli eventi inviati da TrackEvent(), aprire Esplora metriche, aggiungere un nuovo grafico e quindi modificarlo. Le metriche vengono visualizzate nella sezione Metriche personalizzate. 

![Grafico che visualizza gli eventi personalizzati.](./media/app-insights-web-track-usage/06-eventsSegment.png)

Se si impostano i [valori delle proprietà](app-insights-api-custom-events-metrics.md#properties) (denominati anche dimensioni) negli eventi, è possibile raggrupparli e filtrarli in base a tali valori.

Creare più grafici per correlare le modifiche in altri eventi e metriche. Ad esempio, nei momenti in cui vengono usati più giochi, ci si aspetterebbe anche un incremento dei giochi abbandonati. Ma se l'aumento dei giochi abbandonati è sproporzionato, è necessario verificare se il carico elevato causa problemi ritenuti inaccettabili dagli utenti.

## <a name="drill-into-specific-events"></a>Esaminare eventi specifici
Per una migliore comprensione dell'andamento di una sessione tipica, è possibile concentrarsi su una sessione utente specifica che contiene un particolare tipo di evento.

In una griglia di eventi, fare clic sull'evento di interesse e quindi selezionare un'occorrenza specifica recente:

![Nell'elenco sotto il grafico di riepilogo fare clic su un evento](./media/app-insights-web-track-usage/08-searchEvents.png)

Esaminare i dati di telemetria relativi alla sessione in cui si è verificato l'evento NoGame specifico.

![Fare clic su 'tutti i dati di telemetria per sessione'](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

Non si sono verificate eccezioni, quindi non sono stati eventuali errori a impedire all'utente di giocare.

È possibile escludere tramite filtro tutti i tipi di dati di telemetria, ad eccezione delle visualizzazioni pagina per la sessione corrente:

![](./media/app-insights-web-track-usage/10-filter.png)

Come si può vedere ora, questo utente si è connesso semplicemente per controllare i punteggi più recenti. Potrebbe essere utile sviluppare una storia utente che semplifichi questa operazione. È anche consigliabile implementare un evento personalizzato per segnalare quando si verifica questa storia specifica.

## <a name="filter-search-and-segment-your-data-with-properties"></a>Filtrare, cercare e segmentare i dati con proprietà
È possibile associare tag arbitrari e valori numerici agli eventi.

*JavaScript in pagine Web*

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

*JavaScript in pagine Web*

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

## <a name="edit-and-create-queries-over-your-telemetry"></a>Modificare e creare query sui dati di telemetria

Fare clic sull'icona Azure Analytics in un grafico qualsiasi per aprire una query equivalente da modificare.
Scorrere l'elenco per vedere se ci sono più query generate. Posizionare il cursore su una query qualsiasi e fare clic su **Vai**. 

In alternativa, aprire Analytics dall'icona nel pannello Panoramica e scrivere le query o provare alcune query di esempio incluse nella scheda home di Analytics.


![Finestra di Analytics con query generata](./media/app-insights-web-track-usage/open-analytics.png)

[Informazioni sul linguaggio query di Azure Analytics](app-insights-analytics.md).

Per l'analisi dell'utilizzo, le tabelle seguenti sono particolarmente interessanti:

* `customEvents`: risultati delle chiamate [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).
* `pageViews`: contatori di pagine aperte nei browser client o chiamate a [trackPageView()](app-insights-api-custom-events-metrics.md#page-views).


## <a name="a--b-testing"></a>Test A | B
Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare tag differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A questo scopo, definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria inviato dall'applicazione, non solo ai messaggi personalizzati, ma anche ai dati di telemetria standard.

Nel portale Application Insights sarà quindi possibile filtrare e raggruppare (segmentare) i dati sui tag, in modo da confrontare versioni diverse.


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


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


## <a name="build---measure---learn"></a>Compilare - Misurare - Acquisire informazioni
Quando si usa l'analisi, questa diventa parte integrante del ciclo di sviluppo, non solo un'attività da prendere in considerazione per la risoluzione dei problemi. Di seguito sono riportati alcuni suggerimenti:

* Determinare la metrica chiave dell'applicazione. Stabilire se si vogliono prendere in considerazione tutti gli utenti possibili o è preferibile un piccolo gruppo di utenti molto soddisfatti oppure se si vogliono ottimizzare le visite o le vendite.
* Pianificare la misurazione di ogni storia. Quando si definisce una nuova storia o funzionalità utente oppure si pianifica l'aggiornamento di una storia o una funzionalità esistente, pensare sempre a come si misurerà la riuscita della modifica. Prima di iniziare a scrivere codice, chiedersi quale effetto avrà sulle metriche, se funziona e se si dovrà tenere traccia dei nuovi eventi.
  Naturalmente, quando la funzionalità è live, assicurarsi di esaminare l'analisi e agire in base ai risultati.
* Correlare altre metriche alla metrica chiave. Ad esempio, se si aggiunge una funzionalità 'Preferiti', si vuole sapere con quale frequenza gli utenti aggiungono i preferiti. Tuttavia, forse è più interessante sapere con quale frequenza tornano a consultare i preferiti. E, ancora più importante, è utile sapere se i clienti che usano i preferiti in definitiva effettuano più acquisti relativi al prodotto.
* Effettuare un test. Impostare uno switch di funzionalità che consente di visualizzare una nuova funzionalità solo ad alcuni utenti. Usare Application Insights per verificare se la nuova funzionalità viene usata nel modo previsto. Apportare modifiche, quindi rilasciarla per un pubblico più ampio.
* È essenziale comunicare con gli utenti. L'analisi non è sufficiente da sola, ma è complementare per mantenere una buona relazione con i clienti.

## <a name="learn-more"></a>Altre informazioni
* [Rilevare, valutare e diagnosticare gli arresti anomali e i problemi di prestazione nell'applicazione](app-insights-detect-triage-diagnose.md)
* [Iniziare a utilizzare Application Insights su molte piattaforme](app-insights-detect-triage-diagnose.md)
* [Uso dell'API - Panoramica](app-insights-api-custom-events-metrics.md)
* [Informazioni di riferimento sull'API JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



