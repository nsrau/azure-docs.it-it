<properties 
	pageTitle="Configurazione di Application Insights SDK con ApplicationInsights.config o .xml" 
	description="Abilitare o disabilitare i moduli di raccolta dati e aggiungere i contatori delle prestazioni e altri parametri" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Configurazione di Application Insights SDK con ApplicationInsights.config o .xml

Application Insights .NET SDK è costituito da alcuni pacchetti NuGet. Il [pacchetto di base](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornisce l'API per l'invio di dati di telemetria ad Application Insights. [Altri pacchetti](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) forniscono _moduli_ e _inizializzatori_ di telemetria per il rilevamento automatico dei dati di telemetria dall'applicazione e dal rispettivo contesto. Modificando il file di configurazione, è possibile abilitare o disabilitare i moduli e gli inizializzatori di telemetria e impostare parametri per alcuni di essi.

Il file di configurazione è denominato `ApplicationInsights.config` o `ApplicationInsights.xml`, a seconda del tipo di applicazione. Viene aggiunto automaticamente al progetto quando si [installano alcune versioni dell'SDK][start]. Viene anche aggiunto a un'app Web da [Status Monitor in un server IIS][redfield] o quando si [seleziona l'estensione Application Insights per un sito Web o una macchina virtuale di Azure][azure].

Non esiste un file equivalente per controllare l'[SDK in una pagina Web][client].

Nel file di configurazione è presente un nodo per ogni modulo. Per disabilitare un modulo, eliminare il nodo o impostarlo come commento.

## Pacchetto NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)

Il pacchetto NuGet `Microsoft.ApplicationInsights` fornisce i moduli di telemetria seguenti in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
Il modulo `DiagnosticsTelemetryModule` segnala errori nel codice di strumentazione stesso di Application Insights, ad esempio, se il codice non è in grado di accedere ai contatori delle prestazioni o se un `ITelemetryInitializer` genera un'eccezione. La telemetria di traccia rilevata da questo modulo viene visualizzata nella [Ricerca diagnostica][diagnostic].

## Pacchetto NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

Il pacchetto NuGet `Microsoft.ApplicationInsights.DependencyCollector` fornisce i moduli di telemetria seguenti in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
Il modulo `DependencyTrackingTelemetryModule` rileva dati di telemetria sulle chiamate alle dipendenze esterne effettuate dall'applicazione, ad esempio richieste HTTP e query SQL. Per far funzionare questo modulo in un server IIS, è necessario [installare Status Monitor][redfield]. Per usarlo nelle app Web o nelle macchine virtuali di Azure, [selezionare l'estensione Application Insights][azure].

È anche possibile scrivere codice personalizzato per il rilevamento delle dipendenze mediante l'[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

## Pacchetto NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

Il pacchetto NuGet `Microsoft.ApplicationInsights.Web` fornisce gli inizializzatori e i moduli di telemetria seguenti in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Inizializzatori**

* `SyntheticTelemetryInitializer` aggiorna le proprietà di contesto `User`, `Session` e `Operation` di tutti gli elementi di telemetria rilevati durante la gestione di una richiesta da un'origine sintetica, ad esempio un test di disponibilità. Il portale di Application Insights non visualizza per impostazione predefinita i dati di telemetria di tipo sintetico.
* `ClientIpHeaderTelemetryInitializer` aggiorna le proprietà `Ip` del contesto `Location` di tutti gli elementi di telemetria in base all'intestazione HTTP `X-Forwarded-For` della richiesta.
* `UserAgentTelemetryInitializer` aggiorna la proprietà `UserAgent` del contesto `User` di tutti gli elementi di telemetria in base all'intestazione HTTP `User-Agent` della richiesta.
* `OperationNameTelemetryInitializer` aggiorna la proprietà `Name` di `RequestTelemetry` e la proprietà `Name` del contesto `Operation` di tutti gli elementi di telemetria in base al metodo HTTP, oltre ai nomi del controller MVC ASP.NET e all'azione richiamata per elaborare la richiesta.
* `OperationNameTelemetryInitializer` aggiorna 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id'.
* `UserTelemetryInitializer` aggiorna le proprietà `Id` e `AcquisitionDate` del contesto `User` per tutti gli elementi di telemetria con i valori estratti dal cookie `ai_user` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente.
* `SessionTelemetryInitializer` aggiorna la proprietà `Id` del contesto `Session` per tutti gli elementi di telemetria con il valore estratto dal cookie `ai_session` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente. 
* `AzureRoleEnvironmentTelemetryInitializer` aggiorna le proprietà `RoleName` e `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con le informazioni estratte dall'ambiente di runtime di Azure.
* `DomainNameRoleInstanceTelemetryInitializer` aggiorna la proprietà `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con il nome di dominio del computer in cui è in esecuzione l'applicazione Web.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aggiorna la proprietà `Version` del contesto `Component` per tutti gli elementi di telemetria con il valore estratto dal file `BuildInfo.config` prodotto dalla compilazione TFS.
* `DeviceTelemetryInitializer` aggiorna le proprietà seguenti del contesto `Device` per tutti gli elementi di telemetria.
 - `Type` viene impostato su "PC".
 - `Id` viene impostato sul nome di dominio del computer in cui è in esecuzione l'applicazione Web.
 - `OemName` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Manufacturer` mediante WMI.
 - `Model` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Model` mediante WMI.
 - `NetworkType` è impostato sul valore estratto da `NetworkInterface`.
 - `Language` è impostato sul nome di `CurrentCulture`.

**Moduli**

* `RequestTrackingTelemetryModule` rileva le richieste ricevute dall'app Web e misura i tempi di risposta.
* `ExceptionTrackingTelemetryModule` rileva le eccezioni non gestite nell'app Web. Vedere [Errori ed eccezioni][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` impone a `TelemetryChannel` di Application Insights di inviare immediatamente i dati, un elemento di telemetria alla volta, quando un debugger viene collegato al processo dell'applicazione. Ciò permette di ridurre la quantità di tempo tra il momenti in cui l'applicazione rileva i dati di telemetria e il momento in cui vengono visualizzati nel portale di Application Insights, con un costo significativo per la CPU e la larghezza di banda di rete.

## Pacchetto NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

Il pacchetto NuGet `Microsoft.ApplicationInsights.PerfCounterCollector` aggiunge i moduli di telemetria seguenti ad `ApplicationInsights.config` per impostazione predefinita.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` controlla alcuni contatori delle prestazioni di Windows. Questi contatori vengono visualizzati quando si fa clic su un grafico in Esplora metriche per aprire il rispettivo pannello dei dettagli.

È possibile monitorare altri contatori delle prestazioni, sia i contatori standard di Windows che gli eventuali contatori aggiunti.
      
Usare la sintassi seguente per raccogliere gli altri contatori delle prestazioni:

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
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

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Determina la frequenza con cui vengono scaricati (inviati ad Application Insights) i dati nella risorsa di archiviazione in memoria.

-	Min: 1
-	Max: 300
-	Valore predefinito: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Determina le dimensioni massime in MB assegnate all'archivio permanente sul disco locale. Questo archivio viene usato per archiviare in modo permanente gli elementi di telemetria che non sono stati trasmessi all'endpoint di Application Insights. Quando vengono raggiunte le dimensioni di archiviazione, i nuovi elementi di telemetria verranno eliminati.

-	Min: 1
-	Max: 100
-	Valore predefinito: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

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
[start]: app-insights-overview.md

<!---HONumber=Oct15_HO3-->