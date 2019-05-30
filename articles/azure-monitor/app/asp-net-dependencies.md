---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Monitorare le chiamate di dipendenza in locale o un'applicazione web di Microsoft Azure con Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299284"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Monitoraggio delle dipendenze in Azure Application Insights 

Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](../../azure-monitor/app/app-insights-overview.md) misurare la durata delle chiamate di dipendenza, se il non superati o No, insieme ad altre informazioni, ad esempio nome della dipendenza e così via. È possibile esaminare chiamate di dipendenza specifica e metterle in correlazione a richieste ed eccezioni.

## <a name="automatically-tracked-dependencies"></a>Dipendenze rilevate automaticamente

Application Insights SDK per .NET e .NET Core viene fornito con `DependencyTrackingTelemetryModule` che è un modulo di telemetria che raccoglie automaticamente le dipendenze. Questa raccolta di dipendenze è abilitata automaticamente per [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) applicazioni, quando configurati in base ai documenti ufficiali collegati. `DependencyTrackingTelemetryModule` viene fornito come [ciò](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacchetto NuGet e verrà portato automaticamente quando si usa uno dei pacchetti NuGet `Microsoft.ApplicationInsights.Web` o `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` attualmente rileva automaticamente le dipendenze seguenti:

|Dependencies |Dettagli|
|---------------|-------|
|Http/Https | Locale o remoto chiamate http/https |
|Chiamate WCF| Automaticamente registrate solo se si utilizzano associazioni basate su Http.|
|SQL | Le chiamate effettuate con `SqlClient`. Visualizzare [ciò](##advanced-sql-tracking-to-get-full-sql-query) per l'acquisizione di SQL eseguire una query.  |
|[Archiviazione di Azure (Blob, tabelle, code)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il Client di archiviazione di Azure. |
|[SDK del Client dell'hub eventi](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e versioni successive. |
|[SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e versioni successive. |
|Azure Cosmos DB | Automaticamente registrate solo se viene utilizzato HTTP/HTTPS. La modalità TCP non verrà acquisita da Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Installazione automatica delle dipendenze di rilevamento in applicazioni Console

Per rilevare automaticamente le dipendenze dall'App console Core.NET/.NET, installare il pacchetto Nuget `Microsoft.ApplicationInsights.DependencyCollector`e inizializzare `DependencyTrackingTelemetryModule` come indicato di seguito:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Dipendenza automatico monitoraggio funziona?

Le dipendenze vengono raccolti automaticamente usando una delle tecniche seguenti:

* Utilizzo di strumentazione con codice byte intorno a metodi di selezione. (InstrumentationEngine dal StatusMonitor o l'estensione dell'App Web di Azure)
* Callback di EventSource
* Callback di DiagnosticSource (negli.NET/.NET Core SDK più recente)

## <a name="manually-tracking-dependencies"></a>Manualmente le dipendenze di rilevamento

Di seguito sono riportati alcuni esempi di dipendenze, che non vengono raccolte automaticamente e pertanto richiedono la verifica manuale.

* viene tenuta automaticamente traccia di Azure Cosmos DB solo se è usato [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). La modalità TCP non verrà acquisita da Application Insights.
* Redis

Per tali dipendenze raccolte automaticamente dal SDK, è possibile tenere traccia manualmente tramite il [API TrackDependency](api-custom-events-metrics.md#trackdependency) usato dai moduli di raccolta standard automaticamente.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

In alternativa `TelemetryClient` fornisce metodi di estensione `StartOperation` e `StopOperation` che può essere usato per rilevamento manuale delle dipendenze, come illustrato [qui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se si desidera disattivare il modulo di rilevamento delle dipendenze standard, rimuovere il riferimento a DependencyTrackingTelemetryModule in [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per le applicazioni ASP.NET. Per le applicazioni ASP.NET Core, seguire le istruzioni [qui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Tiene traccia delle chiamate AJAX da pagine Web

Per le pagine web, Application Insights JavaScript SDK raccoglie automaticamente, le chiamate AJAX come dipendenze come descritto [qui](javascript.md#ajax-performance). Questo documento è incentrato sulle dipendenze da componenti server.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avanzate per ottenere Query SQL completa di rilevamento SQL

Per le chiamate SQL, il nome del server e del database è sempre raccolti e archiviato come nome del raccolti `DependencyTelemetry`. C'è un campo aggiuntivo denominato 'data', che può contenere il testo della query SQL completo.

Per le applicazioni ASP.NET Core, non è disponibile alcun passaggio aggiuntivo necessario per ottenere la Query SQL completa.

Per le applicazioni ASP.NET, query SQL completa viene raccolto con l'aiuto di strumentazione con codice byte, che richiede il motore di strumentazione. Specifica della piattaforma, come descritto di seguito, sono necessari passaggi aggiuntivi.

| Piattaforma | Passaggi necessari per ottenere Query SQL completa |
| --- | --- |
| App Web di Azure |Nel Pannello di controllo per l'app web, [aprire il pannello di Application Insights](../../azure-monitor/app/azure-web-apps.md) e abilitare i comandi SQL in .NET |
| Server IIS (macchina virtuale di Azure, in locale e così via). | [Installare Status Monitor nel server in cui sono in esecuzione applicazioni](../../azure-monitor/app/monitor-performance-live-website-now.md) e riavviare IIS.
| Servizio cloud di Azure |[Attività di avvio di uso](../../azure-monitor/app/cloudservices.md) a [installare Status Monitor](monitor-performance-live-website-now.md#download) |
| IIS Express | Non supportate

Nei casi precedenti, il modo corretto di convalida che il motore di strumentazione è installato correttamente è necessario convalidare che la versione del SDK raccolti `DependencyTelemetry` è 'rddp'. 'rdddsd' o 'rddf' indica che le dipendenze vengono raccolti tramite i callback di DiagnosticSource o EventSource e pertanto non sarà possibile acquisire query SQL completa.

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze

* La [mappa delle applicazioni](app-map.md) visualizza le dipendenze tra l'app e i componenti adiacenti.
* [Diagnostica delle transazioni](transaction-diagnostics.md) Mostra unificata, correlare i dati del server.
* Il [pannello Browser](javascript.md#ajax-performance) visualizza le chiamate AJAX dai browser degli utenti.
* Fare clic sulle richieste lente o non riuscite per controllare le chiamate alle dipendenze.
* L'[analisi](#analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="diagnosis"></a> Diagnosticare le richieste lente

Ogni evento di richiesta è associato alle chiamate alle dipendenze, alle eccezioni e ad altri eventi registrati mentre l'app elabora la richiesta. Pertanto, se alcune richieste siano eseguendo in modo errato, è possibile trovare definire se si trova a causa di una risposta lenta da una dipendenza.

Di seguito è illustrato un esempio.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze

Aprire il pannello Prestazioni ed esaminare la griglia delle richieste:

![Elenco di richieste con conteggi e medie](./media/asp-net-dependencies/02-reqs.png)

Quella superiore impiega tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:

![Elenco di occorrenze di richiesta](./media/asp-net-dependencies/03-instances.png)

Fare clic su un'istanza a esecuzione prolungata per esaminarla meglio e scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/asp-net-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:

![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/asp-net-dependencies/05-detail.png)

Probabilmente questa dipendenza è l'origine del problema. Dopo avere individuato il problema, rimane solo da capire perché la chiamata sta durando così tanto.

### <a name="request-timeline"></a>Sequenza temporale della richiesta

In un altro caso non ci sono chiamate a dipendenze particolarmente lunghe, ma, passando alla visualizzazione della sequenza temporale, è possibile vedere il punto in cui si è verificato il ritardo nell'elaborazione interna:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/asp-net-dependencies/04-1.png)

Dopo la prima chiamata alla dipendenza si verifica una lunga pausa, quindi è opportuno esaminare il codice per capirne il motivo.

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, Il [di Application Insights profiler](../../azure-monitor/app/profiler.md) tracce HTTP chiama al sito live e Mostra le funzioni nel codice che che richiedono più tempo.

## <a name="failed-requests"></a>Richieste non riuscite

Le richieste non riuscite possono anche essere associate a chiamate non riuscite a dipendenze. Anche in questo caso è possibile fare clic per risalire al problema.

![Fare clic sul grafico delle richieste non riuscite](./media/asp-net-dependencies/06-fail.png)

Fare clic su un'occorrenza di una richiesta non riuscita ed esaminare gli eventi associati.

![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics

È possibile tenere traccia delle dipendenze nel [linguaggio di query Kusto](/azure/kusto/query/). Di seguito sono riportati alcuni esempi.

* Trovare eventuali chiamate alle dipendenze non riuscite:

```

    dependencies | where success != "True" | take 10
```

* Trovare le chiamate AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Trovare le chiamate alle dipendenze associate alle richieste:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Trovare le chiamate AJAX associate alle visualizzazioni di pagina:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Come fa automatico delle dipendenze dell'agente di raccolta report non è stato possibile chiamate alle dipendenze?*

* Le chiamate a dipendenze non riuscite conterranno il campo di 'success' impostato su False. `DependencyTrackingTelemetryModule` non segnala `ExceptionTelemetry`. Viene descritto il modello di dati completo per la dipendenza [qui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK open source
Ad esempio ogni SDK di Application Insights, il modulo di raccolta delle dipendenze è anche open source. Leggere e contribuire al codice o segnalare eventuali problemi [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>Passaggi successivi

* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
* [Dati utente e di pagina](../../azure-monitor/app/javascript.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
