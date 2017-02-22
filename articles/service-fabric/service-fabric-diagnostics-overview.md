---
title: Panoramica di monitoraggio e diagnostica di Azure Service Fabric | Microsoft Docs
description: Informazioni su come monitorare e diagnosticare le applicazioni di Microsoft Azure Service Fabric in Azure, in sviluppo o in locale.
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/3/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: fdb15c3af4980e284e1a9effe434b1cab959d24c
ms.openlocfilehash: 826ae11e4826b37379caa53d85d8ec834a5ed0fb


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Monitorare e diagnosticare le applicazioni di Azure Service Fabric

Monitoraggio, rilevamento, diagnostica e risoluzione dei problemi permettono una continuità dei servizi con interruzioni minime all'esperienza utente, dando così accesso ad approfondimenti professionali, offrendo informazioni sull'uso delle risorse, rilevando problemi hardware, software e di prestazioni e diagnosticando potenziali problemi al comportamento dei servizi. L'efficacia delle operazioni di monitoraggio e diagnostica, essenziali in un ambiente di produzione distribuito reale, dipende tuttavia dall'adozione nella fase di sviluppo dei servizi di un modello analogo che ne assicuri il funzionamento anche nel mondo reale. Service Fabric consente agli sviluppatori di servizi di implementare facilmente un sistema di diagnostica in grado di operare senza problemi sia in ambienti di sviluppo costituiti da un unico computer locale sia in configurazioni con cluster di produzione veri e propri. 

Monitoraggio è un termine generico che comprende la strumentazione del codice, la raccolta dei log di strumentazione, l'analisi dei log stessi, la visualizzazione delle informazioni in base ai dati dei log, la generazione di avvisi in base ai valori e agli approfondimenti dei log, il monitoraggio dell'infrastruttura e l'autorizzazione per i tecnici a rilevare e diagnosticare problemi per i clienti. In questo articolo viene fornita una panoramica di monitoraggio per i cluster di Service Fabric ospitati in Azure o in locale, distribuiti in Windows o Linux con .NET. Innanzitutto è necessario dividere il problema in tre parti
- Strumentazione di codice o di infrastruttura
- Raccolta di eventi generati
- Archiviazione, aggregazione, visualizzazione e analisi

Sebbene esistano alcuni prodotti che coprono tutti e tre gli aspetti, molti clienti scelgono tecnologie diverse per ciascun ambito. È importante che queste tecnologie siano integrate tra loro per disporre di una soluzione di monitoraggio completa per l'applicazione. 

## <a name="monitoring-infrastructure"></a>Monitoraggio dell'infrastruttura

Service Fabric consente di mantenere un'applicazione in esecuzione in caso di errori di infrastruttura, ma gli utenti di tale applicazione devono capire se l'errore si verifica all'interno dell'applicazione o nell'infrastruttura sottostante. Il monitoraggio dell'infrastruttura è necessario anche per la pianificazione della capacità, per sapere cioè quando aggiungere o rimuovere infrastruttura. L'infrastruttura e l'applicazione che compongono una distribuzione di Service Fabric vanno entrambe monitorate, risolvendo eventuali problemi che dovessero verificarsi. Parte dell'infrastruttura può avere dei problemi, fintanto che l'applicazione rimane disponibile ai clienti.

### <a name="azure-monitoring"></a>Monitoraggio di Azure

Per i cluster distribuiti in Azure, [Azure Monitoring](../monitoring-and-diagnostics/toc.md) offre la possibilità di monitorare molte delle risorse di Azure in cui viene compilato un cluster di Service Fabric. Un set di metriche verrà automaticamente raccolto e visualizzato nel portale di Azure per il [set di scalabilità di macchine virtuali (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e le singole [macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines). Queste informazioni possono essere visualizzate nel portale di Azure selezionando il gruppo di risorse in cui è presente il cluster di Service Fabric e selezionando il set di scalabilità di macchine virtuali da visualizzare. Scegliere Metrica all'interno della sezione di navigazione Monitoraggio per visualizzare un grafico dei valori

![Visualizzazione del portale di Azure dei dati di metrica raccolti](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

I grafici possono essere personalizzati seguendo le istruzioni riportate nell'articolo [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). È possibile anche creare avvisi in base a queste metriche come descritto nell'articolo [Usare il portale di Azure per creare avvisi per i servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). È possibile inviare gli avvisi a un servizio di notifica tramite webhook come descritto nell'articolo [Configurare un webhook per un avviso di metrica di Azure(../monitoring-and-diagnostics/insights-webhooks-alerts.md). Monitoraggio di Azure supporta una singola sottoscrizione. Se è necessario supporto per più sottoscrizioni o se sono necessarie più funzionalità, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte di [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), offre una soluzione di gestione IT olistica per l'infrastruttura sia locale che basata sul cloud. I dati di Monitoraggio di Azure possono essere indirizzati direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) è il metodo consigliato per il monitoraggio dell'infrastruttura locale, ma è possibile usare qualsiasi soluzione esistente già in forza nell'azienda per il monitoraggio dell'infrastruttura.

### <a name="service-fabric-support-logs"></a>Log di supporto di Service Fabric

Se è necessario contattare il supporto Microsoft per assistenza con il cluster Azure Service Fabric, saranno quasi sempre richiesti i log di supporto. Se il cluster è ospitato in Azure, questi log vengono automaticamente configurati e raccolti in fase di creazione di un cluster. I log vengono archiviati in un account di archiviazione dedicato visualizzabile nel gruppo di risorse del cluster. Non esiste un nome predefinito per l'account di archiviazione. All'interno dell'account, tuttavia, sono presenti contenitori BLOB e tabelle con nomi che iniziano con "fabric". Se il cluster è autonomo, è necessario configurare la raccolta dei log seguendo le istruzioni in [Creare e gestire un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Per Service Fabric autonomi, i log devono essere inviati a una condivisione file locale. È **obbligatorio** disporre di questi log per ottenere assistenza. I log devono essere usati solo dal team di assistenza clienti Microsoft. Quasi sicuramente, questi log sono comunque di scarso interesse per l'utente.

## <a name="instrument-your-code"></a>Instrumentare il codice

La strumentazione del codice è la base di molti altri aspetti di monitoraggio dei servizi. Spesso è sorprendente scoprire quanto sia importante la strumentazione. Si tratta in realtà dell'unico modo per sapere se ci sono problemi e per capire cosa debba essere corretto. Sebbene sia tecnicamente possibile farlo, di solito non si collega un debugger a un servizio di produzione. Per questo motivo è importante disporre di dati di strumentazione dettagliati. Quando si crea questo volume di informazioni, trasferire tutti gli eventi nel nodo locale può essere oneroso. Molti servizi usano una strategia in due parti per gestire il volume dei dati di strumentazione:
* tutti gli eventi vengono mantenuti nel file di log in sequenza locale per un breve intervallo di giorni e vengono raccolti solo quando necessario per il debug. In genere gli eventi necessari per la diagnostica dettagliata vengono lasciati nel nodo in modo da ridurre i costi e l'utilizzo delle risorse
* Tutti gli eventi che indicano l'integrità dei servizi, ad esempio gli eventi di errore, heartbeat o di prestazioni, vengono inviati a un repository centrale in cui possono essere usati per generare gli avvisi di servizio non integro.

Esistono soluzioni che eseguono automaticamente la strumentazione del codice. Benché questi prodotti siano efficaci, è quasi sempre necessaria una strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. Nelle sezioni successive vengono descritti diversi approcci per la strumentazione del codice e quando è necessario sceglierne uno rispetto a un altro.

### <a name="eventsource"></a>EventSource

Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe EventSource derivata (*ServiceEventSource* o *ActorEventSource*). In questo modo si dispone di un modello a cui è possibile aggiungere eventi aggiuntivi appropriati in base all'applicazione o al servizio. Il nome di EventSource **deve** essere univoco e deve essere rinominato dalla stringa di partenza "MyCompany-&lt;solution&gt;-&lt;project&gt;". Se esistono più definizioni di EventSource con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Spesso gli intervalli di valori sono pre-assegnati per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni su EventSource consultare il [blog di Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentazione MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Uso di eventi EventSource strutturati

Ognuno dei seguenti eventi è definito per un caso specifico, ad esempio è stato registrato un tipo di servizio. I messaggi definiti in questo modo consentono di includere i dati con il testo dell'errore. Così, è possibile eseguire ricerche e usare filtri più efficaci in base ai nomi o ai valori delle proprietà specificate. Strutturando l'output della strumentazione è possibile usarlo con più facilità, ma ciò richiede sforzi maggiori per definire un nuovo evento per ciascun caso d'uso. Alcune definizioni di evento possono essere condivise nell'intera applicazione, ad esempio un metodo di avvio o arresto può essere riusato in più servizi. Un servizio specifico per un dominio, ad esempio un sistema di ordine, può disporre di un evento CreateOrder, che avrà un proprio evento univoco. Spesso questo approccio genera un numero elevato di eventi e può potenzialmente richiedere il coordinamento degli identificatori tra i team di progetto. Per un esempio completo degli EventSources di struttura in Service Fabric esaminare PartyCluster.ApplicationDeployService all'interno dell'esempio Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>Uso generico di EventSource

Poiché può essere difficile definire eventi specifici, molti utenti ne definiscono pochi con un set di parametri in comune che generalmente genera le informazioni sotto forma di stringa. Gran parte dell'aspetto strutturato viene persa, rendendo più difficili le ricerche e i filtri dei risultati. Con questo approccio vengono definiti alcuni eventi, in genere corrispondenti ai livelli di registrazione. Il seguente frammento definisce un messaggio di debug e di errore.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
Anche un approccio ibrido alla strumentazione strutturata e generica può essere una soluzione ideale. La strumentazione strutturata può essere usata per creare report di errori e metriche, mentre è possibile usare gli eventi generici per la registrazione in dettaglio che verrà poi analizzata dai tecnici per la risoluzione dei problemi.

### <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

Scegliere come instrumentare il codice può essere difficile. Una scelta non appropriata potrebbe forzare una nuova strumentazione ed esiste il rischio potenziale di destabilizzare e modificare la base del codice. Per ridurre il rischio, gli sviluppatori possono scegliere una libreria strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), fornita da ASP.NET Core. La libreria presenta un'interfaccia [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) che consente di usare il provider di propria scelta, riducendo al minimo l'impatto sul codice esistente. Un altro aspetto interessante è che il codice può essere usato non solo in .NET Core in Windows e Linux, ma anche nell'intera struttura, dando così all'utente la possibilità di standardizzare il codice di strumentazione in .NET e .NET Core.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Procedura: usare Microsoft.Extensions.Logging all'interno di Service Fabric

1. Aggiungere il pacchetto NuGet **Microsoft.Extensions.Logging** al progetto da instrumentare. Aggiungere anche eventuali pacchetti provider. L'operazione è descritta in seguito per un pacchetto di terze parti. Vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) per altre informazioni
2. Aggiungere una direttiva di uso per "Microsoft.Extensions.Logging"' al file del servizio
3. Definire una variabile privata all'interno della classe di servizio
```csharp
        private ILogger _logger = null;
```
4. Nel costruttore della classe del servizio, aggiungere
```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
```
5. Avviare la strumentazione del codice nei metodi. Di seguito vengono forniti alcuni esempi
```csharp
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
```

#### <a name="using-other-logging-providers"></a>Uso di altri provider di registrazione

Esistono alcuni provider di terze parti che funzionano con questo approccio: [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) e [loggr](https://github.com/imobile3/Loggr.Extensions.Logging) sono tre esempi. Ognuno di questi provider può essere inserito nella registrazione di ASP.Net Core, ma può anche essere usato singolarmente. SeriLog dispone di una funzionalità che consente l'arricchimento di tutti i messaggi inviati da un logger, cosa che può rivelarsi utile per l'output del nome e del tipo di servizio e dei dati sulla partizione. Per usare questa funzionalità con l'infrastruttura di ASP.NET Core, eseguire le operazioni seguenti

1. Aggiungere i pacchetti NuGet **Serilog**, **Serilog.Extensions.Logging** e **Serilog.Sinks.Observable** al progetto. Aggiungere inoltre **SeriLog.Sinks.Literate** per questo esempio, in seguito viene descritto un approccio migliore
2. Creare LoggerConfiguration e l'istanza di logger in SeriLog
```csharp
    Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
```
3. Aggiungere un argomento SeriLog.ILogger al costruttore del servizio e passare il logger appena creato
```csharp
    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
```
4. Nel costruttore del servizio, aggiungere quanto segue per creare il necessario per arricchire le proprietà ServiceTypeName, ServiceName, PartitionId e InstanceId del servizio. L'operazione aggiunge inoltre quanto detto all'impianto di registrazione di ASp.NET Core. Sarà così possibile usare Microsoft.Extensions.Logging.ILogger nel proprio codice.
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
5. Instrumentare il codice allo stesso modo di quando si usa ASP.NET Core senza SeriLog.

> [!NOTE] 
> Non è consigliabile usare Log.Logger statico con questo approccio poiché Service Fabric può ospitare più istanze dello stesso tipo di servizio all'interno di un singolo processo. Ciò significa che l'ultimo writer dei valori che arricchiscono le proprietà disporrà di valori visualizzabili per tutte le istanze in esecuzione. Questo è un motivo per cui la variabile _logger è una variabile di membro privata della classe di servizio. Significa anche che _logger deve essere reso disponibile al codice comune che potrebbe essere usato in tutti i servizi. 

### <a name="which-one-should-i-use"></a>Quale è più indicato usare?

Se le prestazioni sono fondamentali per l'applicazione, EventSource è l'approccio migliore da usare in quanto **di solito** usa meno risorse e ha prestazioni migliori rispetto alla registrazione di ASP.NET Core o delle soluzioni di terze parti.  Non è un problema per molti servizi, ma se il servizio è orientata alle prestazioni EventSource si rivela la scelta più indicata. Per ottenere gli stessi vantaggi della registrazione strutturata, EventSource richiede un investimento notevole da parte del team di progettazione. Il modo migliore per determinare la scelta ideale per i progetti è eseguire un prototipo rapido delle operazioni da eseguire per ciascun approccio e quindi scegliere quello più adatto alle proprie esigenze.

## <a name="event-and-log-collection"></a>Raccolta di eventi e log

### <a name="azure-diagnostics"></a>Diagnostica Azure

Oltre alle funzionalità di Monitoraggio di Azure, Azure consente anche di raccogliere eventi da ciascun servizio in un percorso locale. Esistono due articoli che illustrano come configurare la raccolta di eventi per [Windows](service-fabric-diagnostics-how-to-setup-wad.md) e [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Questi articoli mostrano la raccolta e l'invio all'archiviazione di Azure dei dati degli eventi. È possibile farlo con facilità nel portale o nel modello di Resource Manager abilitando la diagnostica. Attivando questa caratteristica è possibile raccogliere alcune origini di evento prodotte automaticamente da Service Fabric:

- Eventi EventSource e contatori delle prestazioni, quando si usa il modello di programmazione Reliable Actors. Gli eventi vengono enumerati nell'articolo [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
- Eventi EventSource quando si usa il modello di programmazione Reliable Services. Gli eventi vengono enumerati nell'articolo [Funzionalità di diagnostica per Reliable Services con stato](service-fabric-reliable-services-diagnostics.md)
- Gli eventi di sistema vengono inviati come eventi ETW. Esistono numerosi eventi generati da Service Fabric come parte di questa categoria. Includono eventi di posizionamento, avvio e arresto dei servizi. Il modo migliore per visualizzare gli eventi generati è usare il [visualizzatore diagnostico di Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) durante l'esecuzione nel computer locale. Poiché questi eventi sono eventi ETW nativi, esistono alcune limitazioni per le relative modalità di raccolta
- Dalla versione 5.4 di Service Fabric sono disponibili gli eventi di metrica di integrità e caricamento. In questo modo è possibile raccogliere questi eventi e usarli per i report e gli avvisi cronologici. Anche questi eventi sono eventi ETW nativi e dispongono quindi di alcune limitazioni per le relative modalità di raccolta

Quando vengono configurati, questi eventi sono inseriti in uno degli account di archiviazione di Azure creati insieme al cluster, se la diagnostica è abilitata. Le tabelle sono denominate *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable* e *WADServiceFabricSystemEventTable*. Gli eventi di integrità non vengono aggiunti per impostazione predefinita e richiedono una modifica al modello di Resource Manager. Vedere [Raccogliere log tramite la diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md) per informazioni dettagliate.

Questi articoli illustrano inoltre come ottenere eventi personalizzati nell'archiviazione di Azure. Tutti gli articoli esistenti sulla diagnostica di Azure che spiegano come configurare i contatori delle prestazioni o indicano altre informazioni di monitoraggio da una macchina virtuale alla diagnostica di Azure sono applicabili anche a un cluster id Service Fabric. Ad esempio, se l'archivio tabelle di Azure non è la destinazione desiderata, è presente un articolo su come [Trasmettere i dati di Diagnostica di Azure nel percorso critico tramite Hub eventi](../event-hubs/event-hubs-streaming-azure-diags-data.md). Una volta che gli eventi sono inseriti in Hub eventi, possono essere letti e inviati al percorso scelto. È inoltre disponibile un articolo relativo all'integrazione delle [Informazioni di diagnostica di Azure con Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)

Uno degli svantaggi dell'uso della diagnostica di Azure consiste nel fatto che la configurazione viene eseguita tramite un modello di Resource Manager e ha quindi luogo solo nel livello del set di scalabilità della macchina virtuale. Un set di scalabilità della macchina virtuale corrisponde a un tipo di nodo in Service Fabric. Ciò significa che è necessario configurare ciascun tipo di nodo per tutte le applicazioni e tutti i servizi eseguibili su un nodo di quel tipo. Potrebbe trattarsi di un numero elevato di EventSource a seconda del numero di applicazioni e servizi configurati. Inoltre, è necessario eseguire una distribuzione di Resource Manager ogni volta che viene modificata la configurazione di un'applicazione. Idealmente, la configurazione del monitoraggio va di pari passo con quella del servizio.

La diagnostica di Azure funziona solo per i cluster di Service Fabric distribuiti in Azure, ma funziona sia per i cluster Windows che per i cluster Linux.

### <a name="eventflow"></a>EventFlow

[EventFlow è stato rilasciato dal team di Visual Studio](service-fabric-diagnostic-collect-logs-without-an-agent.md) e offre un meccanismo per il routing degli eventi da un nodo a una o più destinazioni di monitoraggio. Poiché è incluso come pacchetto NuGet nel progetto del servizio, il codice e la configurazione di EventFlow vanno di pari passo con quest'ultimo, eliminando i problemi di configurazione per nodo relativi alla diagnostica di Azure. EventFlow viene eseguito all'interno del processo di servizio e si connette direttamente agli output configurati. A causa di questa connessione diretta, EventFlow funziona con le distribuzioni di servizi in Azure, contenitori o in locale. Prestare attenzione durante l'esecuzione in scenari ad alta densità, ad esempio un contenitore, in quanto ogni pipeline di EventFlow stabilisce una connessione esterna. Se si ospita un numero elevato di processi, si avranno numerose connessioni in uscita. Quanto appena detto non è un problema per le applicazioni di Service Fabric, in quanto tutte le repliche di un ServiceType sono eseguite nello stesso processo, limitando così il numero di connessioni in uscita. EventFlow offre inoltre il filtraggio degli eventi, in modo che vengano inviati solo gli eventi corrispondenti al filtro specificato. Per altre informazioni sull'uso di EventFlow con Service Fabric, vedere [Raccogliere log direttamente da un processo del servizio di Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)

> [!NOTE]
> In una versione futura di Service Fabric, verrà resa disponibile un'applicazione host EventSource che consentirà l'ascolto degli input basati su ETW, la raccolta delle metriche a livello di nodo e il supporto per i file di log in sequenza.

Usare EventFlow è molto semplice
1. Aggiungere il pacchetto NuGet al progetto di servizio
2. All'interno della funzione **Main** del servizio creare la pipeline di EventFlow e configurare gli output. In questo caso viene usato SeriLog come output
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
                    IObserver<LogEvent> serilogInput = pipeline.Inputs.OfType<SerilogInput>().First();
                    Log.Logger = new LoggerConfiguration().WriteTo.Observers(events => events.Subscribe(serilogInput)).CreateLogger();

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
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
3. Creare un file denominato *eventFlowConfig.json* nella cartella PackageRoot | Config del servizio. All'interno del file la configurazione ha un aspetto simile
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
Nella configurazione sono definiti due input, le due origini basate su EventSource create da Service Fabric ed EventSource per il servizio. Si noti che gli eventi di livello e di integrità del sistema che usano ETW non sono disponibili per EventFlow. Infatti, è necessario un privilegio elevato per ascoltare un'origine ETW e i servizi non devono mai essere eseguiti con privilegi elevati. L'altro input è SeriLog, configurato nel metodo **Main**.  Sono inoltre applicati alcuni filtri, il primo indica a EventFlow di eliminare tutti gli eventi di livello eventi dettagliato. L'altro filtro verrà definito più avanti nell'articolo. Sono anche configurati due output: l'output standard, che scrive nella finestra di output in Visual Studio, e ApplicationInsights. Assicurarsi di aggiungere la chiave di strumentazione.

4. L'ultimo passaggio consiste nella strumentazione del codice. In questo esempio, viene eseguita la strumentazione di RunAsync in alcuni modi diversi. Nel codice riportato di seguito viene ancora usato SeriLog, parte della sintassi usata è specifica per questo strumento. Tenere presenti le funzionalità specifiche della soluzione di registrazione usata. Vengono generati tre eventi, un evento di livello di debug e due eventi informativi, il secondo dei quali tiene traccia della durata della richiesta. Con la configurazione di EventFlow precedente, l'evento di livello di debug non dovrebbe essere trasmesso all'output.

```csharp
    Stopwatch sw = Stopwatch.StartNew();

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        sw.Restart();

        // Delay a random interval to provide a more interesting request duration.
        await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
    }
```

Per visualizzare gli eventi in Application Insights, aprire il portale di Azure e passare alla risorsa ApplicationInsights. Quindi fare clic su "Ricerca" in alto a sinistra per visualizzare gli eventi.

![Visualizzazione di ricerca eventi in Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Le tracce si trovano nella parte inferiore dell'immagine. Sono presenti solo due eventi, in quanto l'evento di livello di debug è stato eliminato da EventFlow. Che cos'è, quindi, la voce di richiesta sopra alla traccia? Si tratta della terza riga di strumentazione "_logger" che indica che l'evento è stato convertito in metrica di richiesta all'interno di Application Insights. Di nuovo nella definizione del filtro, il tipo è "metadata". In questo modo, si dichiara che l'evento dispone di una proprietà di "RequestName" con il valore "MyRequest" e che un'altra proprietà, "Duration", contiene la durata della richiesta in millisecondi. Questo è ciò che viene visualizzato nell'evento di richiesta in Application Insights. Lo stesso approccio funziona con tutti gli altri input di EventFlow supportati, incluso EventSource.

Se il cluster è autonomo e non può essere connesso a una soluzione basata su cloud per ragioni di prassi aziendali, EventFlow supporta ElasticSearch come output, ma è consigliabile scrivere altri output ed eseguire richieste di pull. Alcuni dei provider di terze parti per la registrazione di ASP.NET Core dispongono inoltre di soluzioni che supportano le installazioni locali.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Creazione di report su integrità e carico di Azure Service Fabric

Service Fabric dispone di un proprio modello di integrità descritto in dettaglio in alcuni articoli
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)
- [Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

Il monitoraggio dell'integrità è fondamentale per vari aspetti dell'uso di un servizio. In particolar modo quando Service Fabric esegue l'aggiornamento di un'applicazione denominata: dopo l'aggiornamento di ciascun dominio di aggiornamento del servizio che viene reso disponibile ai clienti, è necessario che i controlli di integrità abbiano esito positivo prima di passare al dominio di aggiornamento successivo. Se non si riscontra una buona integrità, la distribuzione subisce un rollback, lasciando l'applicazione in uno stato positivo conosciuto. Alcuni clienti hanno subito delle interruzioni prima del rollback dei servizi, ma la maggior parte non ha riscontrato problemi. Inoltre, la risoluzione ha avuto luogo con relativa rapidità, senza dover attendere l'intervento di un tecnico. Con un numero maggiore di controlli di integrità inclusi nel codice, il servizio sarà più resistente alle problematiche di distribuzione.

Un altro aspetto dell'integrità del servizio è dato dalla creazione di report sulle metriche del servizio. Le metriche sono importanti in Service Fabric perché vengono usate per bilanciare l'uso delle risorse. Possono inoltre essere usate come un indicatore dell'integrità del sistema. Si supponga che l'applicazione contenga parecchi servizi e che ciascuna istanza riporti una metrica RPS (richieste al secondo). Se uno dei servizi usa più risorse rispetto a un altro, Service Fabric sposta le istanze del servizio nel cluster, tentanto di mantenere un uso bilanciato delle risorse stesse. L'articolo [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md) fornisce una spiegazione dettagliata del funzionamento.

Le metriche offrono inoltre informazioni dettagliate sulle prestazioni del servizio e possono essere usate nel tempo per controllare l'effettivo funzionamento di quest'ultimo all'interno dei parametri previsti. Ad esempio, se in base alle tendenze le richieste al secondo medie alle 9:00 di lunedì mattina sono pari a 1000, è possibile impostare un report di integrità che avvisa in caso le richieste scendano sotto le 500 o superino le 1500. Potrebbero non esserci problemi, ma è comunque consigliabile controllare per assicurare la migliore esperienza possibile per i clienti. Il servizio può definire una serie di metriche di cui eseguire il report per l'integrità. Questo comunque non influirà sul bilanciamento delle risorse del cluster. Impostare su zero il peso delle metriche. Si consiglia di avviare tutte le metriche con un peso di zero e di non aumentare il peso fino a quando non si è sicuri di averne compreso l'impatto sul bilanciamento delle risorse nel cluster.

> [!TIP]
> Assicurarsi di non per avere troppi metriche ponderate. Può essere difficile capire perché le istanze di servizio vengono spostate, per questo motivo è utile disporre di alcune metriche mirate.

Le metriche e i report di integrità ideali includono quelli che indicano l'integrità e le prestazioni dell'applicazione. Un contatore delle prestazioni della CPU può indicare l'uso di un nodo ma non consente di capire veramente se un servizio specifico sia integro, poiché su quel nodo potrebbero essere in esecuzione più servizi. D'altra parte, metriche come le richieste al secondo, gli elementi elaborati o la latenza delle richieste possono tutte indicare l'integrità di un servizio specifico.

Per segnalare l'integrità, aggiungere un codice come

```csharp
 if (!result.HasValue)
 {
     HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
     this.Partition.ReportInstanceHealth(healthInformation);
 }
```

Per segnalare una metrica, aggiungere al servizio un codice come quello seguente

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Watchdog

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. I watchdog sono una buona posizione in cui ospitare codice in grado di eseguire interventi correttivi per condizioni note senza bisogno di azioni da parte degli utenti.

## <a name="visualization-analysis-and-alerting"></a>Visualizzazione, analisi e avvisi

L'ultima parte del monitoraggio consiste nel visualizzare il flusso di eventi, creare report sulle prestazioni dei servizi e avvisare in caso di problemi. Esistono numerose soluzioni usate per questo aspetto del monitoraggio. È possibile usare Application Insights e OMS per inviare avvisi in base al flusso degli eventi. È possibile usare PowerBI o una soluzione di terze parti come [Kibana](https://www.elastic.co/products/kibana) o [Splunk](https://www.splunk.com/) per visualizzare i dati.

## <a name="next-steps"></a>Passaggi successivi

* [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Raccogliere log direttamente da un processo del servizio di Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO1-->


