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
ms.date: 05/10/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 9c1a3bb6de8756c37903e5f1b9dcf3fdc1ef6a11
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="monitoring-and-diagnostics-in-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric

Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione negli ambienti di produzione. Le soluzioni Service Fabric offrono i risultati migliori quando si implementano il monitoraggio e la diagnostica fin dall'inizio, per assicurare il perfetto funzionamento dei servizi negli ambienti di sviluppo locali e in configurazioni dei cluster di produzione reali.

Gli obiettivi principali di monitoraggio e diagnostica sono:
* Rilevare e diagnosticare i problemi di hardware e infrastruttura
* Rilevare i problemi di software e app, ridurre i tempi di inattività del servizio
* Comprendere l'utilizzo delle risorse e supportare le decisioni operative
* Ottimizzare le prestazioni delle applicazioni, dei servizi e delle infrastrutture
* Generare informazioni aziendali dettagliate e identificare le aree di miglioramento


Il monitoraggio e la diagnostica sono importanti per garantire che tutto funzioni come previsto e consentono di rispondere alle situazioni con un'interruzione minima del servizio. 

Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi: 
1. **Generazione di eventi**: sono inclusi eventi (log, tracce, eventi personalizzati) sia a livello di infrastruttura (cluster) che a livello di applicazione/servizio 
2. **Aggregazione di eventi**: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati. A tale scopo, è possibile usare le tabelle di archiviazione configurate da Diagnostica di Azure oppure creare una pipeline EventFlow
3. **Analisi**: gli eventi devono essere visualizzati e devono essere accessibili in un formato, per consentirne l'analisi e la visualizzazione in base alle esigenze specifiche. È possibile scegliere di usare strumenti come Application Insights, Operations Management Suite, Kibana e molti altri

Anche se sono disponibili molti prodotti che coprono tutte e tre le aree, molti clienti scelgono tecnologie diverse per ognuno degli aspetti. Per disporre di una soluzione di monitoraggio end-to-end per l'applicazione, è importante che queste tecnologie funzionino in combinazione.

A seconda delle esigenze, è possibile estendere ulteriormente la diagnostica e il monitoraggio per includere avvisi automatizzati e mitigazione, funzionalità spesso integrate negli strumenti di analisi che è possibile scegliere di usare. 

## <a name="event-generation"></a>Generazione di eventi

Eventi, log e tracce possono essere generati dal livello dell'infrastruttura (elementi del cluster, computer o azioni di Service Fabric) o dal livello dell'applicazione (qualsiasi strumentazione aggiunta alle app e ai servizi distribuiti nel cluster).

### <a name="infrastructure-monitoring-the-cluster"></a>Infrastruttura: monitoraggio del cluster

Service Fabric consente di mantenere un'applicazione in esecuzione in caso di errori dell'infrastruttura, ma è comunque necessario capire se un errore si verifica nell'applicazione o nell'infrastruttura sottostante. È anche necessario monitorare l'infrastruttura per la pianificazione della capacità, in modo da sapere quando è necessario aggiungere o rimuovere infrastruttura. È importante monitorare e risolvere i problemi dell'infrastruttura e dell'applicazione che compongono una distribuzione di Service Fabric. Anche se un'applicazione è disponibile per i clienti, l'infrastruttura potrebbe provocare ancora problemi. Per assicurarsi che il cluster funzioni come previsto, Service Fabric fornisce cinque diversi canali di registrazione predefiniti:

1. Canale operativo: operazioni generali eseguite da Service Fabric e dal cluster, inclusi eventi per un nodo, una nuova applicazione distribuita, il ripristino dello stato precedente a un aggiornamento di Service Fabric e così via. 
2. Canale di informazioni per i clienti: report di integrità e decisioni di bilanciamento del carico
3. Eventi di Reliable Services: eventi specifici del modello di programmazione
4. Eventi di Reliable Actors: eventi specifici del modello di programmazione e contatori delle prestazioni
5. Log di supporto: log di sistema generati da Service Fabric solo per l'uso da parte di Microsoft per fornire supporto

È consigliabile abilitare il servizio "Diagnostica" al momento della creazione del cluster. Questa operazione può essere eseguita nel portale, come illustrato di seguito, o mediante un modello di Azure Resource Manager che include la diagnostica. 

![Creazione di un cluster dal portale di Azure con la diagnostica abilitata](./media/service-fabric-diagnostics-overview/azure-enable-diagnostics.png)

Come illustrato sopra, c'è anche un campo facoltativo per aggiungere una chiave di strumentazione di Application Insights (AppInsights). Se si sceglie di usare AppInsights per l'analisi degli eventi (AppInsights è una delle soluzioni consigliate), includere qui la risorsa instrumentationKey (GUID) di AppInsights.

#### <a name="service-fabric-support-logs"></a>Log di supporto di Service Fabric

Se è necessario contattare il supporto Microsoft per assistenza con il cluster di Azure Service Fabric, saranno quasi sempre richiesti i log di supporto. Se il cluster è ospitato in Azure, questi log vengono automaticamente configurati e raccolti in fase di creazione di un cluster. I log vengono archiviati in un account di archiviazione dedicato nel gruppo di risorse del cluster. L'account di archiviazione non ha un nome predefinito, ma nell'account vengono visualizzati contenitori BLOB e tabelle con nomi che iniziano con *fabric*. Per informazioni sulla configurazione di raccolte di log per un cluster autonomo, vedere [Creare un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Per istanze autonome di Service Fabric, i log devono essere inviati a una condivisione file locale. È **obbligatorio** disporre di questi log per ottenere assistenza. I log devono essere usati solo dal team di assistenza clienti Microsoft.

#### <a name="azure-service-fabric-health-and-load-reporting"></a>Creazione di report su integrità e carico di Azure Service Fabric

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


### <a name="application-instrumenting-code-for-custom-events"></a>Applicazione: strumentazione del codice per eventi personalizzati

La strumentazione del codice è la base di molti altri aspetti di monitoraggio dei servizi. La strumentazione è l'unico modo per rilevare eventuali problemi e individuare le correzioni necessarie. Anche se è tecnicamente possibile connettere un debugger a un servizio di produzione, questa non è una procedura comune. I dati dettagliati di strumentazione sono quindi importanti. 

Alcuni prodotti instrumentano automaticamente il codice. Benché queste soluzioni siano efficaci, è quasi sempre necessaria una strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. Le sezioni successive illustrano diversi approcci per la strumentazione del codice e indicano quando è consigliabile scegliere un approccio rispetto a un altro.

#### <a name="eventsource"></a>EventSource

Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe derivata da **EventSource** (**ServiceEventSource** o **ActorEventSource**). Viene creato un modello in cui è possibile aggiungere eventi per l'applicazione o il servizio. Il nome di **EventSource** **deve** essere univoco e deve essere rinominato dalla stringa del modello predefinito MyCompany-&lt;soluzione&gt;-&lt;progetto&gt;. Se esistono più definizioni di **EventSource** con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Alcune organizzazioni preassegnano intervalli di valori per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni, vedere il [blog di Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentazione di MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

##### <a name="using-structured-eventsource-events"></a>Uso di eventi EventSource strutturati

Ogni evento negli esempi di codice di questa sezione è definito per un caso specifico, ad esempio, quando viene registrato un tipo di servizio. Quando si definiscono i messaggi in base al caso di utilizzo, è possibile creare pacchetti dei dati e con il testo dell'errore, quindi eseguire ricerche e applicare filtri con maggiore facilità in base ai nomi o ai valori delle proprietà specificate. Strutturando l'output della strumentazione è possibile usarlo con più facilità, ma ciò richiede sforzi maggiori per definire un nuovo evento per ciascun caso d'uso. Alcune definizioni di evento possono essere condivise nell'intera applicazione. Un evento di avvio o arresto di un metodo, ad esempio, può essere riutilizzato in molti servizi in un'applicazione. Un servizio specifico per un dominio, ad esempio un sistema di ordine, può avere un evento **CreateOrder**, che ha un proprio evento univoco. Questo approccio può generare un numero elevato di eventi e può potenzialmente richiedere il coordinamento degli identificatori tra i team di progetto. 

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

##### <a name="using-eventsource-generically"></a>Uso generico di EventSource

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

#### <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

È importante pianificare con attenzione la strumentazione del codice. Il piano di strumentazione corretto può consentire di evitare la potenziale destabilizzazione della codebase e la conseguente necessità di ripetere la strumentazione del codice. Per ridurre il rischio, è possibile scegliere una libreria di strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), inclusa in Microsoft ASP.NET Core. ASP.NET Core ha un'interfaccia [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) che può essere usata con il provider preferito, riducendo al minimo l'effetto sul codice esistente. È possibile usare il codice in ASP.NET Core in Windows e Linux e in .NET Framework completo, in modo da standardizzare la strumentazione del codice.

##### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso di Microsoft.Extensions.Logging in Service Fabric

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

#### <a name="choosing-a-logging-provider"></a>Scelta di un provider di registrazione

Se l'applicazione si basa sulle prestazioni elevate, **EventSource** è l'approccio ottimale. **EventSource** usa *in genere* un numero minore di risorse e offre prestazioni migliori rispetto alla registrazione di ASP.NET Core o di eventuali soluzioni di terze parti disponibili.  Non è un problema per molti servizi, ma se il servizio è orientato alle prestazioni **EventSource** si rivela la scelta più indicata. Per ottenere gli stessi vantaggi della registrazione strutturata, **EventSource** richiede un investimento maggiore da parte del team tecnico. Per determinare quale approccio usare per il progetto, creare un prototipo rapido degli scenari relativi a ogni opzione, quindi scegliere quello più appropriato per le esigenze specifiche.

## <a name="event-aggregation-and-collection"></a>Aggregazione e raccolta di eventi

### <a name="azure-diagnostics"></a>Diagnostica Azure

Oltre alle informazioni fornite da Monitoraggio di Azure, Azure raccoglie eventi da ogni servizio in una posizione centrale. Per altre informazioni, vedere le procedure di configurazione della raccolta di eventi per [Windows](service-fabric-diagnostics-how-to-setup-wad.md) e [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Questi articoli illustrano la procedura di raccolta dei dati degli eventi e di invio dei dati all'Archiviazione di Azure. È possibile eseguire queste operazioni nel portale di Azure o nel modello di Azure Resource Manager abilitando la diagnostica. Diagnostica di Azure raccoglia alcune origini evento prodotte automaticamente da Service Fabric:

- Eventi **ETW** emessi come parte del canale operativo
- Eventi **EventSource** e contatori di prestazioni, quando si usa il modello di programmazione Reliable Actor. Gli eventi vengono enumerati in [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Eventi **EventSource** quando si usa il modello di programmazione Reliable Services. Gli eventi vengono enumerati in [Funzionalità di diagnostica per Reliable Services con stato](service-fabric-reliable-services-diagnostics.md).


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

![Visualizzazione di ricerca eventi in Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

È possibile visualizzare le tracce nella parte inferiore della schermata precedente. Vengono mostrati solo due eventi, perché l'evento a livello di debug è stato eliminato da EventFlow. La voce relativa alla richiesta precedente alla traccia è la terza riga di strumentazione di `_logger`. La riga indica che l'evento è stato convertito in una metrica di richiesta in Application Insights.

Nella definizione del filtro il tipo è **metadata**. Questo codice dichiara che un evento con proprietà `RequestName` con valore `MyRequest` e un'altra proprietà `Duration` contengono la durata della richiesta, in millisecondi. Questo è ciò che viene visualizzato nell'evento di richiesta in Application Insights. Lo stesso approccio funziona con tutti gli altri input di EventFlow supportati, incluso **EventSource**.

Se è presente un cluster autonomo che non può essere connesso a una soluzione basata sul cloud a causa dei criteri, è possibile usare Elasticsearch come output. È tuttavia possibile scrivere altri output e sono consigliate le richieste pull. Alcuni dei provider di terze parti per la registrazione di ASP.NET Core offrono anche soluzioni che supportano le installazioni locali.

## <a name="visualization-analysis-and-alerts"></a>Visualizzazione, analisi e avvisi

L'ultima parte del monitoraggio consiste nel visualizzare il flusso di eventi, creare report sulle prestazioni dei servizi e avvisare in caso di problemi. È possibile usare soluzioni diverse per questo aspetto del monitoraggio. È possibile usare AppInsights e Operations Management Suite (OMS) per creare avvisi basati sul flusso di eventi. È possibile usare Microsoft Power BI o una soluzione di terze parti come [Kibana](https://www.elastic.co/products/kibana) o [Splunk](https://www.splunk.com/) per visualizzare i dati.

### <a name="appinsights"></a>AppInsights

AppInsights è uno degli strumenti consigliati per il monitoraggio di applicazioni e servizi. La versione aggiornata di AI.SDK funziona molto bene con gli eventi di Service Fabric e, oltre a fornire buoni strumenti di visualizzazione dei dati e query (tramite AppInsights Analytics), consente anche di creare un oggetto AppMap accurato, utile per tenere traccia delle dipendenze tra i processi in un'applicazione o un cluster.

Configurare una risorsa AppInsights cercando"Application Insights" in Azure Marketplace. Dopo la creazione, passare a *Proprietà* per trovare la chiave di strumentazione di Application Insights (sotto forma di un GUID). La chiave viene usata per:
* Integrare AppInsights per la ricezione di eventi a livello di infrastruttura da un cluster di Service Fabric direttamente tramite un modello di Azure Resource Manager o tramite il portale di Azure durante la creazione di un cluster, presupponendo che il servizio Diagnostica sia stato abilitato
* Configurare EventFlow (eventFlowConfig.json) per restituire i dati ad Application Insights, come illustrato nella sezione precedente

### <a name="oms"></a>OMS

OMS è un altro strumento consigliato per la diagnostica e il monitoraggio dei cluster di Service Fabric. La soluzione Service Fabric può essere aggiunta a qualsiasi area di lavoro e ha un dashboard che mostra i diversi tipi di eventi di Service Fabric. Le aree di lavoro di OMS hanno anche un potente strumento di query dei log in Log Analytics.

Per configurare un'area di lavoro di OMS, verificare che il servizio Diagnostica sia stato abilitato per il cluster. Aggiungere "Analisi Service Fabric" da Azure Marketplace a un'area di lavoro di OMS esistente oppure creare una nuova area. Configurare le origini dati dell'area di lavoro per la connessione alle tabelle di Archiviazione di Azure in cui il cluster scrive gli eventi. 

Affinché OMS possa rilevare eventi personalizzati, è anche necessario assicurarsi che la strumentazione aggiunta alle applicazioni scriva nelle stesse tabelle di Archiviazione o in altre tabelle configurate come origini per l'area di lavoro. 

OMS attualmente è anche la soluzione consigliata per la visualizzazione e l'analisi dei dati se sono necessari monitoraggio e diagnostica per i contenitori, poiché è possibile aggiungere una soluzione Contenitori nell'area di lavoro che funziona bene con i contenitori orchestrati da Service Fabric. Per una breve guida per questa operazione, vedere [qui](service-fabric-diagnostics-containers-windowsserver.md).

### <a name="azure-monitor"></a>Monitoraggio di Azure

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) per monitorare molte risorse di Azure in cui viene creato un cluster di Service Fabric. Un set di metriche per il [set di scalabilità di macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e per le singole [macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) viene raccolto automaticamente e visualizzato nel portale di Azure. Per visualizzare le informazioni raccolte, nel portale di Azure selezionare il gruppo di risorse che contiene il cluster di Service Fabric. Selezionare quindi il set di scalabilità di macchine virtuali da visualizzare. Nella sezione **Monitoraggio** selezionare **Metriche** per visualizzare un grafico dei valori.

![Visualizzazione del portale di Azure dei dati di metrica raccolti](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Per personalizzare i grafici, seguire le istruzioni disponibili in [Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). È anche possibile creare avvisi in base a queste metriche, come illustrato in [Creare avvisi in Monitoraggio di Azure per servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). È possibile inviare avvisi a un servizio di notifica usando webhook, come illustrato in [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Monitoraggio di Azure supporta solo una sottoscrizione. Se è necessario monitorare più sottoscrizioni o se sono necessarie funzionalità aggiuntive, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), incluso in Microsoft Operations Management Suite, fornisce una soluzione olistica di gestione IT per infrastrutture locali e basate sul cloud. È possibile indirizzare i dati di Monitoraggio di Azure direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

È consigliabile usare Operations Management Suite per monitorare l'infrastruttura locale, ma è possibile usare qualsiasi soluzione esistente usata dall'organizzazione per il monitoraggio dell'infrastruttura.

## <a name="additional-steps"></a>Passaggi aggiuntivi

### <a name="watchdogs"></a>Watchdog

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. I watchdog sono una buona posizione in cui ospitare codice in grado di eseguire interventi correttivi per condizioni note senza bisogno di azioni da parte degli utenti. È possibile trovare un'implementazione di esempio del servizio watchdog [qui](https://github.com/Azure-Samples/service-fabric-watchdog-service).


## <a name="next-steps"></a>Passaggi successivi

* [Raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Raccogliere log direttamente da un processo del servizio di Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md)

