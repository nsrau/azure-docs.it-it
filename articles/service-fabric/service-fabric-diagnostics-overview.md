---
title: Panoramica di monitoraggio e diagnostica di Azure Service Fabric | Microsoft Docs
description: Informazioni su come monitorare e diagnosticare le applicazioni di Microsoft Azure Service Fabric ospitate in Azure, nell&quot;ambiente di sviluppo o in locale.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Monitorare e diagnosticare le applicazioni di Azure Service Fabric

Il monitoraggio e la diagnostica sono essenziali in un ambiente di produzione live. Azure Service Fabric consente di implementare il monitoraggio e la diagnostica durante lo sviluppo del servizio, per assicurare il funzionamento perfetto del servizio in un ambiente di sviluppo locale con computer singolo e in un'installazione cluster di produzione reale.

Il monitoraggio e la diagnostica offrono i vantaggi seguenti durante lo sviluppo dei servizi:
* Riduzione al minimo dei disagi per i clienti.
* Approfondimenti professionali.
* Monitoraggio dell'uso delle risorse.
* Rilevamento di errori hardware e software o dei problemi di prestazioni.
* Diagnosi dei potenziali problemi per i servizi.

Il monitoraggio è un termine ampio che include le attività seguenti:
* Strumentazione del codice
* Raccolta di log di strumentazione
* Analisi dei log
* Visualizzazione degli approfondimenti in base ai dati dei log
* Configurazione degli avvisi in base ai valori dei log e agli approfondimenti
* Monitoraggio dell'infrastruttura
* Rilevamento e diagnosi dei problemi che interessano i clienti

Questo articolo offre una panoramica del monitoraggio per cluster di Service Fabric ospitati in Azure, in locale, distribuiti in Windows o Linux oppure tramite Microsoft .NET Framework. Vengono esaminati tre aspetti importanti del monitoraggio e della diagnostica:
- Strumentazione di codice o di infrastruttura
- Raccolta di eventi generati
- Archiviazione, aggregazione, visualizzazione e analisi

Anche se sono disponibili molti prodotti che comprendono tutte e tre le aree, molti clienti scelgono tecnologie diverse per ogni aspetto del monitoraggio. Per offrire una soluzione di monitoraggio end-to-end per l'applicazione, è importante l'interazione tra ogni elemento.

## <a name="monitoring-infrastructure"></a>Monitoraggio dell'infrastruttura

Service Fabric consente di mantenere un'applicazione in esecuzione in caso di errori di infrastruttura, ma è necessario capire se un errore si verifica nell'applicazione o nell'infrastruttura sottostante. È anche necessario monitorare l'infrastruttura per la pianificazione della capacità, in modo da sapere quando è necessario aggiungere o rimuovere infrastruttura. È importante monitorare e risolvere i problemi dell'infrastruttura e dell'applicazione che compongono una distribuzione di Service Fabric. Anche se un'applicazione è disponibile per i clienti, è possibile che si verifichino ancora problemi per l'infrastruttura.

### <a name="azure-monitor"></a>Monitoraggio di Azure

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) per monitorare molte risorse di Azure in cui viene creato un cluster di Service Fabric. Un set di metriche per il [set di scalabilità di macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e per le singole [macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) viene raccolto automaticamente e visualizzato nel portale di Azure. Per visualizzare le informazioni raccolte, nel portale di Azure selezionare il gruppo di risorse che contiene il cluster di Service Fabric. Selezionare quindi il set di scalabilità di macchine virtuali da visualizzare. Nella sezione **Monitoraggio** selezionare **Metriche** per visualizzare un grafico dei valori.

![Visualizzazione del portale di Azure dei dati di metrica raccolti](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

Per personalizzare i grafici, seguire le istruzioni disponibili in [Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). È anche possibile creare avvisi in base a queste metriche, come illustrato in [Creare avvisi in Monitoraggio di Azure per servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). È possibile inviare avvisi a un servizio di notifica usando webhook, come illustrato in [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Monitoraggio di Azure supporta solo una sottoscrizione. Se è necessario monitorare più sottoscrizioni o se sono necessarie funzionalità aggiuntive, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), incluso in Microsoft Operations Management Suite, fornisce una soluzione olistica di gestione IT per infrastrutture locali e basate sul cloud. È possibile indirizzare i dati di Monitoraggio di Azure direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

È consigliabile usare Operations Management Suite per monitorare l'infrastruttura locale, ma è possibile usare qualsiasi soluzione esistente usata dall'organizzazione per il monitoraggio dell'infrastruttura.

### <a name="service-fabric-support-logs"></a>Log di supporto di Service Fabric

Se è necessario contattare il supporto Microsoft per assistenza con il cluster di Azure Service Fabric, saranno quasi sempre richiesti i log di supporto. Se il cluster è ospitato in Azure, questi log vengono automaticamente configurati e raccolti in fase di creazione di un cluster. I log vengono archiviati in un account di archiviazione dedicato nel gruppo di risorse del cluster. L'account di archiviazione non ha un nome predefinito, ma nell'account vengono visualizzati contenitori BLOB e tabelle con nomi che iniziano con *fabric*. Per informazioni sulla configurazione di raccolte di log per un cluster autonomo, vedere [Creare un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Per istanze autonome di Service Fabric, i log devono essere inviati a una condivisione file locale. È **obbligatorio** disporre di questi log per ottenere assistenza. I log devono essere usati solo dal team di assistenza clienti Microsoft.

## <a name="instrument-your-code"></a>Instrumentare il codice

La strumentazione del codice è la base di molti altri aspetti di monitoraggio dei servizi. La strumentazione è l'unico modo per rilevare eventuali problemi e individuare le correzioni necessarie. Anche se è tecnicamente possibile connettere un debugger a un servizio di produzione, questa non è una procedura comune. I dati dettagliati di strumentazione sono quindi importanti. Quando si crea questo volume di informazioni, trasferire tutti gli eventi nel nodo locale può essere oneroso. Molti servizi usano una strategia in due parti per gestire il volume dei dati di strumentazione:
1.  Tutti gli eventi vengono mantenuti nel file di log in sequenza locale per un breve intervallo e vengono raccolti solo quando necessario per il debug. In genere gli eventi necessari per la diagnostica dettagliata vengono lasciati nel nodo in modo da ridurre i costi e l'utilizzo delle risorse.
2.  Eventuali eventi relativi all'integrità del servizio vengono inviati a un repository centrale, dove possono essere usati per generare avvisi relativi a un servizio non integro. Gli eventi relativi all'integrità del servizio includono eventi di errore, eventi heartbeat ed eventi relativi alle prestazioni.

Alcuni prodotti instrumentano automaticamente il codice. Benché queste soluzioni siano efficaci, è quasi sempre necessaria una strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. Le sezioni successive illustrano diversi approcci per la strumentazione del codice e indicano quando è consigliabile scegliere un approccio rispetto a un altro.

### <a name="eventsource"></a>EventSource

Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe derivata da **EventSource** (**ServiceEventSource** o **ActorEventSource**). Viene creato un modello in cui è possibile aggiungere eventi per l'applicazione o il servizio. Il nome di **EventSource** **deve** essere univoco e deve essere rinominato dalla stringa del modello predefinito MyCompany-&lt;soluzione&gt;-&lt;progetto&gt;. Se esistono più definizioni di **EventSource** con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Alcune organizzazioni preassegnano intervalli di valori per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni, vedere il [blog di Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentazione di MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Uso di eventi EventSource strutturati

Ogni evento negli esempi di codice di questa sezione è definito per un caso specifico, ad esempio, quando viene registrato un tipo di servizio. Quando si definiscono i messaggi in base al caso di utilizzo, è possibile creare pacchetti dei dati e con il testo dell'errore, quindi eseguire ricerche e applicare filtri con maggiore facilità in base ai nomi o ai valori delle proprietà specificate. Strutturando l'output della strumentazione è possibile usarlo con più facilità, ma ciò richiede sforzi maggiori per definire un nuovo evento per ciascun caso d'uso. Alcune definizioni di evento possono essere condivise nell'intera applicazione. Un evento di avvio o arresto di un metodo, ad esempio, può essere riutilizzato in molti servizi in un'applicazione. Un servizio specifico per un dominio, ad esempio un sistema di ordine, può avere un evento **CreateOrder**, che ha un proprio evento univoco. Questo approccio può generare un numero elevato di eventi e può potenzialmente richiedere il coordinamento degli identificatori tra i team di progetto. Per un esempio completo della struttura degli eventi **EventSource** in Service Fabric, vedere l'evento **PartyCluster.ApplicationDeployService** nell'esempio Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

#### <a name="using-eventsource-generically"></a>Uso generico di EventSource

Poiché può essere difficile definire eventi specifici, molti utenti ne definiscono pochi con un set di parametri in comune che generalmente genera le informazioni sotto forma di stringa. Gran parte dell'aspetto strutturato viene persa, rendendo più difficili le ricerche e i filtri dei risultati. In questo approccio vengono definiti alcuni eventi, in genere corrispondenti ai livelli di registrazione. Il frammento seguente definisce un messaggio di debug e di errore:

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

Anche un ibrido di strumentazione strutturata e generica può essere una soluzione ideale. La strumentazione strutturata viene usata per la segnalazione degli errori e per le metriche. Gli eventi generici possono essere usati per la registrazione dettagliata utilizzata dai tecnici per la risoluzione dei problemi.

### <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

È importante pianificare con attenzione la strumentazione del codice. Il piano di strumentazione corretto può consentire di evitare la potenziale destabilizzazione della codebase e la conseguente necessità di ripetere la strumentazione del codice. Per ridurre il rischio, è possibile scegliere una libreria di strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), inclusa in Microsoft ASP.NET Core. ASP.NET Core ha un'interfaccia [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) che può essere usata con il provider preferito, riducendo al minimo l'effetto sul codice esistente. È possibile usare il codice in ASP.NET Core in Windows e Linux e in .NET Framework completo, in modo da standardizzare la strumentazione del codice.

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso di Microsoft.Extensions.Logging in Service Fabric

1. Aggiungere il pacchetto Microsoft.Extensions.Logging NuGet al progetto da strumentare. Aggiungere anche eventuali pacchetti del provider. Per un pacchetto di terze parti, vedere l'esempio seguente. Per altre informazioni, vedere [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Registrazione in ASP.NET Core).
2. Aggiungere una direttiva **using** per Microsoft.Extensions.Logging al file del servizio.
3. Definire una variabile privata all'interno della classe di servizio.

  ```csharp
  private ILogger _logger = null;

  ```
4. Nel costruttore della classe del servizio aggiungere il codice seguente.

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Avviare la strumentazione del codice nei metodi. Ecco alcuni esempi:

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

#### <a name="using-other-logging-providers"></a>Uso di altri provider di registrazione

Alcuni provider di terze parti usano l'approccio descritto nella sezione precedente, inclusi [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). È possibile inserirli tutti nella registrazione ASP.NET Core oppure usarli separatamente. Serilog offre una funzionalità che arricchisce tutti i messaggi inviati da un logger. Questa funzionalità può essere utile per restituire il nome del servizio, il tipo e le informazioni sulla partizione. Per usare questa funzionalità nell'infrastruttura di ASP.NET Core, seguire questa procedura:

1. Aggiungere i pacchetti NuGet Serilog, Serilog.Extensions.Logging e Serilog.Sinks.Observable al progetto. Per l'esempio successivo aggiungere anche Serilog.Sinks.Literate. Un approccio migliore viene illustrato più avanti in questo articolo.
2. In Serilog creare LoggerConfiguration e l'istanza del logger.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Aggiungere un argomento SeriLog.ILogger al costruttore del servizio e passare il logger appena creato.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. Nel costruttore del servizio aggiungere il codice seguente. Il codice crea enricher delle proprietà per le proprietà **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** del servizio. Aggiunge anche un enricher delle proprietà alla struttura di registrazione di ASP.NET Core, per consentire di usare Microsoft.Extensions.Logging.ILogger nel codice.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Instrumentare il codice come se si stesse usando ASP.NET Core senza Serilog.

  > [!NOTE]
  > È consigliabile non usare Log.Logger statico con l'esempio precedente. Service Fabric può ospitare più istanze dello stesso tipo di servizio in un singolo processo. Se si usa Log.Logger statico, l'ultimo writer degli enricher delle proprietà mostrerà i valori per tutte le istanze in esecuzione. Questo è un motivo per cui la variabile _logger è una variabile di membro privata della classe di servizio. È anche necessario rendere disponibile _logger al codice comune, che potrebbe essere usato nei servizi.

### <a name="choosing-a-logging-provider"></a>Scelta di un provider di registrazione

Se l'applicazione si basa sulle prestazioni elevate, **EventSource** è l'approccio ottimale. **EventSource** usa *in genere* un numero minore di risorse e offre prestazioni migliori rispetto alla registrazione di ASP.NET Core o di eventuali soluzioni di terze parti disponibili.  Non è un problema per molti servizi, ma se il servizio è orientato alle prestazioni **EventSource** si rivela la scelta più indicata. Per ottenere gli stessi vantaggi della registrazione strutturata, **EventSource** richiede un investimento notevole da parte del team di progettazione. Per determinare quale approccio usare per il progetto, creare un prototipo rapido degli scenari relativi a ogni opzione, quindi scegliere quello più appropriato per le esigenze specifiche.

## <a name="event-and-log-collection"></a>Raccolta di eventi e log

### <a name="azure-diagnostics"></a>Diagnostica Azure

Oltre alle informazioni fornite da Monitoraggio di Azure, Azure raccoglie eventi da ogni servizio in una posizione centrale. Per altre informazioni, vedere le procedure di configurazione della raccolta di eventi per [Windows](service-fabric-diagnostics-how-to-setup-wad.md) e [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Questi articoli illustrano la procedura di raccolta dei dati degli eventi e di invio dei dati all'Archiviazione di Azure. È possibile eseguire queste operazioni nel portale di Azure o nel modello di Azure Resource Manager abilitando la diagnostica. Diagnostica di Azure raccoglia alcune origini evento prodotte automaticamente da Service Fabric:

- Eventi **EventSource** e contatori di prestazioni, quando si usa il modello di programmazione Reliable Actor. Gli eventi vengono enumerati in [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Eventi **EventSource** quando si usa il modello di programmazione Reliable Services. Gli eventi vengono enumerati in [Funzionalità di diagnostica per Reliable Services con stato](service-fabric-reliable-services-diagnostics.md).
- Gli eventi di sistema vengono generati come eventi di Event Tracing for Windows (ETW). Molti eventi vengono generati da Service Fabric come parte di questa categoria, inclusi gli eventi di posizionamento ed eventi di avvio/arresto del servizio. Il modo migliore per visualizzare gli eventi generati è usare il [Visualizzatore eventi di diagnostica di Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) nel computer locale. Poiché questi eventi sono eventi ETW nativi, esistono alcune limitazioni per le relative modalità di raccolta.
- Dalla versione 5.4 di Service Fabric sono disponibili gli eventi di metrica di integrità e caricamento. È quindi possibile usare la raccolta di esempi per la creazione di report cronologici e per la creazione di avvisi. Questi eventi sono anche eventi ETW nativi e sono previste alcune limitazioni per le relative modalità di raccolta.

Dopo la configurazione, gli eventi vengono visualizzati in un account di archiviazione di Azure creato durante la creazione del cluster, presupponendo che sia stata abilitata la diagnostica. Le tabelle sono denominate WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable e WADServiceFabricSystemEventTable. Gli eventi di integrità non vengono aggiunti per impostazione predefinita. È necessario modificare il modello di Resource Manager per aggiungerli. Per altre informazioni, vedere [Raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Gli articoli elencati in questa sezione illustrano anche come ottenere eventi personalizzati in Archiviazione di Azure. Altri articoli su Diagnostica di Azure relativi alla configurazione dei contatori delle prestazioni oppure gli articoli che includono altre informazioni sul monitoraggio da macchine virtuali in Diagnostica di Azure sono applicabili anche a un cluster di Service Fabric. Se, ad esempio, non si vuole usare l'archiviazione tabelle di Azure Table come destinazione, vedere [Trasmettere i dati di Diagnostica di Azure nel percorso critico tramite Hub eventi](../event-hubs/event-hubs-streaming-azure-diags-data.md). Quando gli eventi si trovano in Hub eventi di Azure, è possibile leggerli e inviarli alla posizione scelta. Per altre informazioni sull'integrazione, vedere [Azure diagnostic information with Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) (Informazioni di Diagnostica di Azure con Application Insights).

Uno svantaggio dell'uso di Diagnostica di Azure consiste nella necessità di configurarlo usando il modello di Resource Manager. La diagnostica viene quindi eseguita solo a livello di set di scalabilità di macchine virtuali. Un set di scalabilità di macchine virtuali corrisponde a un tipo di nodo in Service Fabric. Configurare ogni tipo di nodo per tutte le applicazioni e tutti i servizi eseguibili su un nodo di quel tipo. Potrebbe trattarsi di un numero elevato di eventi **EventSource**, in base al numero di applicazioni e servizi configurati. È anche necessario distribuire Resource Manager a ogni modifica della configurazione di un'applicazione. Idealmente, la configurazione del monitoraggio va di pari passo con quella del servizio.

Diagnostica di Azure può essere usato solo per cluster di Service Fabric distribuiti in Azure. Funziona per cluster Windows e Linux.

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) può indirizzare gli eventi da un nodo a una o più destinazioni di monitoraggio. Poiché è incluso come pacchetto NuGet nel progetto del servizio, il codice e la configurazione di EventFlow vanno di pari passo con il servizio, eliminando i problemi di configurazione specifici di ogni nodo indicati in precedenza e relativi a Diagnostica di Azure. EventFlow viene eseguito nel processo del servizio e si connette direttamente agli output configurati. A causa della connessione diretta, EventFlow funziona con le distribuzioni dei servizi in Azure, nei contenitori e in locale. Se si esegue EventFlow in scenari a densità elevata, ad esempio in un contenitore, è necessario prestare attenzione perché ogni pipeline di EventFlow stabilisce una connessione esterna. Se si ospitano molti processi, si ottengono molte connessioni in uscita. Questo non costituisce un problema per le applicazioni di Service Fabric, perché tutte le repliche di `ServiceType` sono eseguite nello stesso processo e ciò limita il numero di connessioni in uscita. EventFlow offre anche i filtri per gli eventi, in modo che vengano inviati solo gli eventi corrispondenti al filtro specificato. Per informazioni dettagliate su come usare EventFlow con Service Fabric, vedere [Raccogliere log direttamente da un processo del servizio di Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

Per usare EventFlow:

1. Aggiungere il pacchetto NuGet al progetto di servizio.
2. Nella funzione **Main** del servizio creare la pipeline di EventFlow e quindi configurare gli output. Nell'esempio seguente viene usato Serilog come output.

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. Creare un file denominato eventFlowConfig.json nella cartella \\PackageRoot\\Config del servizio. All'interno del file la configurazione ha un aspetto simile a questo:

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    Nella configurazione vengono definiti due input: le due origini basate su **EventSource** create da Service Fabric ed **EventSource** per il servizio. Gli eventi di livello e di integrità del sistema che usano ETW non sono disponibili per EventFlow. Infatti, è necessario un privilegio elevato per ascoltare un'origine ETW e i servizi non devono mai essere eseguiti con privilegi elevati. L'altro input è Serilog. La configurazione di Serilog è stata eseguita nel metodo **Main**.  Vengono applicati alcuni filtri. Il primo filtro indica a EventFlow di rilasciare tutti gli eventi con un livello di evento dettagliato. Vengono configurati due output, ovvero l'output standard, che scrive nella finestra di output in Visual Studio, e ApplicationInsights. Assicurarsi di aggiungere la chiave di strumentazione.

4. Instrumentare il codice. Nell'esempio successivo viene instrumentato `RunAsync` in diversi modi, per visualizzare alcuni esempi. Nel codice seguente viene usato ancora Serilog. Alcuni elementi della sintassi usati sono specifici per Serilog. Annotare le funzionalità specifiche della soluzione di registrazione usata. Vengono generati tre eventi: un evento a livello di debug e due eventi informativi. Il secondo evento informativo registra la durata delle richieste. Nella configurazione di EventFlow illustrata in precedenza l'evento a livello di debug non deve essere trasmesso all'output.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Per visualizzare gli eventi in Azure Application Insights, nel portale di Azure passare alla risorsa Application Insights. Per visualizzare gli eventi, selezionare la casella **Cerca**.

![Visualizzazione di ricerca eventi in Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

È possibile visualizzare le tracce nella parte inferiore della schermata precedente. Vengono mostrati solo due eventi, perché l'evento a livello di debug è stato eliminato da EventFlow. La voce relativa alla richiesta precedente alla traccia è la terza riga di strumentazione di `_logger`. La riga indica che l'evento è stato convertito in una metrica di richiesta in Application Insights.

Nella definizione del filtro il tipo è **metadata**. Questo codice dichiara che un evento con proprietà `RequestName` con valore `MyRequest` e un'altra proprietà `Duration` contengono la durata della richiesta, in millisecondi. Questo è ciò che viene visualizzato nell'evento di richiesta in Application Insights. Lo stesso approccio funziona con tutti gli altri input di EventFlow supportati, incluso **EventSource**.

Se è presente un cluster autonomo che non può essere connesso a una soluzione basata sul cloud a causa dei criteri, è possibile usare Elasticsearch come output. È tuttavia possibile scrivere altri output e sono consigliate le richieste pull. Alcuni dei provider di terze parti per la registrazione di ASP.NET Core offrono anche soluzioni che supportano le installazioni locali.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Creazione di report su integrità e carico di Azure Service Fabric

Service Fabric offre un modello di integrità specifico, descritto in dettaglio in questi articoli:
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)
- [Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

Il monitoraggio dell'integrità è fondamentale per vari aspetti dell'uso di un servizio. Il monitoraggio dell'integrità è particolarmente importante quando Service Fabric esegue l'aggiornamento di un'applicazione denominata. Dopo l'aggiornamento di ogni dominio di aggiornamento del servizio, quando il dominio è disponibile per i clienti, è necessario che superi controlli di integrità prima che la distribuzione passi al dominio di aggiornamento successivo. Se non è possibile ottenere uno stato di integrità ottimale, viene eseguito il rollback della distribuzione, in modo che l'applicazione raggiunga uno stato positivo noto. Anche se è possibile che alcuni clienti subiscano interruzioni prima del rollback del servizio, la maggior parte dei clienti non riscontra problemi. La risoluzione è inoltre relativamente veloce, senza dovere attendere l'intervento di un tecnico. Con un numero maggiore di controlli di integrità inclusi nel codice, il servizio sarà più resistente alle problematiche di distribuzione.

Un altro aspetto dell'integrità del servizio è dato dalla creazione di report sulle metriche del servizio. Le metriche sono importanti in Service Fabric perché vengono usate per bilanciare l'uso delle risorse. Possono anche essere usate come un indicatore dell'integrità del sistema. È ad esempio possibile che sia presente un'applicazione con molti servizi e che ogni istanza segnali una metrica relativa alle richieste al secondo. Se uno dei servizi usa più risorse rispetto a un altro, Service Fabric sposta le istanze del servizio nel cluster, provando a mantenere un uso bilanciato delle risorse stesse. Per una spiegazione più dettagliata del funzionamento dell'utilizzo delle risorse, vedere [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).

Le metriche consentono anche di ottenere informazioni approfondite sulle prestazioni del servizio. Nel corso del tempo, è possibile usare le metriche per assicurarsi che il servizio stia operando entro i parametri previsti. Se, ad esempio, le tendenze indicano che alle 9:00 del lunedì mattina la media delle richieste al secondo è pari a 1.000, è possibile configurare un report di integrità che genera un avviso se le richieste al secondo sono inferiori a 500 o superiori a 1.500. È possibile che non si verifichino problemi, ma è comunque consigliabile controllare per assicurare la migliore esperienza possibile per i clienti. Il servizio può definire una serie di metriche di cui eseguire il report per l'integrità. Questo comunque non influirà sul bilanciamento delle risorse del cluster. Per ottenere questo risultato, impostare il peso delle metriche su zero. È consigliabile avviare tutte le metriche con un peso di zero e di non aumentare il peso fino a quando non si è sicuri di avere compreso l'impatto della ponderazione sul bilanciamento delle risorse nel cluster.

> [!TIP]
> Non usare un numero eccessivo di metriche ponderate. Potrebbe essere difficile comprendere i motivi dello spostamento delle istanze del servizio per il bilanciamento del carico. Anche un numero ridotto di metriche può essere molto utile.

Tutte le informazioni che possono indicare l'integrità e le prestazioni dell'applicazione possono essere usate per report relativi alle metriche e all'integrità. Un contatore delle prestazioni della CPU può indicare l'uso di un nodo ma non consente di capire se un servizio specifico sia integro, poiché su quel nodo potrebbero essere in esecuzione più servizi. Le metriche relative alle richieste al secondo, agli elementi elaborati e alla latenza delle richieste, tuttavia, possono indicare l'integrità di un servizio specifico.

Per segnalare l'integrità, usare codice analogo al seguente:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Per segnalare una metrica, usare codice analogo al seguente:

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>Watchdog

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. I watchdog sono una buona posizione in cui ospitare codice in grado di eseguire interventi correttivi per condizioni note senza bisogno di azioni da parte degli utenti.

## <a name="visualization-analysis-and-alerts"></a>Visualizzazione, analisi e avvisi

L'ultima parte del monitoraggio consiste nel visualizzare il flusso di eventi, creare report sulle prestazioni dei servizi e avvisare in caso di problemi. È possibile usare soluzioni diverse per questo aspetto del monitoraggio. È possibile usare Azure Application Insights e Operations Management Suite per creare avvisi basati sul flusso di eventi. È possibile usare Microsoft Power BI o una soluzione di terze parti come [Kibana](https://www.elastic.co/products/kibana) o [Splunk](https://www.splunk.com/) per visualizzare i dati.

## <a name="next-steps"></a>Passaggi successivi

* [Raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Raccogliere log direttamente da un processo del servizio di Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md)

