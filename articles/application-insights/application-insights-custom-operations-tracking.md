---
title: Verifica delle operazioni personalizzate con Azure Application Insights .NET SDK | Documentazione Microsoft
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
ms.date: 06/30/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b31d38fe2f7060597956a1ee9c66f43ce39d7240
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Verifica delle operazioni personalizzate con Application Insights .NET SDK

Gli SDK di Application Insights di Azure verificano automaticamente le richieste HTTP in ingresso e le chiamate ai servizi dipendenti, ovvero richieste HTTP, query SQL e così via. La verifica e la correlazione di richieste e dipendenze offre visibilità sui tempi di risposta e sull'affidabilità dell'intera applicazione in tutti i microservizi che combinano questa applicazione. 

Esiste una classe di modelli di applicazione che non può essere supportata in modo generico. Per il corretto monitoraggio di tali modelli, è necessaria la strumentazione manuale del codice. Questo articolo descrive alcuni criteri che potrebbero richiedere una strumentazione manuale, ad esempio per l’elaborazione di code personalizzate e l'esecuzione prolungata di attività in background.

Questo documento offre indicazioni su come tenere traccia delle operazioni personalizzate con Application Insights SDK. Questa documentazione è relativa a:

- Application Insights per .NET (noto anche come Base SDK) versione 2.4+.
- Application Insights per applicazioni Web (che esegue ASP.NET) versione 2.4+.
- Application Insights per ASP.NET Core versione 2.1+.

## <a name="overview"></a>Panoramica
Un'operazione è un lavoro logico eseguito da un'applicazione. Sono indicati nome, ora di avvio, durata, risultato e contesto dell'esecuzione, ad esempio nome utente, proprietà e risultato. Se l'operazione A è stata avviata dall'operazione B, l'operazione B è impostata come elemento padre per A. Un'operazione può avere un solo padre, ma può avere molte operazioni figlio. Per ulteriori informazioni sulle operazioni e la correlazione dei dati di telemetria, vedere [Correlazione dei dati di telemetria di Azure Application Insights](application-insights-correlation.md).

Nell’SDK di Application Insights .NET l'operazione viene descritta dalla classe astratta [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) e dai discendenti [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Verifica delle operazioni in ingresso 
Application Insights Web SDK raccoglie automaticamente le richieste HTTP per le applicazioni ASP.NET eseguite nella pipeline di IIS e per tutte le applicazioni ASP.NET Core. Sono disponibili soluzioni supportate dalla community per altre piattaforme e altri framework. Se tuttavia l'applicazione non è supportata dalle soluzioni standard o supportate dalla community, è possibile instrumentarla manualmente.

Un altro esempio che richiede la verifica personalizzata è offerto dal ruolo di lavoro che riceve gli elementi dalla coda. Per alcune code, la chiamata per aggiungere un messaggio a questa coda viene registrata come dipendenza. L'operazione di alto livello che descrive l'elaborazione del messaggio tuttavia non viene raccolta automaticamente.

Di seguito viene illustrato come è possibile tenere traccia di tali operazioni.

A un livello elevato, l'attività consiste nel creare `RequestTelemetry` e impostare le proprietà note. Al termine dell'operazione, tenere traccia dei dati di telemetria. L'esempio seguente illustra questa attività.

### <a name="http-request-in-owin-self-hosted-app"></a>Richiesta HTTP nell'app con self-hosting Owin
In questo esempio, si seguirà il [protocollo HTTP per la correlazione](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Si deve prevedere di ricevere le intestazioni descritte qui.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
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
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Il protocollo HTTP per la correlazione dichiara inoltre l’intestazione `Correlation-Context`. Tuttavia, è omesso qui per motivi di semplicità.

## <a name="queue-instrumentation"></a>Strumentazione della coda
Per la comunicazione HTTP, è stato creato un protocollo per il passaggio dei dettagli della correlazione. Con alcuni protocolli delle code è possibile passare metadati aggiuntivi con il messaggio, con altri invece non è consentito.

### <a name="service-bus-queue"></a>Coda del bus di servizio
Con la [coda del bus di servizio](../service-bus-messaging/index.md) di Microsoft Azure è possibile passare un contenitore di proprietà insieme al messaggio. Viene usato per passare l'ID di correlazione.

La coda del bus di servizio usa protocolli basati su TCP. Application Insights non tiene traccia automaticamente delle operazioni della coda, quindi ne viene tenuta traccia manualmente. L'operazione di rimozione dalla coda è un'API di tipo push e non è possibile tenerne traccia.

#### <a name="enqueue"></a>Accodare

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
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
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Coda di archiviazione di Azure
L'esempio seguente illustra come tenere traccia delle operazioni della [coda di archiviazione di Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) e correlare i dati di telemetria tra producer, consumer e Archiviazione di Azure. 

La coda di archiviazione ha un'API HTTP. Tutte le chiamate alla coda vengono tracciate dall'agente di raccolta di dipendenze Application Insights per le richieste HTTP.
Assicurarsi di avere `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` in `applicationInsights.config`. Se non è disponibile, aggiungerlo a livello di programmazione come descritto in [Filtraggio e pre-elaborazione nell’SDK Azure Application Insights](app-insights-api-filtering-sampling.md).

Se si configura Application Insights manualmente, creare e inizializzare `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` in modo simile a:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Inoltre è possibile correlare l'ID operazione di Application Insights con l'ID di richiesta di Archiviazione. Per informazioni su come impostare e ottenere un client di richiesta di Archivazione e un ID di richiesta del server, vedere [Monitoraggio, diagnosi e risoluzione dei problemi dell'archiviazione di Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Accodare
Poiché le code di archiviazione di Azure supportano l'API HTTP, tutte le operazioni con la coda vengono automaticamente registrate da Application Insights. In molti casi, questa strumentazione dovrebbe essere sufficiente. Per correlare le tracce sul lato consumer con le tracce del producer, è necessario passare parte del contesto di correlazione in modo simile a quanto avviene nel protocollo HTTP per la correlazione. 

In questo esempio, si traccia l’operazione facoltativa `Enqueue`. È possibile:

 - **Correlare gli eventuali tentativi**, che hanno tutti un'operazione padre comune, ovvero `Enqueue`. In caso contrario, vengono registrati come elementi figlio della richiesta in ingresso. Se sono presenti più richieste logiche per la coda, potrebbe risultare difficile trovare la chiamata che ha restituito i tentativi.
 - **Correlare i log di archiviazione (se e quando necessario)** con i dati di telemetria di Application Insights.

L'operazione `Enqueue` è l’elemento figlio di un'operazione padre (ad esempio, una richiesta HTTP in ingresso). La chiamata di dipendenza HTTP è l'elemento figlio dell'operazione `Enqueue` e nipote della richiesta in ingresso:

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Per ridurre la quantità di dati di telemetria segnalati dall'applicazione o per non tenere traccia dell'operazione `Enqueue` per altri motivi, è possibile usare direttamente l'API `Activity`:

- Creare (e avviare) un nuovo `Activity` anziché avviare l'operazione Application Insights. *Non* è necessario assegnare proprietà ad essa, tranne il nome dell'operazione.
- Serializzare `yourActivity.Id` nel payload dei messaggi invece di `operation.Telemetry.Id`. È anche possibile usare `Activity.Current.Id`.


#### <a name="dequeue"></a>Rimuovere dalla coda
In modo simile a `Enqueue`, la richiesta HTTP effettiva per la coda di archiviazione viene automaticamente registrata da Application Insights. L'operazione `Enqueue` tuttavia viene probabilmente eseguita nel contesto padre, ad esempio il contesto della richiesta in ingresso. Gli SDK di Application Insights correlano automaticamente tale operazione (e la parte HTTP) con la richiesta padre e gli altri dati di telemetria segnalati nello stesso ambito.

L'operazione `Dequeue` è un'operazione complessa. L’SDK Application Insights tiene automaticamente traccia delle richieste HTTP. Tuttavia, non conosce il contesto di correlazione fino a quando non viene analizzato il messaggio. Non è possibile correlare la richiesta HTTP per ottenere il messaggio con gli altri dati di telemetria.

In molti casi, può essere utile correlare la richiesta di coda HTTP anche con le altre tracce. L'esempio seguente illustra come fare:

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

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
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
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

Nell'esempio seguente viene tracciato il messaggio in arrivo in modo simile a quanto avviene per la richiesta HTTP in ingresso:

```C#
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Analogamente, è possibile instrumentare le altre operazioni della coda. L'operazione di visualizzazione deve essere instrumentata in modo simile a quella di rimozione dalla coda. Non è necessario instrumentare operazioni di gestione della coda. Application Insights tiene traccia di operazioni come HTTP e nella maggior parte dei casi è sufficiente.

Quando si instrumenta l'eliminazione di un messaggio, assicurarsi di impostare gli identificatori delle operazioni (correlazione). In alternativa, è possibile usare l'API `Activity`. Non è quindi necessario impostare gli identificatori delle operazioni negli elementi di telemetria perché Application Insights esegue questa operazione automaticamente:

- Creare un nuovo oggetto `Activity` dopo avere ottenuto un elemento dalla coda.
- Usare `Activity.SetParentId(message.ParentId)` per correlare i log del consumer e del producer.
- Avviare il `Activity`.
- Tenere traccia delle operazioni di rimozione dalla coda, elaborazione ed eliminazione usando gli helper `Start/StopOperation`. dallo stesso flusso di controllo asincrono (contesto di esecuzione). In questo modo la correlazione sarà corretta.
- Arrestare il `Activity`.
- Usare `Start/StopOperation` o chiamare `Track` telemetry manualmente.

### <a name="batch-processing"></a>Elaborazione batch
Per alcune code, è possibile una rimozione dalla coda di più messaggi con una singola richiesta. L'elaborazione di tali messaggi è presumibilmente indipendente e appartiene a diverse operazioni logiche. In questo caso, non è possibile correlare l'operazione `Dequeue` a una determinata elaborazione dei messaggi.

Ogni elaborazione dei messaggi deve essere eseguita nel proprio flusso di controllo asincrono. Per ulteriori informazioni, vedere la sezione [Verifica delle dipendenze in uscita](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Attività in background a esecuzione prolungata
Alcune applicazioni avviano operazioni a esecuzione prolungata che possono essere causate dalle richieste degli utenti. Dal punto di vista della verifica/strumentazione, non c'è differenza dalla strumentazione delle richieste o delle dipendenze: 

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
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In questo esempio, si usa `telemetryClient.StartOperation` per creare `RequestTelemetry` e riempire il contesto di correlazione. Si supponga di avere un'operazione padre creata dalle richieste in ingresso che hanno pianificato l'operazione. `BackgroundTask`, purché venga avviato nello stesso flusso di controllo asincrono di una richiesta in ingresso, viene correlato con tale operazione padre. `BackgroundTask` e tutti gli elementi di telemetria annidati vengono automaticamente correlati alla richiesta che l'ha generato anche dopo la fine della richiesta.

Quando l'attività viene avviata dal thread in background a cui non sono associate operazioni (`Activity`), `BackgroundTask` non ha elementi padre. Tuttavia, può avere operazioni annidate. Tutti gli elementi di telemetria segnalati dall'attività sono correlati a `RequestTelemetry` creato in `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Verifica delle dipendenze in uscita
È possibile tenere traccia della propria tipologia di dipendenza o di operazioni non supportate da Application Insights.

Il metodo `Enqueue` nella coda del bus di servizio o nella coda di archiviazione è un esempio di tale verifica personalizzata.

L'approccio generale per la verifica personalizzata delle dipendenze è:

- Chiamare il metodo `TelemetryClient.StartOperation` (estensione) che riempie le proprietà `DependencyTelemetry` necessarie per la correlazione e altre proprietà (timestamp di avvio, durata).
- Impostare le altre proprietà personalizzate in `DependencyTelemetry`, ad esempio nome e altri contesti necessari.
- Effettuare una chiamata di dipendenza e attendere.
- Al termine, arrestare l'operazione con `StopOperation`.
- Gestire le eccezioni.

`StopOperation` arresta solo l'operazione che è stata avviata. Se l'operazione corrente in esecuzione non corrisponde all'operazione che si desidera arrestare, `StopOperation` non esegue alcuna operazione. Questa situazione può verificarsi se si avviano più operazioni in parallelo nello stesso contesto di esecuzione:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

È quindi necessario assicurarsi di chiamare sempre `StartOperation` e di eseguire l'attività nel contesto corretto:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // Update status code and success as appropriate.
    }
    catch(...) 
    {
        // Update status code and success as appropriate.
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
- Estrarre la [configurazione](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) standard di una raccolta di proprietà di contesto.
- Vedere [System.Diagnostics.Activity User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) (Guida dell'utente di System.Diagnostics.Activity) per informazioni su come correlare i dati di telemetria.

