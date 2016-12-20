---
title: Trigger e associazioni di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare i trigger e le associazioni in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: d809636cae48dd0ccca4c99370f41996430d89e4
ms.openlocfilehash: b5d8d38f03514d89bf6c0b5e36adf0379f1bef1a


---
# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Guida di riferimento per gli sviluppatori di trigger e associazioni di Funzioni di Azure
In questo argomento vengono fornite informazioni di riferimento generali per trigger e associazioni. Sono incluse alcune delle funzionalità di associazione avanzate e la sintassi supportate da tutti i tipi di associazione.  

Se si è in cerca di informazioni dettagliate su configurazione e codifica di un tipo specifico di trigger o associazione, fare clic su uno dei trigger o delle associazioni riportati di seguito:

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questi articoli si presuppone che siano stati letti la [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md) e gli articoli di riferimento per gli sviluppatori di [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) o [Node.js](functions-reference-node.md).

## <a name="overview"></a>Panoramica
I trigger sono risposte agli eventi usate per attivare il codice personalizzato. Consentono di rispondere agli eventi nella piattaforma Azure o in locale. Le associazioni rappresentano i metadati necessari per connettere il codice al trigger desiderato o ai dati di input o output associati. Il file *function.json* per ogni funzione contiene tutte le relative associazioni. Non esiste alcun limite al numero di associazioni di input e output che può avere una funzione. Tuttavia, per ogni funzione è supportata solo una singola associazione del trigger.  

Per capire meglio le diverse associazioni che è possibile integrare con l'app per le funzioni di Azure, fare riferimento alla tabella seguente.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Per comprendere meglio i trigger e le associazioni in generale, si supponga di voler eseguire un codice per elaborare un nuovo elemento rilasciato in una coda di Archiviazione di Azure. A questo scopo, Funzioni di Azure fornisce un trigger di coda di Azure. Per monitorare la coda sono necessarie le informazioni seguenti:

* L'account di archiviazione in cui esiste la coda.
* Il nome della coda.
* Un nome di variabile che verrà usato dal codice per fare riferimento al nuovo elemento rilasciato nella coda.  

Un'associazione di trigger della coda contiene queste informazioni per una funzione di Azure. Di seguito è riportato un esempio *function.json* contenente un'associazione di trigger della coda. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

Il codice può inviare diversi tipi di output a seconda della modalità di elaborazione del nuovo elemento della coda. Ad esempio, si potrebbe voler scrivere un nuovo record in una tabella di Archiviazione di Azure.  A tale scopo, è possibile configurare un'associazione di output a una tabella di Archiviazione di Azure. Di seguito è riportato un esempio *function.json* che include un'associazione di output nella tabella di archiviazione che può essere usata con un trigger della coda. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La funzione C# seguente risponde al rilascio di un nuovo elemento nella coda e scrive un nuovo elemento dell'utente in una tabella di Archiviazione di Azure.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

Per altri esempi di codice e informazioni più specifiche sui tipi di archiviazione di Azure supportati, vedere [Trigger e associazioni di Archiviazione di Azure in Funzioni di Azure](functions-bindings-storage.md).

Per usare le funzionalità di associazione più avanzate nel portale di Azure, fare clic sull'opzione **Editor avanzato** nella scheda **Integra** della funzione. L'editor avanzato consente di modificare *function.json* direttamente nel portale.

## <a name="random-guids"></a>GUID casuali
Funzioni di Azure fornisce una sintassi per generare GUID casuali con le associazioni. La sintassi di associazione seguente scriverà l'output in un nuovo BLOB con un nome univoco in un contenitore di Archiviazione di Azure: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>Restituzione di un output singolo
Nei casi in cui il codice della funzione restituisce un output singolo, è possibile usare un'associazione di output di nome `$return` per mantenere una firma più naturale della funzione nel codice. Tale associazione può essere usata solo con i linguaggi che supportano un valore restituito (C#, Node.js, F#). L'associazione sarà simile all'associazione di output BLOB seguente che viene usata con un trigger della coda.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

Il codice C# seguente restituisce l'output in modo più naturale, senza usare un parametro `out` nella firma della funzione.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

Esempio asincrono:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


Di seguito è illustrato lo stesso approccio con Node.js.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

Di seguito è mostrato un esempio di F#.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

Può essere usato anche con più parametri di output, definendo un output singolo con `$return`.

## <a name="resolving-app-settings"></a>Risoluzione di impostazioni app
È consigliabile archiviare i dati sensibili come parte dell'ambiente di runtime usando le impostazioni app. Mantenendo le informazioni all'esterno dei file di configurazione dell'applicazione, si limita l'esposizione quando si usa un repository pubblico per archiviare i file app.  

Il runtime di Funzioni di Azure risolve le impostazioni app in valori quando il nome dell'impostazione app è racchiusa tra segni di percentuale, `%your app setting%`. La seguente [associazione Twilio](functions-bindings-twilio.md) usa un'impostazione app denominata `TWILIO_ACCT_PHONE` per il campo `from` dell'associazione. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>Associazione di parametri
Invece di un'impostazione di configurazione statica per le proprietà di associazione dell'output, è possibile configurare le impostazioni in modo da essere associate dinamicamente ai dati come parte dell'associazione di input del trigger. Si consideri uno scenario in cui vengono elaborati nuovi ordini tramite una coda di Archiviazione di Azure. Ogni nuovo elemento della coda è una stringa JSON contenente almeno le proprietà seguenti:

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address".
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Si potrebbe inviare al cliente un SMS tramite il proprio account Twilio come aggiornamento per confermare la ricezione dell'ordine.  È possibile configurare i campi `body` e `to` dell'associazione dell'output Twilio in modo che sia associata dinamicamente a `name` e `mobileNumber`, che facevano parte dell'input, come mostrato di seguito.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

A questo punto il codice della funzione deve solo inizializzare il parametro di output come mostrato di seguito. Durante l'esecuzione, le proprietà di output verranno associate ai dati di input desiderati.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Associazione avanzata in fase di esecuzione (associazione imperativa)

Il modello standard di associazione di input e output che usa *function.json* viene chiamato associazione [*dichiarativa*](https://en.wikipedia.org/wiki/Declarative_programming), in cui l'associazione viene definita dalla dichiarazione JSON. Tuttavia, è possibile usare l'associazione [imperativa](https://en.wikipedia.org/wiki/Imperative_programming). Con questo modello è possibile associare rapidamente i dati a qualsiasi numero di associazioni di input e output supportate nel codice della funzione.
L'associazione imperativa può essere necessaria nei casi in cui il calcolo del percorso di associazione o di altri input deve avvenire in fase di esecuzione nella funzione anziché in fase di progettazione. 

Per eseguire l'associazione imperativa, procedere come segue:

- **Non** includere una voce in *function.json* per le associazioni imperative da eseguire.
- Passare un parametro di input [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) o [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Usare il seguente modello C# per eseguire l'associazione dati.

        using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
        {
                ...
        }

dove `BindingTypeAttribute` è l'attributo .NET che definisce l'associazione e `T` è il tipo di input o output supportato da quel tipo di associazione. `T` non può essere un tipo di parametro `out`, ad esempio `out JObject`. L'associazione di output della tabella App per dispositivi mobili, ad esempio, supporta[ sei tipi di output](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ma è possibile usare solo [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) o [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) per `T`.
    
L'esempio di codice seguente crea un [associazione di output del BLOB di archiviazione](functions-bindings-storage-blob.md#storage-blob-output-binding) con percorso del BLOB definito in fase di esecuzione, quindi scrive una stringa per il BLOB.

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
                {
                        writer.Write("Hello World!!");
                }
        }

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definisce l'associazione di input o output del [BLOB di archiviazione](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) è un tipo di associazione di output supportato.
Ovvero, il codice ottiene l'impostazione app predefinita per la stringa di connessione dell'account di archiviazione (`AzureWebJobsStorage`). È possibile specificare un'impostazione app personalizzata da usare aggiungendo [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando la matrice di attributi in `BindAsync<T>()`. Ad esempio,

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                var attributes = new Attribute[]
                {
                        new BlobAttribute("samples-output/path"),
                        new StorageAccountAttribute("MyStorageAccount")
                };
                using (var writer = await binder.BindAsync<TextWriter>(attributes))
                {
                        writer.Write("Hello World!");
                }
        }

La tabella seguente indica l'attributo .NET corrispondente da usare per ogni tipo di associazione e il pacchetto di riferimento.

| Associazione | Attributo | Aggiungi riferimento |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Hub eventi | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| App per dispositivi mobili | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Hub di notifica | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Bus di servizio | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Coda di archiviazione | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| BLOB di archiviazione | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabella di archiviazione | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions"` |



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Test di Funzioni di Azure](functions-test-a-function.md)
* [Scalabilità di Funzioni di Azure](functions-scale.md)




<!--HONumber=Nov16_HO3-->


