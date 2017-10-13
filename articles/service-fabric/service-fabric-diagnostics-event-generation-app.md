---
title: Monitoraggio dell'applicazione Azure Service Fabric | Microsoft Docs
description: Informazioni su log ed eventi a livello di servizio e applicazione usati per monitorare e diagnosticare i cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Generazione di eventi e log a livello di applicazione e servizio

## <a name="instrumenting-the-code-with-custom-events"></a>Strumentazione del codice con eventi personalizzati

La strumentazione del codice è la base di molti altri aspetti di monitoraggio dei servizi. La strumentazione è l'unico modo per rilevare eventuali problemi e individuare le correzioni necessarie. Anche se è tecnicamente possibile connettere un debugger a un servizio di produzione, questa non è una procedura comune. I dati dettagliati di strumentazione sono quindi importanti.

Alcuni prodotti instrumentano automaticamente il codice. Benché queste soluzioni siano efficaci, è quasi sempre necessaria una strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. Questo documento illustra diversi approcci alla strumentazione del codice e indicano quando è consigliabile scegliere un approccio rispetto a un altro.

## <a name="eventsource"></a>EventSource
Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe derivata da **EventSource** (**ServiceEventSource** o **ActorEventSource**). Viene creato un modello in cui è possibile aggiungere eventi per l'applicazione o il servizio. Il nome di **EventSource** **deve** essere univoco e deve essere rinominato dalla stringa del modello predefinito MyCompany-&lt;soluzione&gt;-&lt;progetto&gt;. Se esistono più definizioni di **EventSource** con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Alcune organizzazioni preassegnano intervalli di valori per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni, vedere il [blog di Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentazione di MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Uso di eventi EventSource strutturati

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

### <a name="using-eventsource-generically"></a>Uso generico di EventSource

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

## <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

È importante pianificare con attenzione la strumentazione del codice. Il piano di strumentazione corretto può consentire di evitare la potenziale destabilizzazione della codebase e la conseguente necessità di ripetere la strumentazione del codice. Per ridurre il rischio, è possibile scegliere una libreria di strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), inclusa in Microsoft ASP.NET Core. ASP.NET Core ha un'interfaccia [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) che può essere usata con il provider preferito, riducendo al minimo l'effetto sul codice esistente. È possibile usare il codice in ASP.NET Core in Windows e Linux e in .NET Framework completo, in modo da standardizzare la strumentazione del codice. Ciò viene approfondito di seguito:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso di Microsoft.Extensions.Logging in Service Fabric

1. Aggiungere il pacchetto Microsoft.Extensions.Logging NuGet al progetto da strumentare. Aggiungere anche eventuali pacchetti del provider. Per un pacchetto di terze parti, vedere l'esempio seguente. Per altre informazioni, vedere [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Registrazione in ASP.NET Core).
2. Aggiungere una direttiva **using** per Microsoft.Extensions.Logging al file del servizio.
3. Definire una variabile privata all'interno della classe di servizio.

  ```csharp
  private ILogger _logger = null;

  ```
4. Nel costruttore della classe del servizio aggiungere il codice seguente:

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

## <a name="using-other-logging-providers"></a>Uso di altri provider di registrazione

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

4. Nel costruttore del servizio aggiungere il codice seguente che crea il necessario per arricchire le proprietà **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** del servizio. Aggiunge anche un enricher delle proprietà alla struttura di registrazione di ASP.NET Core, per consentire di usare Microsoft.Extensions.Logging.ILogger nel codice.

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

  >[!NOTE]
  >È consigliabile non usare Log.Logger statico con l'esempio precedente. Service Fabric può ospitare più istanze dello stesso tipo di servizio in un singolo processo. Se si usa Log.Logger statico, l'ultimo writer degli enricher delle proprietà mostrerà i valori per tutte le istanze in esecuzione. Questo è un motivo per cui la variabile _logger è una variabile di membro privata della classe di servizio. È anche necessario rendere disponibile _logger al codice comune, che potrebbe essere usato nei servizi.

## <a name="choosing-a-logging-provider"></a>Scelta di un provider di registrazione

Se l'applicazione si basa sulle prestazioni elevate, **EventSource** è in genere l'approccio ottimale. **EventSource** usa *in genere* un numero minore di risorse e offre prestazioni migliori rispetto alla registrazione di ASP.NET Core o di eventuali soluzioni di terze parti disponibili.  Non è un problema per molti servizi, ma se il servizio è orientato alle prestazioni **EventSource** si rivela la scelta più indicata. Tuttavia per ottenere questi vantaggi della registrazione strutturata, **EventSource** richiede un investimento maggiore da parte del team tecnico. Se possibile, effettuare un rapido prototipo di alcune opzioni di registrazione e quindi scegliere quello che meglio soddisfa le esigenze dell'utente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver scelto il provider di accesso per instrumentare le applicazioni e i servizi, è necessario aggregare i log e gli eventi prima di inviarli a una piattaforma. Per meglio comprendere alcune delle opzioni consigliate leggere altre informazioni su [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [WAD](service-fabric-diagnostics-event-aggregation-wad.md).
