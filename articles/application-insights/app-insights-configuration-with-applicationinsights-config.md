<properties 
	pageTitle="Configurazione di Application Insights SDK con ApplicationInsights.config o .xml" 
	description="Abilitare o disabilitare i moduli di raccolta dati e aggiungere i contatori delle prestazioni e altri parametri" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Configurazione di Application Insights SDK con ApplicationInsights.config o .xml

Application Insights SDK è costituito da diversi moduli. Il modulo principale fornisce l'API di base che invia i dati di telemetria al portale di Application Insights. I moduli aggiuntivi raccolgono i dati dall'applicazione e dal relativo contesto. Modificando il file di configurazione, è possibile abilitare o disabilitare i moduli e impostare i parametri per alcuni di essi.

Il file di configurazione è denominato `ApplicationInsights.config` o `ApplicationInsights.xml`, a seconda del tipo di applicazione. Viene aggiunto automaticamente al progetto quando si [installa l'SDK][start]. Viene anche aggiunto a un'app Web da [Status Monitor in un server IIS][redfield] o quando si [seleziona l'estensione Application Insights per un sito Web o una macchina virtuale di Azure][azure].

Non esiste un file equivalente per controllare l'[SDK in una pagina Web][client].


## Moduli di telemetria

Nel file di configurazione è presente un nodo per ogni modulo. Per disabilitare un modulo, eliminare il nodo o impostarlo come commento.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Segnala gli errori nell'SDK. Ad esempio, se l'SDK non può accedere ai contatori delle prestazioni o se un oggetto TelemetryInitializer personalizzato genera un'eccezione.

I dati vengono visualizzati in [Ricerca diagnostica][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Raccoglie i dati sulla velocità di risposta dei componenti esterni usati dall'applicazione. Per far funzionare questo modulo in un server IIS, è necessario [installare Status Monitor][redfield]. Per usarlo nelle app Web o nelle macchine virtuali di Azure, [selezionare l'estensione Application Insights][azure].

#### Web.WebApplicationLifecycleModule

Prova a scaricare tutti i buffer in memoria dei dati di telemetria in modo che non vengano persi alla chiusura di un processo.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Conta le richieste che arrivano nell'app Web e misura i tempi di risposta.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Conta le eccezioni non gestite nell'app Web. Vedere [Errori ed eccezioni][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Modulo dell'agente di raccolta dati delle prestazioni

#### PerfCollector.PerformanceCollectorModule

Per impostazione predefinita, questo modulo raccoglie una serie di contatori delle prestazioni di Windows. È possibile visualizzare questi contatori quando si apre il pannello Filtri in Esplora metriche.

È possibile monitorare altri contatori delle prestazioni, sia i contatori standard di Windows che gli eventuali contatori aggiunti.
      
Usare la sintassi seguente per raccogliere gli altri contatori delle prestazioni:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` deve essere `\CategoryName(InstanceName)\CounterName` o `\CategoryName\CounterName`.
      
Se si fornisce l'attributo `ReportAs`, questo verrà usato come nome visualizzato in Application Insights.

Per la creazione di report in Application Insights, i nomi dei contatori devono includere solo lettere, parentesi tonde, barre, trattini, caratteri di sottolineatura, spazi e punti.

È necessario usare ReportAs se il contatore da monitorare contiene caratteri non validi, ad esempio '#' o cifre.
      
I seguenti segnaposto sono supportati come `InstanceName`:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Parametri del canale (Java)

Questi parametri influiscono sul modo in cui l'SDK per Java deve archiviare e scaricare i dati di telemetria raccolti.

#### MaxTelemetryBufferCapacity

Indica il numero di elementi di telemetria che possono essere archiviati nella risorsa di archiviazione in memoria dell'SDK. Quando viene raggiunto questo numero, il buffer di telemetria viene scaricato, ovvero gli elementi di telemetria vengono inviati al server Application Insights.

-	Min: 1
-	Max: 1000
-	Valore predefinito: 500

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Determina la frequenza con cui vengono scaricati (inviati ad Application Insights) i dati nella risorsa di archiviazione in memoria.

-	Min: 1
-	Max: 300
-	Valore predefinito: 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Determina le dimensioni massime in MB assegnate all'archivio permanente sul disco locale. Questo archivio viene usato per archiviare in modo permanente gli elementi di telemetria che non sono stati trasmessi all'endpoint di Application Insights. Quando vengono raggiunte le dimensioni di archiviazione, i nuovi elementi di telemetria verranno eliminati.

-	Min: 1
-	Max: 100
-	Valore predefinito: 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Inizializzatori di contesto

Questi componenti raccolgono i dati dalla piattaforma. I dati vengono raccolti nell'[oggetto TelemetryContext][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Inizializzatori di telemetria

Questi componenti aggiungono i dati a ogni evento di telemetria inviato ad Application Insights.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Questo componente identifica le richieste HTTP che sembrano provenire da robot, quali motori di ricerca e test Web. Imposta TelemetryClient.Context.Operation.SyntheticSource.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Aggiunge un nome di operazione a ogni elemento di telemetria. Per le app Web, con "operazione" si intende una richiesta HTTP. Imposta TelemetryClient.Context.Operation.Name

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Consente alla funzionalità di "trovare gli eventi nella stessa richiesta" in [Ricerca diagnostica][diagnostic]. Imposta TelemetryClient.Context.Operation.Id

Aggiunge un ID operazione a ogni elemento di dati inviato ad Application Insights. Per le app Web, un'"operazione" è una richiesta HTTP. Quindi, ad esempio, la richiesta e tutti gli eventi e le tracce personalizzati che fanno parte dell'elaborazione della richiesta contengono tutti lo stesso ID operazione.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Aggiunge un ID utente anonimo a ogni elemento di telemetria. In questo modo è possibile filtrare solo gli eventi relativi alle attività di un utente nella ricerca diagnostica. Se ad esempio viene segnalata un'eccezione, è possibile tracciare l'operazione che l'utente stava eseguendo.

Imposta telemetryClient.Context.User

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Aggiunge un ID sessione a ogni evento. Imposta telemetryClient.Context.Session

## Inizializzatori personalizzati


Se gli inizializzatori standard non sono adatti per l'applicazione, è possibile creare degli inizializzatori personalizzati.

Usare gli inizializzatori di contesto per impostare i valori che verranno usati per inizializzare ogni client di telemetria. Se ad esempio è stata pubblicata più di una versione dell'app, potrebbe essere necessario separare i dati filtrando in base a una proprietà personalizzata:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Usare gli inizializzatori di telemetria per aggiungere l'elaborazione a ogni evento. Ad esempio, l'SDK Web contrassegna come non riuscita qualsiasi richiesta con un codice di risposta >= 400. È possibile eseguire l'override di questo comportamento:

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Per installare gli inizializzatori, aggiungere le righe in ApplicationInsights.config:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


In alternativa, è possibile scrivere il codice per installare l'inizializzatore all'inizio dell'esecuzione dell'applicazione. Ad esempio:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

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

[Altre informazioni sull'API][api].

Per ottenere una nuova chiave, [creare una nuova risorsa nel portale di Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->