---
title: 'Informazioni di riferimento su ApplicationInsights.config: Azure | Documentazione Microsoft'
description: Abilitare o disabilitare i moduli di raccolta dati e aggiungere i contatori delle prestazioni e altri parametri.
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
editor: alancameronwills
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/3/2017
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52b5be98742c9bf0834c12136416e856af5d99cc
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurazione di Application Insights SDK con ApplicationInsights.config o .xml
Application Insights .NET SDK è costituito da alcuni pacchetti NuGet. Il [pacchetto di base](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornisce l'API per l'invio di dati di telemetria ad Application Insights. [Altri pacchetti](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) forniscono *moduli* e *inizializzatori* di telemetria per il rilevamento automatico dei dati di telemetria dall'applicazione e dal rispettivo contesto. Modificando il file di configurazione, è possibile abilitare o disabilitare i moduli e gli inizializzatori di telemetria e impostare parametri per alcuni di essi.

Il file di configurazione è denominato `ApplicationInsights.config` o `ApplicationInsights.xml`, a seconda del tipo di applicazione. Viene aggiunto automaticamente al progetto quando si [installano alcune versioni dell'SDK][start]. Viene anche aggiunto a un'app Web da [Status Monitor][redfield] in un server IIS o quando si seleziona l'[estensione Application Insights per un sito Web o una macchina virtuale di Azure](app-insights-azure-web-apps.md).

Non esiste un file equivalente per controllare l'[SDK in una pagina Web][client].

Questo documento illustra le sezioni visibili nel file di configurazione, il modo in cui esse controllano i componenti SDK, e quali pacchetti NuGet caricano questi componenti.

## <a name="telemetry-modules-aspnet"></a>Moduli di telemetria (ASP.NET)
Ogni modulo di telemetria raccoglie un tipo specifico di dati e utilizza l’API principale per inviare i dati. I moduli sono installati da diversi pacchetti NuGet che aggiungono anche le linee necessarie al file .config.

Nel file di configurazione è presente un nodo per ogni modulo. Per disabilitare un modulo, eliminare il nodo o impostarlo come commento.

### <a name="dependency-tracking"></a>Rilevamento delle dipendenze
[Rilevamento delle dipendenze](app-insights-asp-net-dependencies.md) raccoglie la telemetria delle chiamate effettuate dall’applicazione ai database e ai database e servizi esterni. Per far funzionare questo modulo in un server IIS, è necessario [installare Status Monitor][redfield]. Per usarlo nelle app Web o nelle macchine virtuali di Azure, [selezionare l'estensione Application Insights](app-insights-azure-web-apps.md).

È anche possibile scrivere codice personalizzato per il rilevamento delle dipendenze mediante l' [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Agente di raccolta dati delle prestazioni
[Raccoglie contatori delle prestazioni di sistema](app-insights-performance-counters.md) come CPU, memoria e rete caricate dalle istallazioni IIS. E’ possibile specificare quali contatori raccogliere, inclusi i contatori delle prestazioni installati.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria delle diagnostiche Application Insights
Il modulo `DiagnosticsTelemetryModule` segnala errori nel codice di strumentazione stesso di Application Insights, ad esempio, se il codice non è in grado di accedere ai contatori delle prestazioni o se un `ITelemetryInitializer` genera un'eccezione. La telemetria di traccia rilevata da questo modulo viene visualizzata nella [Ricerca diagnostica][diagnostic]. Invia i dati di diagnostica a dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se si installa questo pacchetto, il file ApplicationInsights.config non viene creato automaticamente.

### <a name="developer-mode"></a>Modalità di sviluppo
`DeveloperModeWithDebuggerAttachedTelemetryModule` impone a `TelemetryChannel` di Application Insights di inviare immediatamente i dati, un elemento di telemetria alla volta, quando un debugger viene collegato al processo dell'applicazione. Ciò permette di ridurre la quantità di tempo tra il momento in cui l'applicazione rileva i dati di telemetria e il momento in cui vengono visualizzati nel portale di Application Insights. Questo causa un costo significativo per la CPU e la larghezza di banda di rete.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) 

### <a name="web-request-tracking"></a>Rilevamento delle richieste web
Riporta il [tempo di risposta e il codice dei risultati](app-insights-asp-net.md) delle richieste HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 

### <a name="exception-tracking"></a>Rilevamento delle eccezioni
`ExceptionTrackingTelemetryModule` rileva le eccezioni non gestite nell'app Web. Vedere [Errori ed eccezioni][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - rileva le [eccezioni delle attività non notate](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - rileva le eccezioni non gestite per i ruoli di lavoro, servizi windows, e applicazioni della console.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) .

### <a name="eventsource-tracking"></a>Rilevamento EventSource
`EventSourceTelemetryModule` consente di configurare eventi EventSource da inviare ad Application Insights come tracce. Per informazioni su eventi EventSource di rilevamento, vedere [Using EventSource Events](app-insights-asp-net-trace-logs.md#using-eventsource-events) (uso degli eventi EventSource).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Registrazione degli eventi ETW
`EtwCollectorTelemetryModule` consente di configurare gli eventi dai provider ETW da inviare ad Application Insights come tracce. Per informazioni sul rilevamento di eventi ETW, vedere [Using ETW Events](app-insights-asp-net-trace-logs.md#using-etw-events) (Uso di eventi ETW).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Il pacchetto Microsoft.ApplicationInsights include l' [API principale](https://msdn.microsoft.com/library/mt420197.aspx) dell'SDK. Gli altri moduli di telemetria utilizzano questo pacchetto, ed è possibile anche [utilizzarlo per definire la propria telemetria](app-insights-api-custom-events-metrics.md).

* Non ci sono voci in ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se si installa questo NuGet, non viene generato nessun file .config.

## <a name="telemetry-channel"></a>Canale di telemetria
Il canale di telemetria gestisce i buffering e la trasmissione della telemetria al servizio Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` è il canale predefinito per i servizi. Esegue il buffering dei dati in memoria.
* `Microsoft.ApplicationInsights.PersistenceChannel` è un’alternativa per le applicazioni della console. Esso può salvare qualsiasi dato unflushed nell’archivio permanente quando si arresta l’applicazione, e lo invia quando la si riapre.

## <a name="telemetry-initializers-aspnet"></a>Inizializzatori di telemetria (ASP.NET)
Gli inizializzatori di telemetria impostano proprietà di contesto che vengono inviate insieme ad ogni elemento di telemetria.

E’ possibile [scrivere i propri inizializzatori](app-insights-api-filtering-sampling.md#add-properties) per impostare proprietà di contesto.

Gli inizializzatori standard sono tutti impostati dal Web o dai pacchetti NuGet WindowsServer:

* `AccountIdTelemetryInitializer` imposta la proprietà AccountId.
* `AuthenticatedUserIdTelemetryInitializer` imposta la proprietà AuthenticatedUserId come impostata dal SDK di JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` aggiorna le proprietà `RoleName` e `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con le informazioni estratte dall'ambiente di runtime di Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aggiorna la proprietà `Version` del contesto `Component` per tutti gli elementi di telemetria con il valore estratto dal file `BuildInfo.config` prodotto dalla compilazione MS.
* `ClientIpHeaderTelemetryInitializer` aggiorna le proprietà `Ip` del contesto `Location` di tutti gli elementi di telemetria in base all'intestazione HTTP `X-Forwarded-For` della richiesta.
* `DeviceTelemetryInitializer` aggiorna le proprietà seguenti del contesto `Device` per tutti gli elementi di telemetria.
  * `Type` viene impostato su "PC".
  * `Id` viene impostato sul nome di dominio del computer in cui è in esecuzione l'applicazione Web.
  * `OemName` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Manufacturer` mediante WMI.
  * `Model` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Model` mediante WMI.
  * `NetworkType` è impostato sul valore estratto da `NetworkInterface`.
  * `Language` è impostato sul nome di `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aggiorna la proprietà `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con il nome di dominio del computer in cui è in esecuzione l'applicazione Web.
* `OperationNameTelemetryInitializer` aggiorna la proprietà `Name` di `RequestTelemetry` e la proprietà `Name` del contesto `Operation` di tutti gli elementi di telemetria in base al metodo HTTP, oltre ai nomi del controller MVC ASP.NET e all'azione richiamata per elaborare la richiesta.
* `OperationIdTelemetryInitializer` o `OperationCorrelationTelemetryInitializer` aggiorna la proprietà di contesto `Operation.Id` di tutti gli elementi di telemetria rilevati durante la gestione di una richiesta con il `RequestTelemetry.Id` generato automaticamente.
* `SessionTelemetryInitializer` aggiorna la proprietà `Id` del contesto `Session` per tutti gli elementi di telemetria con il valore estratto dal cookie `ai_session` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente.
* `SyntheticTelemetryInitializer` o `SyntheticUserAgentTelemetryInitializer` aggiorna le proprietà di contesto `User`, `Session` e `Operation` di tutti gli elementi di telemetria rilevati durante la gestione di una richiesta da un'origine sintetica, ad esempio un test di disponibilità o un robot del motore di ricerca. Per impostazione predefinita, [Esplora metriche](app-insights-metrics-explorer.md) non mostra la telemetria sintetica.

    `<Filters>` imposta le proprietà di identificazione delle richieste.
* `UserAgentTelemetryInitializer` aggiorna la proprietà `UserAgent` del contesto `User` di tutti gli elementi di telemetria in base all'intestazione HTTP `User-Agent` della richiesta.
* `UserTelemetryInitializer` aggiorna le proprietà `Id` e `AcquisitionDate` del contesto `User` per tutti gli elementi di telemetria con i valori estratti dal cookie `ai_user` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente.
* `WebTestTelemetryInitializer` imposta l'id utente, l'id di sessione e le proprietà di origine sintetica per le richieste HTTP che provengono da [test di disponibilità](app-insights-monitor-web-app-availability.md).
  `<Filters>` imposta le proprietà di identificazione delle richieste.

Per le applicazioni .NET in esecuzione in Service Fabric, è possibile includere il pacchetto NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Questo pacchetto include `FabricTelemetryInitializer`, che aggiunge le proprietà di Service Fabric per gli elementi di telemetria. Per ulteriori informazioni, vedere la [pagina GitHub](https://go.microsoft.com/fwlink/?linkid=848457) sulle proprietà aggiunte dal pacchetto NuGet.

## <a name="telemetry-processors-aspnet"></a>Processori di telemetria (ASP.NET)
I processori di telemetria possono filtrare e modificare ciascun elemento di telemetria prima di inviarlo dal SDK al portale.

E’ possibile [scrivere i propri processori di telemetria](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processore di telemetria di campionamento adattivo (da 2.0.0-beta3)
Questa opzione è abilitata per impostazione predefinita. Se l'app invia molti dati di telemetria, questo processore rimuove alcune di esse.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Il parametro fornisce la destinazione che l'algoritmo tenta di ottenere. Ogni istanza di SDK funziona in modo indipendente, pertanto se il server è un cluster di più computer, il volume dei dati di telemetria verrà di conseguenza moltiplicato.

[Altre informazioni sul campionamento](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processore di telemetria di campionamento adattivo (da 2.0.0-beta1)
È inoltre disponibile un [processore di telemetria di campionamento](app-insights-api-filtering-sampling.md) standard (da 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parametri del canale (Java)
Questi parametri influiscono sul modo in cui l'SDK per Java deve archiviare e scaricare i dati di telemetria raccolti.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Indica il numero di elementi di telemetria che possono essere archiviati nella risorsa di archiviazione in memoria dell'SDK. Quando viene raggiunto questo numero, il buffer di telemetria viene scaricato, ovvero gli elementi di telemetria vengono inviati al server Application Insights.

* Min: 1
* Max: 1000
* Valore predefinito: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Determina la frequenza con cui vengono scaricati (inviati ad Application Insights) i dati nella risorsa di archiviazione in memoria.

* Min: 1
* Max: 300
* Valore predefinito: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Determina le dimensioni massime in MB assegnate all'archivio permanente sul disco locale. Questo archivio viene usato per archiviare in modo permanente gli elementi di telemetria che non sono stati trasmessi all'endpoint di Application Insights. Quando vengono raggiunte le dimensioni di archiviazione, i nuovi elementi di telemetria verranno eliminati.

* Min: 1
* Max: 100
* Valore predefinito: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Determina la risorsa di Application Insights in cui vengono visualizzati i dati. In genere, viene creata una risorsa separata, con una chiave separata, per ognuna delle applicazioni.

Se si vuole impostare la chiave in modo dinamico, ad esempio se si intende inviare i risultati dall'applicazione a diverse risorse, è possibile omettere la chiave dal file di configurazione e impostarla nel codice.

Per impostare la chiave per tutte le istanze di TelemetryClient, inclusi i moduli di telemetria standard, impostare la chiave in TelemetryConfiguration.Active. Eseguire questa operazione in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se si vuole inviare un set specifico di eventi a una risorsa diversa, è possibile impostare la chiave per un oggetto TelemetryClient specifico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Per ottenere una nuova chiave, [creare una nuova risorsa nel portale di Application Insights][new].

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sull'API][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

