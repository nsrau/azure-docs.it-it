---
title: Verifica delle operazioni personalizzate con Azure Application Insights .NET SDK | Microsoft Docs
description: Verifica delle operazioni personalizzate con Azure Application Insights .NET SDK
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Verifica delle operazioni personalizzate con Application Insights .NET SDK

Gli SDK di Application Insights tengono automaticamente traccia delle richieste HTTP in ingresso e delle chiamate ai servizi dipendenti, ovvero richieste HTTP, query SQL e così via. Nel caso di un'applicazione costituita da più microservizi, la verifica e la correlazione di richieste e dipendenze offre visibilità sui tempi di risposta e sull'affidabilità dell'intera applicazione in tutti i microservizi. L'elenco verrà gradualmente ampliato e verrà effettuata una raccolta automatica di altre piattaforme e framework noti. 

Esiste una classe di modelli di applicazione che non possono essere supportati in modo generico. Per il corretto monitoraggio di tali modelli, è necessaria la strumentazione manuale del codice. Questo articolo illustra alcuni modelli che possono richiedere la strumentazione manuale, tra cui l'elaborazione di code personalizzata o l'esecuzione prolungata di attività in background.

Questo documento offre indicazioni su come tenere traccia delle operazioni personalizzate con ApplicationInsights SDK.

Il documento è rilevante per:
- Application Insights per .NET (noto anche come Base SDK) versione `2.4+`
- Application Insights per applicazioni Web (che esegue ASP.NET) versione `2.4+`
- Application Insights per AspNetCore versione `2.1+`

## <a name="overview"></a>Panoramica
In base all'operazione, si può comprendere parte della logica delle attività eseguite dall'applicazione. Sono indicati nome, ora di avvio, durata e contesto dell'esecuzione, ad esempio nome utente, proprietà e risultato. Se l'operazione `A` è stata avviata dall'operazione `B`, l'operazione `B` viene impostata come padre di `A`.  L'operazione può avere una sola operazione padre e più operazioni figlio. Per altre informazioni sulle operazioni e sulla correlazione dei dati di telemetria, vedere [qui](application-insights-correlation.md).

In Application Insights .NET SDK l'operazione viene descritta dalla classe astratta [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) e dai discendenti [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Verifica delle operazioni in ingresso 
Application Insights Web SDK raccoglie automaticamente le richieste HTTP per le applicazioni ASP.NET eseguite nella pipeline di IIS e per tutte le applicazioni ASP.NET Core. Sono disponibili soluzioni supportate dalla community per altre piattaforme e altri framework. Se tuttavia l'applicazione non è supportata dalle soluzioni standard o supportate dalla community, è possibile instrumentarla manualmente.

Un altro esempio che richiede la verifica personalizzata è offerto dal ruolo di lavoro che riceve gli elementi dalla coda. Per alcune code, la chiamata per aggiungere un messaggio a questa coda viene registrata come dipendenza. L'operazione generale che descrive l'elaborazione del messaggio tuttavia non viene raccolta automaticamente.

Di seguito viene illustrato come è possibile tenere traccia di tali operazioni.

In generale, l'attività consiste nel creare `RequestTelemetry`, nell'impostare le proprietà note e, al termine dell'operazione, nel tenere traccia della telemetria. L'esempio seguente illustra come fare.

### <a name="http-request-in-owin-self-hosted-app"></a>Richiesta HTTP nell'app con self-hosting Owin
Viene seguito il [protocollo HTTP per la correlazione](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) e si riceveranno le intestazioni descritte nel protocollo.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Il protocollo per la correlazione HTTP dichiara anche l'intestazione `Correlation-Context` che viene tuttavia omessa per maggiore semplicità.

## <a name="queue-instrumentation"></a>Strumentazione della coda
Per la comunicazione HTTP, è stato creato un protocollo per passare i dettagli della correlazione. Alcuni protocolli delle code consentono di passare metadati aggiuntivi con il messaggio, altri invece non lo consentono.

### <a name="service-bus-queue"></a>Coda del bus di servizio
La [coda del bus di servizio](../service-bus-messaging/index.md) consente di passare il contenitore delle proprietà con il messaggio e viene usata per passare l'ID correlazione.

La coda del bus di servizio usa protocolli basati su TCP. Application Insights non tiene traccia automaticamente delle operazioni della coda, quindi ne viene tenuta traccia manualmente. L'operazione di rimozione dalla coda è un'API di tipo push e non è possibile tenerne traccia.

#### <a name="enqueue"></a>Accodare

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Coda di archiviazione di Azure
L'esempio seguente illustra come tenere traccia delle operazioni della [coda di archiviazione di Azure](../storage/storage-dotnet-how-to-use-queues.md) e correlare i dati di telemetria tra producer, consumer e Archiviazione di Azure. 

La coda di archiviazione di Azure ha un'API HTTP e le chiamate alla coda vengono registrate da DependencyCollector di ApplicationInsights per le richieste HTTP.
Verificare di avere `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` in `applicationInsights.config` o aggiungerlo a livello di codice come illustrato [qui](app-insights-api-filtering-sampling.md).

Se si configura ApplicationInsights manualmente, creare e inizializzare `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` in modo simile a:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

È anche possibile correlare l'ID operazione di ApplicationInsights con RequestId di Archiviazione di Azure. Per informazioni su come impostare e ottenere il client della richiesta di archiviazione e l'ID richiesta del server, vedere [questo articolo](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Accodare
Poiché le code di archiviazione di Azure supportano l'API HTTP, tutte le operazioni con la coda vengono automaticamente registrate da ApplicationInsights. In molti casi, questa strumentazione dovrebbe essere sufficiente.
Per correlare le tracce sul lato consumer con le tracce del producer, è necessario passare parte del contesto di correlazione in modo simile a quanto avviene nel protocollo HTTP per la correlazione. 

Nell'esempio seguente viene registrata l'operazione facoltativa `Enqueue`, che consente di:
 - Correlare gli eventuali tentativi, che hanno tutti un'operazione padre comune, ovvero `Enqueue`. In caso contrario, vengono registrati come elementi figlio della richiesta in ingresso. Se quindi sono presenti più richieste logiche per la coda, potrebbe risultare difficile trovare la chiamata che ha restituito i tentativi.
 - Correlare i log di archiviazione di Azure (se e quando necessario) con i dati di telemetria di ApplicationInsights.

L'operazione `Enqueue` è l'elemento figlio di un'operazione padre (ad esempio, una richiesta HTTP in ingresso) e la chiamata alla dipendenza HTTP è l'elemento figlio dell'operazione `Enqueue` e nipote della richiesta in ingresso.

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

Per ridurre la quantità di dati di telemetria segnalati dall'applicazione o per non tenere traccia dell'operazione `enqueue` per altri motivi, è possibile usare direttamente l'API `Activity`:

- Creare (e avviare) un nuova `Activity` invece di avviare l'operazione di ApplicationInsights. NON è necessario assegnare proprietà tranne il nome dell'operazione.
- Serializzare `yourActivity.Id` nel payload dei messaggi invece di `operation.Telemetry.Id`. È anche possibile usare `Activity.Current.Id`


#### <a name="dequeue"></a>Rimuovere dalla coda
In modo simile a `Enqueue`, la richiesta HTTP effettiva per la coda di archiviazione di Azure viene automaticamente registrata da ApplicationInsights. L'operazione `Enqueue` tuttavia viene probabilmente eseguita nel contesto padre, ad esempio il contesto della richiesta in ingresso. Gli SDK di Application Insights correlano automaticamente tale operazione (e la parte HTTP) con la richiesta padre e gli altri dati di telemetria segnalati nello stesso ambito.

L'operazione `Dequeue` è complessa: Application Insights SDK tiene automaticamente traccia delle richieste HTTP, tuttavia viene a conoscenza del contesto di correlazione solo dopo che il messaggio è stato analizzato. Non è possibile correlare la richiesta HTTP per ottenere il messaggio con gli altri dati di telemetria.

In molti casi, può essere utile correlare la richiesta HTTP anche con le altre tracce. L'esempio successivo illustra come farlo.

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

Nell'esempio seguente viene tracciato il messaggio in arrivo in modo simile a quanto avviene per la richiesta HTTP in ingresso.

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

Analogamente, è possibile instrumentare le altre operazioni della coda. L'operazione di visualizzazione deve essere instrumentata in modo simile a quella di rimozione dalla coda. Non è necessario instrumentare operazioni di gestione della coda. Application Insights tiene traccia di tali operazioni come HTTP e nella maggior parte dei casi è sufficiente.

Quando si instrumenta l'eliminazione di un messaggio, assicurarsi di impostare gli identificatori delle operazioni (correlazione). In alternativa, è possibile usare l'API `Activity`. Non è quindi necessario impostare gli identificatori delle operazioni negli elementi di telemetria perché ApplicationInsights esegue questa operazione automaticamente:

- Creare un nuovo oggetto `Activity` dopo avere ottenuto un elemento dalla coda
- Usare `Activity.SetParentId(message.ParentId)` per correlare i log del consumer e del producer
- Avviare `Activity`
- Tenere traccia delle operazioni di rimozione dalla coda, elaborazione ed eliminazione usando gli helper `Start/StopOperation` dallo stesso flusso di controllo asincrono (contesto di esecuzione). In questo modo la correlazione sarà corretta.
- Arrestare quindi `Activity`
- Usare `Start/StopOperation` o chiamare Track telemetry manualmente 

### <a name="batch-processing"></a>Elaborazione batch
Alcune code consentono di rimuovere dalla coda più messaggi con una sola richiesta, ma l'elaborazione di tali messaggi è presumibilmente indipendente e appartiene a operazioni logiche diverse.
In questo caso, non è possibile correlare l'operazione `Dequeue` a una determinata elaborazione dei messaggi.

Ogni elaborazione dei messaggi deve essere eseguita nel proprio flusso di controllo asincrono. Per altre informazioni, vedere la sezione [Verifica delle dipendenze in uscita](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Attività in background a esecuzione prolungata
Alcune applicazioni avviano operazioni a esecuzione prolungata che possono essere causate dalle richieste degli utenti. Dal punto di vista della verifica/strumentazione, non c'è differenza dalla strumentazione delle richieste o delle dipendenze. 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In questo esempio, si usa `telemetryClient.StartOperation` per creare `RequestTelemetry` e riempire il contesto di correlazione. Si supponga di avere un'operazione padre, creata ad esempio dalle richieste in ingresso che hanno pianificato l'operazione. `BackgroundTask`, purché venga avviato nello stesso flusso di controllo asincrono di una richiesta in ingresso, viene correlato con tale operazione padre. `BackgroundTask` e tutti gli elementi di telemetria annidati verranno automaticamente correlati alla richiesta che l'ha generato anche dopo la fine della richiesta.

Quando l'attività viene avviata dal thread in background a cui non sono associate operazioni (Activity), `BackgroundTask` non ha elementi padre. Può tuttavia avere operazioni annidate e tutti gli elementi di telemetria segnalati dall'attività sono correlati a `RequestTelemetry` creato in `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Verifica delle dipendenze in uscita
È possibile tenere traccia del proprio tipo di dipendenza o di operazioni non supportate da ApplicationInsights.

Il metodo `Enqueue` nella coda del bus di servizio o nella coda di archiviazione di Azure è un esempio di tale verifica personalizzata.

L'approccio generale per la verifica personalizzata delle dipendenze è:
- Chiamare il metodo `TelemetryClient.StartOperation` (estensione) che riempie le proprietà `DependencyTelemetry` necessarie per la correlazione e altre proprietà (timestamp di avvio, durata).
- Impostare le altre proprietà personalizzate in `DependencyTelemetry`: nome e altri contesti necessari
- Effettuare una chiamata alle dipendenze e attendere
- Al termine, arrestare l'operazione con `StopOperation`
- Gestire le eccezioni

`StopOperation` arresta solo l'operazione avviata: se l'operazione in esecuzione corrente non corrisponde a quella che si vuole arrestare, StopOperation non esegue alcuna operazione. Questo problema può verificarsi se si avviano più operazioni in parallelo nello stesso contesto di esecuzione:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

È quindi necessario verificare di chiamare sempre `StartOperation` e di eseguire l'attività nel contesto corretto:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni di base sulla [correlazione di dati di telemetria](application-insights-correlation.md) in Application Insights.
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- Segnalare [metriche ed eventi](app-insights-api-custom-events-metrics.md) personalizzati ad Application Insights.
- Estrarre la [configurazione](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) di una raccolta di proprietà di contesto standard.
- Vedere [System.Diagnostics.Activity User Guide (Guida dell'utente di System.Diagnostics.Activity)](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) per informazioni su come correlare i dati di telemetria

