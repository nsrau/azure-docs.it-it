---
title: Analisi dell'utilizzo per applicazioni Web con Azure Application Insights | Microsoft docs
description: Informazioni sugli utenti e le operazioni eseguite con l'app Web.
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 33aebd002458d40a5a09b6fe334ad43ec578df83
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Analisi dell'utilizzo per applicazioni Web con Application Insights

Quali sono le funzionalità dell'app Web più comuni? Gli utenti raggiungono i propri obiettivi con l'app? Escono in particolari punti e riaccedono in un secondo momento?  [Application Insights di Azure](app-insights-overview.md) consente di ottenere importanti informazioni approfondite sull'uso dell'app Web da parte degli utenti. Ogni volta che si aggiorna l'app, è possibile valutarne il funzionamento per gli utenti. Con queste informazioni è possibile prendere decisioni in base ai dati sui cicli di sviluppo successivi.

## <a name="send-telemetry-from-your-app"></a>Inviare dati di telemetria dall'app

La migliore esperienza viene ottenuta tramite l'installazione di Application Insights nel codice server dell'app e nelle pagine Web. I componenti client e server dell'app inviano dati di telemetria al portale di Azure per l'analisi.

1. **Codice server:** installare il modulo appropriato per l'app [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) o per [altre](app-insights-platforms.md) app.

    * *Non si vuole installare il codice server? [Creare una risorsa di Azure Application Insights](app-insights-create-new-resource.md).*

2. **Codice della pagina Web:** aprire il [Portale di Azure](https://portal.azure.com), aprire la risorsa di Application Insights per l'app e quindi aprire **Introduzione > Monitoraggio e diagnosi dell'applicazione lato client**. 

    ![Copiare lo script nell'intestazione della pagina master web.](./media/app-insights-usage-overview/02-monitor-web-page.png)


3. **Ottenere dati di telemetria:** eseguire il progetto in modalità di debug per alcuni minuti e quindi cercare i risultati nel pannello Panoramica in Application Insights.

    Pubblicare l'app per monitorare le prestazioni dell'app ed esaminare le operazioni eseguite dagli utenti con l'app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Includere l'ID di utente e sessione nei dati di telemetria
Per tenere traccia degli utenti nel tempo, Application Insights richiede un modo per identificarli. Lo strumento Eventi è l'unico strumento relativo all'uso per cui non è richiesto un ID utente o un ID di sessione.

Per iniziare a inviare questi ID, vedere [qui](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Esplorare le statistiche e i dati demografici di uso
Scoprire quando le persone usano l'app, a quali pagine sono più interessati, dove si trovano, quali browser e sistemi operativi usano. 

I report sugli utenti e le sessioni filtrano i dati in base alle pagine o agli eventi personalizzati e li segmentano in base a proprietà quali posizione, ambiente e pagina. È anche possibile aggiungere filtri personalizzati.

![Utenti](./media/app-insights-usage-overview/users.png)  

Informazioni approfondite sui modelli di segnalazione corretti nel set di dati.  

* Il report sugli **utenti** conta il numero di utenti univoci che accedono alle pagine nei periodi di tempo scelti. Gli utenti vengono conteggiati usando i cookie. Se un utente accede al sito con browser o computer client diversi o cancella i cookie, verrà conteggiato più volte.
* Il report sulle **sessioni** conteggia il numero di sessioni dell'utente che accedono al sito. Una sessione è un periodo di attività dell'utente, che termina quando si verifica un periodo di inattività di più di mezz'ora.

[Altre informazioni sugli strumenti di Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Visualizzazioni pagina

Nel pannello Uso fare clic nel riquadro Visualizzazioni pagina per ottenere una suddivisione delle pagine più comuni:

![Nel pannello Panoramica fare clic sul grafico Visualizzazioni pagina.](./media/app-insights-usage-overview/05-games.png)

L'esempio precedente proviene da un sito Web di giochi. Dai grafici, è possibile immediatamente notare quanto segue:

* L'utilizzo non è migliorato nell'ultima settimana. Potrebbe essere utile ottimizzare il motore di ricerca.
* La pagina Tennis è la pagina del gioco più popolare. È opportuno quindi migliorare questa pagina.
* In media, gli utenti visitano la pagina Tennis circa tre volte alla settimana. Il numero delle sessioni è tre volte superiore al numero di utenti.
* La maggior parte degli utenti visita il sito durante la settimana lavorativa negli Stati Uniti e nell'orario di lavoro. Si dovrebbe forse aggiungere un pulsante "Nascondi rapidamente" nella pagina Web.
* Le [annotazioni](app-insights-annotations.md) nel grafico indicano quando sono state distribuite le nuove versioni del sito Web. Nessuna delle distribuzioni recenti ha avuto un effetto visibile sull'utilizzo.

Cosa fare se si desidera analizzare il traffico del sito in modo più dettagliato, ad esempio suddividendolo in base a una proprietà personalizzata, mentre il sito invia i dati di telemetria di visualizzazioni della pagina?

1. Nel menu delle risorse di Application Insights aprire lo strumento **Eventi**. Questo strumento consente di analizzare il numero di visualizzazioni della pagina ed eventi personalizzati inviati dall'app, in base a diverse opzioni di filtro, coorte e segmentazione.
2. Nell'elenco a discesa "Who used" (Usato da) selezionare "Any Page View" (Qualsiasi visualizzazione di pagina).
3. Nell'elenco a discesa "Split by" (Dividi per) selezionare una proprietà in base alla quale dividere i dati di telemetria di visualizzazione della pagina.

## <a name="retention---how-many-users-come-back"></a>Conservazione: numero di utenti che ritornano

La conservazione consente di comprendere la frequenza con cui gli utenti tornano a usare l'app, in base alle coorti di utenti che hanno eseguito un'azione aziendale in un determinato intervallo di tempo. 

- Comprendere le funzionalità specifiche per cui alcuni utenti ritornano di più rispetto ad altri 
- Fare ipotesi in base a dati utente reali 
- Determinare se la conservazione è un problema per il prodotto 

![Conservazione](./media/app-insights-usage-overview/retention.png) 

I controlli di conservazione nella parte superiore consentono di definire l'intervallo di tempo e gli eventi specifici per il calcolo della conservazione. Il grafico nella parte centrale offre una rappresentazione visiva della percentuale della conservazione generale in base all'intervallo di tempo specificato. Il grafico nella parte inferiore rappresenta la singola conservazione in un determinato periodo di tempo. Questo livello di dettagli consente di capire le operazioni eseguite dagli utenti e le possibili motivazioni per cui un utente sceglie di ritornare con una granularità più dettagliata.  

[Altre informazioni sullo strumento di conservazione](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Eventi aziendali personalizzati

Per ottenere una descrizione chiara delle operazioni che gli utenti eseguono con l'app Web, è utile inserire righe di codice per registrare eventi personalizzati. Questi eventi possono tenere traccia di qualsiasi operazione, da azioni dell'utente dettagliate, ad esempio fare clic su pulsanti specifici, agli eventi aziendali più importanti, ad esempio effettuare un acquisto o vincere a un gioco. 

Sebbene in alcuni casi, le visualizzazioni della pagina possano rappresentare eventi utili, questo non è vero in generale. Un utente può aprire la pagina di un prodotto senza acquistarlo. 

Con eventi aziendali specifici, è possibile rappresentare in un grafico lo stato di avanzamento degli utenti all'interno del sito. È possibile scoprirne le preferenze per le diverse opzioni e i punti in cui abbandonano il sito o hanno difficoltà. Con queste informazioni si possano prendere decisioni consapevoli sulle priorità nel backlog di sviluppo.

Gli eventi possono essere registrati nella pagina Web:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

O sul lato server dell'app Web:

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

È possibile collegare i valori delle proprietà a questi eventi, in modo da poter filtrare o suddividere gli eventi quando li si ispeziona nel portale. A ogni evento viene anche associato un set standard di proprietà, ad esempio un'ID utente anonimo, che consente di analizzare la sequenza delle attività di un singolo utente.

Altre informazioni sugli [eventi personalizzati](app-insights-api-custom-events-metrics.md#trackevent) e le [proprietà](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Analisi approfondita degli eventi

Negli strumenti Utenti, Sessioni ed Eventi, è possibile eseguire un'analisi approfondita degli eventi personalizzati per utente, nome dell'evento e proprietà.
![Utenti](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Progettare i dati di telemetria con l'app

Quando si progettano le funzionalità dell'app, prendere in considerazione le modalità in cui si desidera misurarne il successo con gli utenti. Decidere quali eventi aziendali è necessario registrare e codificare il monitoraggio delle chiamate per questi eventi nell'app sin dall'inizio.

## <a name="a--b-testing"></a>Test A | B
Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare valori per le proprietà differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A questo scopo, definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria inviato dall'applicazione, non solo ai messaggi personalizzati, ma anche ai dati di telemetria standard.

Nel portale Application Insights è possibile filtrare e dividere i dati sui valori delle proprietà, in modo da confrontare versioni diverse.

A tale scopo, [configurare un inizializzatore di telemetria](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

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

Nell'inizializzatore dell'app Web, ad esempio Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Tutti i nuovi TelemetryClients aggiungono automaticamente il valore di proprietà specificato. I singoli eventi di telemetria possono eseguire la sostituzione dei valori predefiniti.

## <a name="next-steps"></a>Passaggi successivi
   - [Utenti, sessioni ed eventi](app-insights-usage-segmentation.md)
   - [Grafici a imbuto](usage-funnels.md)
   - [Conservazione](app-insights-usage-retention.md)
   - [Cartelle di lavoro](app-insights-usage-workbooks.md)
   - [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)

