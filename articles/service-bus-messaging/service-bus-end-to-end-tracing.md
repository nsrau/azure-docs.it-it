---
title: Diagnostica e analisi end-to-end di Azure Service Bus | Microsoft Docs
description: Panoramica della diagnostica e dell'analisi end-to-end del client del bus di servizio
services: service-bus-messaging
documentationcenter: 
author: lmolkova
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Correlazione e analisi distribuita tramite la messaggistica del bus di servizio

Uno dei problemi più comuni riscontrati durante lo sviluppo di microservizi è costituito dalla possibilità di analizzare l'operazione da un client in tutti i servizi che partecipano all'elaborazione. Si tratta di una caratteristica utile per il debug, l'analisi delle prestazioni, i test A/B e altri comuni scenari di diagnostica.
Una parte di questo problema consiste nel tenere traccia degli elementi di lavoro logici. Tale operazione include i risultati e la latenza dell'elaborazione dei messaggi e le chiamate di dipendenza esterne. Un'altra parte è costituita dalla correlazione di questi eventi di diagnostica oltre i limiti del processo.

Quando un producer invia un messaggio tramite una coda, tale operazione avviene in genere nell'ambito di un'altra operazione logica, avviata da un altro client o servizio. La stessa operazione viene portata avanti dal consumer dopo che ha ricevuto un messaggio. Sia il producer che il consumer (e altri servizi che elaborano l'operazione) generano presumibilmente eventi di telemetria per analizzare il flusso e il risultato dell'operazione. Per correlare tali eventi e l'operazione di analisi end-to-end, ogni servizio che restituisce dati di telemetria deve contrassegnare tutti gli eventi con un contesto di analisi.

La messaggistica del bus di servizio di Microsoft Azure include proprietà di payload definite che producer e consumer devono usare per passare tale contesto di analisi.
Il protocollo si basa sul [protocollo di correlazione HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nome proprietà        | DESCRIZIONE                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificatore univoco di una chiamata esterna alla coda effettuata dal producer. Per la logica, le considerazioni e il formato, vedere [Request-Id in HTTP protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) (Request-Id nel protocollo HTTP). |
|  Correlation-Context | Contesto dell'operazione, che viene propagato in tutti i servizi coinvolti nell'elaborazione dell'operazione. Per altre informazioni, vedere [Correlation-Context in HTTP protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) (Correlation-Context nel protocollo HTTP). |

## <a name="service-bus-net-client-auto-tracing"></a>Analisi automatica del client .NET del bus di servizio

A partire dalla versione 3.0.0, il [client del bus di servizio di Microsoft Azure per .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornisce punti di strumentazione di analisi a cui possono agganciarsi sistemi di analisi o parti del codice client.
La strumentazione consente di verificare tutte le chiamate al servizio di messaggistica del bus di servizio dal lato client. Se per l'elaborazione dei messaggi si usa il [criterio del gestore di messaggi](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), anche tale operazione viene instrumentata.

### <a name="tracking-with-azure-application-insights"></a>Verifica con Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) offre funzionalità avanzate di monitoraggio delle prestazioni, tra cui la verifica automatica delle richieste e delle dipendenze.

A seconda del tipo di progetto, installare Application Insights SDK:
- [ASP.NET](../application-insights/app-insights-asp-net.md) 2.5-beta2 o versione successiva
- [ASP.NET Core](../application-insights/app-insights-asp-net-core.md) 2.2.0-beta2 o versione successiva.
Questi collegamenti forniscono informazioni dettagliate su come installare l'SDK, creare risorse e, se necessario, configurare l'SDK. Per le applicazioni non ASP.NET, vedere l'articolo [Azure Application Insights for Console Applications](../application-insights/application-insights-console.md) (Azure Application Insights per applicazioni console).

Se per elaborare i messaggi si usa il [criterio con gestore di messaggi](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), non ci sono altre operazioni da eseguire: tutte le chiamate al bus di servizio eseguite dal servizio vengono automaticamente verificate e correlate con altri elementi di telemetria. In caso contrario vedere l'esempio seguente per la verifica manuale delle operazioni di elaborazione dei messaggi.

#### <a name="trace-message-processing"></a>Analizzare l'elaborazione dei messaggi

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

In questo esempio, per ogni messaggio elaborato viene restituito `RequestTelemetry`, che include un timestamp, la durata e il risultato (esito positivo). La telemetria include anche un set di proprietà di correlazione.
Alle eccezioni e alle analisi nidificate restituite durante l'elaborazione dei messaggi vengono inoltre assegnate proprietà di correlazione in modo che vengano rappresentate come elementi figlio di `RequestTelemetry`.

Nel caso in cui durante l'elaborazione dei messaggi in cui si eseguano chiamate a componenti esterni supportati, anche questi verranno verificati e correlati automaticamente. Per informazioni sulla verifica e la correlazione manuale, vedere [Verifica delle operazioni personalizzate con Application Insights .NET SDK](../application-insights/application-insights-custom-operations-tracking.md).

### <a name="tracking-without-tracing-system"></a>Verifica senza sistema di analisi
Nel caso in cui il sistema di analisi non supporti la verifica automatica delle chiamate al bus di servizio, è possibile provare ad aggiungere questo supporto in un sistema di analisi o nell'applicazione. Questa sezione illustra gli eventi di diagnostica inviati dal client .NET del bus di servizio.  

Per instrumentare il client .NET del bus di servizio, si usano le primitive di analisi .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` funge da un contesto di analisi mentre `DiagnosticSource` è un meccanismo di notifica. 

Se non è presente alcun listener per gli eventi DiagnosticSource, la strumentazione è disattivata, quindi i costi di strumentazione sono azzerati. DiagnosticSource cede il controllo completo al listener:
- il listener controlla le origini e gli eventi di cui rimanere in ascolto
- il listener controlla il campionamento e la frequenza degli eventi
- gli eventi vengono inviati con un payload che fornisce il contesto completo, in modo che sia possibile accedere e modificare l'oggetto Message durante l'evento

Prima di procedere con l'implementazione, è consigliabile leggere il [manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

Nel codice seguente verrà creato un listener per eventi del bus di servizio nell'app ASP.NET Core che scrive i log con Microsoft.Extension.Logger.
Il listener usa la libreria [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) per eseguire la sottoscrizione a DiagnosticSource. È comunque possibile eseguire facilmente la sottoscrizione a DiagnosticSource anche senza la libreria.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In questo esempio il listener registra durata, risultato, identificatore univoco e ora di inizio di ogni operazione del bus di servizio.

#### <a name="events"></a>Eventi

Per ogni operazione vengono inviati due eventi, ovvero 'Start' e 'Stop'. Molto probabilmente si è interessati solo agli eventi di tipo 'Stop'. Tali eventi forniscono il risultato dell'operazione, nonché l'ora di inizio e la durata sotto forma di proprietà Activity.

Il payload dell'evento fornisce a un listener con il contesto dell'operazione, replicando il valore restituito e i parametri in arrivo dell'API. Il payload dell'evento 'Stop' contiene tutte le proprietà del payload dell'evento 'Start', di conseguenza è possibile ignorare completamente l'evento 'Start'.

Tutti gli eventi includono anche le proprietà 'Entity' ed 'Endpoint', che però non figurano nella tabella seguente.
  * `string Entity`: nome dell'entità (coda, argomento e così via)
  * `Uri Endpoint`: URL dell'endpoint del bus di servizio

Per ogni evento 'Stop' esiste una proprietà `Status` con l'operazione asincrona `TaskStatus` con cui è stata completata. Per semplicità anche tale proprietà non figura nella tabella seguente.

Ecco l'elenco completo delle operazioni instrumentate:

| Nome operazione | API verificata | Proprietà del payload specifiche|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> Messages: elenco dei messaggi da inviare |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Message Message: messaggio da elaborare<br/>DateTimeOffset ScheduleEnqueueTimeUtc: offset del messaggio pianificato<br/>long SequenceNumber: numero di sequenza del messaggio pianificato (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | long SequenceNumber: numero di sequenza del messaggio da annullare | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount: numero massimo di messaggi che è possibile ricevere<br/>IList<Message> Messages: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber: punto di partenza da cui sfogliare un batch di messaggi<br/>int RequestedMessageCount: numero di messaggi da recuperare<br/>IList<Message> Messages: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers: elenco contenente i numeri di sequenza da ricevere<br/>IList<Message> Messages: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens: elenco contenente i token di blocco dei messaggi corrispondenti da completare|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | string LockToken: token di blocco del messaggio corrispondente da abbandonare |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | string LockToken: token di blocco del messaggio corrispondente da rinviare | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | string LockToken: token di blocco del messaggio corrispondente da inserire nella coda di messaggi non recapitabili | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | string LockToken: token di blocco del messaggio corrispondente da inserire per cui rinnovare il blocco<br/>DateTime LockedUntilUtc: nuova data e ora di scadenza del token di blocco in formato UTC (payload dell'evento 'Stop')|
| Microsoft.Azure.ServiceBus.Process | Funzione lambda del gestore di messaggi fornita in [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Message Message: messaggio da elaborare |
| Microsoft.Azure.ServiceBus.ProcessSession | Funzione lambda del gestore della sessione di messaggi fornita in [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Message Message: messaggio da elaborare<br/>IMessageSession Session: sessione da elaborare |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription Rule: descrizione della regola che fornisce la regola da aggiungere |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | string RuleName: nome della regola da rimuovere |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> Rules: tutte le regole associate alla sottoscrizione (solo payload di 'Stop') |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | string SessionId: elemento sessionId presente nei messaggi |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | string SessionId: elemento sessionId presente nei messaggi<br/>byte [] State: stato della sessione (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | string SessionId: elemento sessionId presente nei messaggi<br/>byte [] State: stato della sessione |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | string SessionId: elemento sessionId presente nei messaggi |
| Microsoft.Azure.ServiceBus.Exception | qualsiasi API instrumentata| Exception Exception: istanza di Exception |

In ogni evento è possibile accedere a `Activity.Current` che contiene il contesto dell'operazione

#### <a name="logging-additional-properties"></a>Registrazione di proprietà aggiuntive

`Activty.Current` fornisce il contesto dettagliato dell'operazione corrente e i relativi elementi padre. Per altre informazioni, vedere la [documentazione di Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
La strumentazione del bus di servizio fornisce informazioni aggiuntive nei tag `Activity.Current.Tags`, che contengono `MessageId` e `SessionId` qualora disponibili.

Anche le attività che verificano gli eventi 'Receive', 'Peek' e 'ReceiveDeferred' possono contenere il tag `RelatedTo`. Tale tag contiene un elenco distinto di elementi `Diagnostic-Id` di messaggi che sono stati ricevuti come risultato.
Tale operazione può causare la ricezione di numerosi messaggi non correlati. L'elemento `Diagnostic-Id`, inoltre, non è noto all'inizio dell'operazione, di conseguenza le operazioni di tipo 'Receive' potrebbero essere correlate a operazioni di tipo 'Process' solo usando questo tag. Il tag è utile quando si analizzano problemi di prestazioni per verificare il tempo impiegato per la ricezione del messaggio.

Un modo efficiente per registrare i tag consiste nell'eseguire l'iterazione su di essi. Ecco come aggiungere i tag all'esempio precedente 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtro e campionamento

In alcuni casi è consigliabile registrare solo parte degli eventi per ridurre il consumo dello spazio di archiviazione o il sovraccarico delle prestazioni. È possibile registrare solo gli eventi di tipo 'Stop' (come nell'esempio precedente) o una percentuale campione degli eventi. 
A questo scopo, è possibile usare `DiagnosticSource` con il predicato `IsEnabled`. Per altre informazioni, vedere [Context-Based Filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) (Filtro in base al contesto in DiagnosticSource).

Per ridurre al minimo l'impatto sulle prestazioni, è possibile chiamare `IsEnabled` più volte per una singola operazione.

`IsEnabled` viene chiamato nella sequenza seguente:

1. `IsEnabled(<OperationName>, string entity, null)` ad esempio, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Si noti che alla fine non è presente alcun evento di tipo 'Start' o 'Stop'. Usarlo per escludere determinate operazioni o code. Se il callback restituisce `false`, gli eventi per l'operazione non vengono inviati.

  * Per le operazioni di tipo 'Process' e 'ProcessSession' viene ricevuto anche il callback `IsEnabled(<OperationName>, string entity, Activity activity)`. Usarlo per filtrare gli eventi in base alle proprietà `activity.Id` o Tags.
  
2. `IsEnabled(<OperationName>.Start)` ad esempio, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Controlla se deve essere attivato l'evento 'Start'. Il risultato interessa solo l'evento 'Start', ma l'ulteriore strumentazione non dipende da esso.

Non esiste alcun elemento `IsEnabled` per l'evento 'Stop'.

Se il risultato di alcune operazioni è un'eccezione, viene chiamato `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`. È possibile eseguire la sottoscrizione solo di eventi 'Exception' e impedire il resto della strumentazione. In questo caso sarà comunque necessario gestire tali eccezioni. Dal momento che l'altra strumentazione è disabilitata, il contesto di analisi con i messaggi non verrà trasmesso dal consumer al producer.

È possibile usare `IsEnabled` anche per implementare strategie di campionamento. Il campionamento basato su `Activity.Id` o su `Activity.RootId` assicura il campionamento coerente in tutti i livelli (purché venga propagato dal sistema di analisi o da codice personalizzato).

In presenza di più listener `DiagnosticSource` per la stessa origine, basta che l'evento sia accettato da un solo listener, di conseguenza non è garantito che venga chiamato `IsEnabled`.

## <a name="next-steps"></a>Passaggi successivi

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Correlazione di dati di telemetria in Application Insights](../application-insights/application-insights-correlation.md)
* [Impostare Application Insights: tenere traccia delle dipendenze](../application-insights/app-insights-asp-net-dependencies.md) per verificare se REST, SQL o altre risorse esterne causano rallentamenti.
* [Verifica delle operazioni personalizzate con Application Insights .NET SDK](../application-insights/application-insights-custom-operations-tracking.md)
