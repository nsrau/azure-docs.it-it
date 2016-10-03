<properties
	pageTitle="Trigger e associazioni del bus di servizio di Azure di Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare trigger e associazioni del bus di servizio di Azure in Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server"/> 

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/> 

# Trigger e associazioni del bus di servizio per code e argomenti in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice per trigger e associazioni del bus di servizio di Azure in Funzioni di Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> Trigger del bus di servizio per coda o argomento

#### function.json

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per la coda o l'argomento o per il messaggio della coda o dell'argomento.
- `queueName`: solo per il trigger della coda, il nome della coda in cui eseguire il polling.
- `topicName`: solo per il trigger dell'argomento, il nome dell'argomento in cui eseguire il polling.
- `subscriptionName`: solo per il trigger dell'argomento, il nome della sottoscrizione.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione del bus di servizio. La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico. Se la stringa di connessione non ha diritti di gestione, impostare la proprietà `accessRights`. Se si lascia vuota la proprietà `connection`, il trigger o l'associazione usano la stringa di connessione del bus di servizio predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsServiceBus.
- `accessRights`: specifica i diritti di accesso disponibili per la stringa di connessione. Il valore predefinito è `manage`. Impostare su `listen` se si usa una stringa di connessione che non offre autorizzazioni di gestione. In caso contrario il runtime di Funzioni potrebbe provare senza successo a eseguire operazioni che richiedono diritti di gestione.
- `type`: deve essere impostato su *serviceBusTrigger*.
- `direction`: deve essere impostato su *in*.

Esempio di *function.json* per un trigger del bus di servizio per la coda:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Esempio di codice C# che elabora un messaggio di coda del bus di servizio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Esempio di codice F# che elabora un messaggio della coda del bus di servizio

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### Esempio di codice Node.js che elabora un messaggio di coda del bus di servizio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### Tipi supportati

Il messaggio di coda del bus di servizio può essere deserializzato in uno qualsiasi dei seguenti tipi:

* Object (da JSON)
* stringa
* matrice di byte
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> Comportamento di PeekLock

Il runtime di Funzioni riceve un messaggio in modalità `PeekLock` e chiama `Complete` sul messaggio se la funzione viene completata correttamente oppure `Abandon` se la funzione ha esito negativo. Se il tempo di esecuzione della funzione supera il timeout di `PeekLock`, il blocco viene rinnovato automaticamente.

#### <a id="sbpoison"></a> Gestione dei messaggi non elaborabili

Il bus di servizio esegue la gestione dei messaggi non elaborabili in modo autonomo, non controllabile o modificabile nella configurazione o nel codice di Funzioni di Azure.

#### <a id="sbsinglethread"></a> Single threading

Per impostazione predefinita il runtime di Funzioni elabora più messaggi della coda contemporaneamente. Per impostare il runtime in modo che elabori un solo messaggio della coda o dell'argomento alla volta, impostare `serviceBus.maxConcurrrentCalls` su 1 nel file *host.json*. Per informazioni sul file *host.json*, vedere [Struttura di cartelle](functions-reference.md#folder-structure) nell'articolo Guida di riferimento per gli sviluppatori e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) nel wiki del repository WebJobs.Script.

## <a id="sboutput"></a> Associazione di output del bus di servizio per coda o argomento

#### function.json

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda.
- `queueName`: solo per il trigger della coda, il nome della coda in cui eseguire il polling.
- `topicName`: solo per il trigger dell'argomento, il nome dell'argomento in cui eseguire il polling.
- `subscriptionName`: solo per il trigger dell'argomento, il nome della sottoscrizione.
- `connection`: identico al trigger del bus di servizio.
- `accessRights`: specifica i diritti di accesso disponibili per la stringa di connessione. Il valore predefinito è `manage`. Impostare su `send` se si usa una stringa di connessione che non offre autorizzazioni di gestione. In caso contrario il runtime di Funzioni potrebbe provare senza successo a eseguire operazioni che richiedono diritti di gestione, ad esempio la creazione di code.
- `type`: deve essere impostato su *serviceBus*.
- `direction`: deve essere impostato su *out*.

Esempio di *function.json* che usa un trigger timer per scrivere messaggi di coda del bus di servizio:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipi supportati

Funzioni di Azure può creare un messaggio di coda del bus di servizio da uno qualsiasi dei tipi seguenti.

* Object (crea sempre un messaggio JSON; crea il messaggio con un oggetto null se il valore è null quando la funzione termina)
* string (crea il messaggio della coda se il valore è diverso da null quando la funzione termina)
* matrice di byte (funziona come string)
* `BrokeredMessage` (C#, funziona come string)

Per la creazione di più messaggi in una funzione C# è possibile usare `ICollector<T>` o `IAsyncCollector<T>`. Quando si chiama il metodo `Add` viene creato un messaggio.

#### Esempio di codice C# che crea messaggi di coda del bus di servizio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Esempio di codice F# che crea un messaggio della coda del bus di servizio

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### Esempio di codice Node.js che crea un messaggio di coda del bus di servizio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->