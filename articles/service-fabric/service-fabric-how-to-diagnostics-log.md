---
title: Generare eventi di log da un'app .NET Service Fabric in Azure o da un cluster autonomo
description: Informazioni su come aggiungere la registrazione all'applicazione .NET Service Fabric ospitata in un cluster di Azure o in un cluster autonomo.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: d1b3dc25dd9bda9d7f9d9152c2a94cea8321f5cf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660852"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Aggiungere la registrazione all'applicazione di Service Fabric

L'applicazione deve fornire informazioni sufficienti per eseguire un debug accurato quando si verificano problemi. La registrazione è una delle funzioni più importanti che è possibile aggiungere all'applicazione di Service Fabric. Quando si verifica un errore, una registrazione accurata può consentire un'analisi precisa degli errori. L'analisi dei modelli di log consente di scoprire diversi modi per migliorare le prestazioni o la progettazione dell'applicazione. Questo documento illustra alcune opzioni di registrazione diverse.

## <a name="eventflow"></a>EventFlow

Il gruppo di [librerie EventFlow](https://github.com/Azure/diagnostics-eventflow) consente alle applicazioni di definire quali dati di diagnostica raccogliere e dove eseguire l'output di questi. I dati di diagnostica possono essere costituiti da qualsiasi elemento, da contatori delle prestazioni a tracce di applicazioni. L'esecuzione avviene nello stesso processo dell'applicazione. In questo modo il sovraccarico di comunicazione viene ridotto al minimo. Per altre informazioni su EventFlow e Service Fabric, vedere [Aggregazione di eventi di Azure Service Fabric con EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Uso di eventi EventSource strutturati

La definizione di eventi messaggio per caso d'uso consente di creare pacchetti relativi all'evento nel contesto dell'evento stesso. È possibile eseguire più facilmente ricerche e filtri in base ai nomi o ai valori delle proprietà dell'evento specificato. Strutturando l'output della strumentazione è possibile leggerlo con più facilità, ma ciò richiede sforzi maggiori per definire un evento per ciascun caso d'uso. 

Alcune definizioni di evento possono essere condivise nell'intera applicazione. Un evento di avvio o arresto di un metodo, ad esempio, può essere riutilizzato in molti servizi in un'applicazione. Un servizio specifico per un dominio, ad esempio un sistema di ordine, può avere un evento **CreateOrder**, che ha un proprio evento univoco. Questo approccio può generare un numero elevato di eventi e può potenzialmente richiedere il coordinamento degli identificatori tra i team di progetto. 

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

    ...

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

    ...

```

Anche un ibrido di strumentazione strutturata e generica può essere una soluzione ideale. La strumentazione strutturata viene usata per la segnalazione degli errori e per le metriche. Gli eventi generici possono essere usati per la registrazione dettagliata utilizzata dai tecnici per la risoluzione dei problemi.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

La funzione di registrazione di ASP.NET Core ([pacchetto NuGet Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) è un framework di registrazione che fornisce un'API di registrazione standard per l'applicazione. Alla funzione di registrazione di ASP.NET Core è possibile collegare il supporto per altri back-end di registrazione. Ciò offre un'ampia gamma di supporto per l'elaborazione di registrazioni nell'applicazione, senza alcuna necessità di modifiche rilevanti al codice.

1. Aggiungere il pacchetto NuGet **Microsoft.Extensions.Logging** al progetto da instrumentare. Aggiungere anche eventuali pacchetti di provider. Per altre informazioni, vedere [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Registrazione in ASP.NET Core).
2. Aggiungere una direttiva **using** per **Microsoft.Extensions.Logging** al file del servizio.
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

### <a name="using-other-logging-providers"></a>Uso di altri provider di registrazione

Alcuni provider di terze parti usano l'approccio descritto nella sezione precedente, inclusi [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/) e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). È possibile inserirli tutti nella registrazione ASP.NET Core oppure usarli separatamente. Serilog offre una funzionalità che arricchisce tutti i messaggi inviati da un logger. Questa funzionalità può essere utile per restituire il nome del servizio, il tipo e le informazioni sulla partizione. Per usare questa funzionalità nell'infrastruttura di ASP.NET Core, seguire questa procedura:

1. Aggiungere i pacchetti NuGet **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate** e **Serilog.Sinks.Observable** al progetto. 
2. Creare una `LoggerConfiguration` e l'istanza del logger.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Aggiungere un argomento `Serilog.ILogger` al costruttore del servizio e passare il logger appena creato.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. Nel costruttore del servizio, crea enricher delle proprietà per **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId**.

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
   >Con l'esempio precedente è consigliabile *non* usare `Log.Logger` statico. Service Fabric può ospitare più istanze dello stesso tipo di servizio in un singolo processo. Se si usa `Log.Logger` statico, l'ultimo writer degli enricher delle proprietà visualizza i valori per tutte le istanze in esecuzione. Questo è un motivo per cui la variabile _logger è una variabile di membro privata della classe di servizio. È anche necessario rendere disponibile `_logger` al codice comune, che può essere usato in più servizi.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [monitoraggio delle applicazioni in Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Informazioni sulla registrazione con [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md).










