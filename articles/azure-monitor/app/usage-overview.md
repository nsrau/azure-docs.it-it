---
title: Analisi dell'utilizzo con Azure Application Insights | Documentazione Microsoft
description: Informazioni sugli utenti e le operazioni eseguite con l'app.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: f2539d5250ff436a720fe10f748f40db29b0ee25
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118524"
---
# <a name="usage-analysis-with-application-insights"></a>Uso dell'analisi con Application Insights

Quali sono le funzionalità dell'app Web o dell'app per dispositivi mobili più comuni? Gli utenti raggiungono i propri obiettivi con l'app? Escono in particolari punti e riaccedono in un secondo momento?  [Application Insights di Azure](../../azure-monitor/app/app-insights-overview.md) consente di ottenere importanti informazioni approfondite sull'uso dell'app da parte degli utenti. Ogni volta che si aggiorna l'app, è possibile valutarne il funzionamento per gli utenti. Con queste informazioni è possibile prendere decisioni in base ai dati sui cicli di sviluppo successivi.

## <a name="send-telemetry-from-your-app"></a>Inviare dati di telemetria dall'app

La migliore esperienza viene ottenuta tramite l'installazione di Application Insights nel codice server dell'app e nelle pagine Web. I componenti client e server dell'app inviano dati di telemetria al portale di Azure per l'analisi.

1. **Codice server:** installare il modulo appropriato per l'app [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) o per [altre](../../azure-monitor/app/platforms.md) app.

    * *Non si vuole installare il codice server? [Creare una risorsa di Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Codice della pagina Web:** aggiungere lo script seguente nella pagina Web prima del tag ``</head>`` di chiusura. Sostituire la chiave di strumentazione con il valore appropriato della risorsa di Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Per altre informazioni sulle configurazioni più avanzate per il monitoraggio di siti Web, vedere le [informazioni di riferimento sull'API di JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Codice app per dispositivi mobili:** usare App Center SDK per raccogliere gli eventi dall'app, quindi inviare copie di questi eventi ad Application Insights per l'analisi [seguendo le istruzioni in questa guida](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Ottenere i dati di telemetria:** eseguire il progetto in modalità di debug per alcuni minuti e quindi cercare i risultati nel pannello Panoramica in Application Insights.

    Pubblicare l'app per monitorare le prestazioni dell'app ed esaminare le operazioni eseguite dagli utenti con l'app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Includere l'ID di utente e sessione nei dati di telemetria
Per tenere traccia degli utenti nel tempo, Application Insights richiede un modo per identificarli. Lo strumento Eventi è l'unico strumento relativo all'uso per cui non è richiesto un ID utente o un ID di sessione.

Iniziare a inviare gli ID utente e sessione tramite [questa procedura](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Esplorare le statistiche e i dati demografici di uso
Scoprire quando le persone usano l'app, a quali pagine sono più interessati, dove si trovano, quali browser e sistemi operativi usano. 

I report sugli utenti e le sessioni filtrano i dati in base alle pagine o agli eventi personalizzati e li segmentano in base a proprietà quali posizione, ambiente e pagina. È anche possibile aggiungere filtri personalizzati.

![Utenti](./media/usage-overview/users.png)  

Informazioni approfondite sui modelli di segnalazione corretti nel set di dati.  

* Il report sugli **utenti** conta il numero di utenti univoci che accedono alle pagine nei periodi di tempo scelti. Per le app Web gli utenti vengono conteggiati usando i cookie. Se un utente accede al sito con browser o computer client diversi o cancella i cookie, verrà conteggiato più volte.
* Il report sulle **sessioni** conteggia il numero di sessioni dell'utente che accedono al sito. Una sessione è un periodo di attività dell'utente, che termina quando si verifica un periodo di inattività di più di mezz'ora.

[Altre informazioni sugli strumenti di Utenti, Sessioni ed Eventi](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Conservazione: numero di utenti che ritornano

La conservazione consente di comprendere la frequenza con cui gli utenti tornano a usare l'app, in base alle coorti di utenti che hanno eseguito un'azione aziendale in un determinato intervallo di tempo. 

- Comprendere le funzionalità specifiche per cui alcuni utenti ritornano di più rispetto ad altri 
- Fare ipotesi in base a dati utente reali 
- Determinare se la conservazione è un problema per il prodotto 

![Conservazione](./media/usage-overview/retention.png) 

I controlli di conservazione nella parte superiore consentono di definire l'intervallo di tempo e gli eventi specifici per il calcolo della conservazione. Il grafico nella parte centrale offre una rappresentazione visiva della percentuale della conservazione generale in base all'intervallo di tempo specificato. Il grafico nella parte inferiore rappresenta la singola conservazione in un determinato periodo di tempo. Questo livello di dettagli consente di capire le operazioni eseguite dagli utenti e le possibili motivazioni per cui un utente sceglie di ritornare con una granularità più dettagliata.  

[Altre informazioni sullo strumento di conservazione](usage-retention.md)

## <a name="custom-business-events"></a>Eventi aziendali personalizzati

Per ottenere una descrizione chiara delle operazioni che gli utenti eseguono con l'app, è utile inserire righe di codice per registrare eventi personalizzati. Questi eventi possono tenere traccia di qualsiasi operazione, da azioni dell'utente dettagliate, ad esempio fare clic su pulsanti specifici, agli eventi aziendali più importanti, ad esempio effettuare un acquisto o vincere a un gioco. 

Sebbene in alcuni casi, le visualizzazioni della pagina possano rappresentare eventi utili, questo non è vero in generale. Un utente può aprire la pagina di un prodotto senza acquistarlo. 

Con eventi aziendali specifici, è possibile rappresentare in un grafico lo stato di avanzamento degli utenti all'interno del sito. È possibile scoprirne le preferenze per le diverse opzioni e i punti in cui abbandonano il sito o hanno difficoltà. Con queste informazioni si possano prendere decisioni consapevoli sulle priorità nel backlog di sviluppo.

Gli eventi possono essere registrati dal lato client dell'app:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

O dal lato server:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

È possibile collegare i valori delle proprietà a questi eventi, in modo da poter filtrare o suddividere gli eventi quando li si ispeziona nel portale. A ogni evento viene anche associato un set standard di proprietà, ad esempio un'ID utente anonimo, che consente di analizzare la sequenza delle attività di un singolo utente.

Altre informazioni sugli [eventi personalizzati](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) e le [proprietà](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Analisi approfondita degli eventi

Negli strumenti Utenti, Sessioni ed Eventi, è possibile eseguire un'analisi approfondita degli eventi personalizzati per utente, nome dell'evento e proprietà.
![Utenti](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Progettare i dati di telemetria con l'app

Quando si progettano le funzionalità dell'app, prendere in considerazione le modalità in cui si desidera misurarne il successo con gli utenti. Decidere quali eventi aziendali è necessario registrare e codificare il monitoraggio delle chiamate per questi eventi nell'app sin dall'inizio.

## <a name="a--b-testing"></a>Test A | B
Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare valori per le proprietà differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A questo scopo, definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria inviato dall'applicazione, non solo ai messaggi personalizzati, ma anche ai dati di telemetria standard.

Nel portale Application Insights è possibile filtrare e dividere i dati sui valori delle proprietà, in modo da confrontare versioni diverse.

A tale scopo, [configurare un inizializzatore di telemetria](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Nell'inizializzatore dell'app Web, ad esempio Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Tutti i nuovi TelemetryClients aggiungono automaticamente il valore di proprietà specificato. I singoli eventi di telemetria possono eseguire la sostituzione dei valori predefiniti.

## <a name="next-steps"></a>Passaggi successivi
   - [Utenti, sessioni ed eventi](usage-segmentation.md)
   - [Grafici a imbuto](usage-funnels.md)
   - [Conservazione](usage-retention.md)
   - [Flussi degli utenti](usage-flows.md)
   - [Cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md)
   - [Aggiungere il contesto utente](usage-send-user-context.md)
