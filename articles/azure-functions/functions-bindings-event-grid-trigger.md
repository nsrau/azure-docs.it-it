---
title: Trigger di griglia di eventi di Azure per funzioni di Azure
description: Informazioni su come eseguire il codice quando si inviano eventi di griglia di eventi in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368961"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Trigger di griglia di eventi di Azure per funzioni di Azure

Usare il trigger di funzione per rispondere a un evento inviato a un argomento di griglia di eventi.

Per informazioni sui dettagli di configurazione e configurazione, vedere la [Panoramica](./functions-bindings-event-grid.md).

## <a name="example"></a>Esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Per un esempio di trigger HTTP, vedere [ricevere eventi in un endpoint HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x e versioni successive)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) associata a `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Per altre informazioni, consultare le sezioni Pacchetti, [Attributi](#attributes-and-annotations), [Configurazione](#configuration) e [Uso](#usage).

### <a name="version-1x"></a>Versione 1.x

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) di Funzioni 1.x associata a `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Versione 2. x e successive

Di seguito è riportato un esempio che associa a `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Per altre informazioni, consultare le sezioni Pacchetti, [Attributi](#attributes-and-annotations), [Configurazione](#configuration) e [Uso](#usage).

### <a name="version-1x"></a>Versione 1.x

Ecco il codice script C# di Funzioni 1.x associato a un oggetto `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Ecco il codice Python:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Questa sezione contiene gli esempi seguenti:

* [Trigger griglia di eventi, parametro String](#event-grid-trigger-string-parameter)
* [Trigger griglia di eventi, parametro POJO](#event-grid-trigger-pojo-parameter)

Negli esempi seguenti viene illustrata l'associazione di trigger in [Java](functions-reference-java.md) che usa l'associazione e stampa un evento, ricevendo prima l'evento come `String` e secondo come POJO.

### <a name="event-grid-trigger-string-parameter"></a>Trigger griglia di eventi, parametro stringa

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Trigger griglia di eventi, parametro POJO

Questo esempio usa il POJO seguente, che rappresenta le proprietà di livello superiore di un evento Griglia di eventi:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

All'arrivo, il payload JSON dell'evento viene deserializzato nel POJO ```EventSchema``` per l'uso nella funzione. Questo processo consente alla funzione di accedere alle proprietà dell'evento in modo orientato agli oggetti.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `EventGridTrigger` per i parametri il cui valore deriva da EventGrid. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs).

Di seguito è mostrato un attributo `EventGridTrigger` in una firma del metodo:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere l'esempio in C#.

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L'annotazione [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) consente di configurare in modo dichiarativo un'associazione di griglia di eventi fornendo valori di configurazione. Per altri dettagli, vedere le sezioni di [esempio](#example) e di [configurazione](#configuration) .

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*. Non sono presenti parametri o proprietà di costruttori da impostare nell'attributo `EventGridTrigger`.

|Proprietà di function.json |Descrizione|
|---------|---------|
| **type** | Obbligatoria. Deve essere impostata su `eventGridTrigger`. |
| **direction** | Obbligatoria. Deve essere impostata su `in`. |
| **nome** | Obbligatoria: nome della variabile usato nel codice della funzione per il parametro che riceve i dati dell'evento. |

## <a name="usage"></a>Uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In funzioni di Azure 1. x, è possibile usare i tipi di parametro seguenti per il trigger griglia di eventi:

* `JObject`
* `string`

In funzioni di Azure 2. x e versioni successive è anche possibile usare il tipo di parametro seguente per il trigger griglia di eventi:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - Definisce le proprietà dei campi comuni a tutti i tipi di evento.

> [!NOTE]
> In Funzioni v1, se si prova a eseguire l'associazione a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, il compilatore mostrerà un messaggio che segnala che il parametro è deprecato e suggerisce di usare `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Per usare il tipo più recente, fare riferimento al pacchetto NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) e specificare il nome completo del tipo `EventGridEvent` anteponendo il prefisso `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

In funzioni di Azure 1. x, è possibile usare i tipi di parametro seguenti per il trigger griglia di eventi:

* `JObject`
* `string`

In funzioni di Azure 2. x e versioni successive è anche possibile usare il tipo di parametro seguente per il trigger griglia di eventi:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - Definisce le proprietà dei campi comuni a tutti i tipi di evento.

> [!NOTE]
> In Funzioni v1, se si prova a eseguire l'associazione a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, il compilatore mostrerà un messaggio che segnala che il parametro è deprecato e suggerisce di usare `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Per usare il tipo più recente, fare riferimento al pacchetto NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) e specificare il nome completo del tipo `EventGridEvent` anteponendo il prefisso `Microsoft.Azure.EventGrid.Models`. Per informazioni su come fare riferimento a pacchetti NuGet in una funzione script C#, vedere [Uso dei pacchetti NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'istanza di griglia di eventi è disponibile tramite il parametro configurato nella proprietà `name` del file *Function. JSON* .

# <a name="pythontabpython"></a>[Python](#tab/python)

L'istanza di griglia di eventi è disponibile tramite il parametro configurato nella proprietà `name` del file *Function. JSON* , tipizzata come `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

L'istanza dell'evento di griglia di eventi è disponibile tramite il parametro associato all'attributo `EventGridTrigger`, tipizzato come `EventSchema`. Per altri dettagli, vedere l' [esempio](#example) .

---

## <a name="event-schema"></a>Schema di eventi

I dati di un evento di Griglia di eventi vengono trasmessi come oggetto JSON nel corpo di una richiesta HTTP. L'oggetto JSON sarà simile all'esempio seguente:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

L'esempio illustrato rappresenta una matrice di un elemento. Griglia di eventi invia sempre una matrice e può inviare più eventi nella matrice. Il runtime richiama la funzione una volta per ogni elemento della matrice.

Le proprietà di livello superiore nei dati JSON dell'evento sono uguali per tutti i tipi di evento, mentre il contenuto della proprietà `data` è specifico per ogni tipo di evento. L'esempio illustrato si riferisce a un evento di archiviazione BLOB.

Per altre informazioni sulle proprietà comuni e specifiche degli eventi, vedere [Proprietà degli eventi](../event-grid/event-schema.md#event-properties) nella documentazione relativa a Griglia di eventi.

Il tipo `EventGridEvent` definisce solo le proprietà di livello superiore, mentre la proprietà `Data` è un elemento `JObject`.

## <a name="create-a-subscription"></a>Creare una sottoscrizione

Per iniziare a ricevere richieste HTTP di Griglia di eventi, è necessario creare una sottoscrizione di Griglia di eventi in cui sia specificato l'URL dell'endpoint che richiama la funzione.

### <a name="azure-portal"></a>Portale di Azure

Per le funzioni sviluppate nel portale di Azure con il trigger Griglia di eventi, selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

![Creare una sottoscrizione nel portale](media/functions-bindings-event-grid/portal-sub-create.png)

Quando si seleziona questo collegamento, nel portale si apre la pagina **Crea sottoscrizione di eventi** con l'URL dell'endpoint precompilato.

![URL dell'endpoint precompilato](media/functions-bindings-event-grid/endpoint-url.png)

Per altre informazioni su come creare sottoscrizioni tramite il portale di Azure, vedere [Creare eventi personalizzati con il portale di Azure](../event-grid/custom-event-quickstart-portal.md) nella documentazione relativa a Griglia di eventi.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per creare una sottoscrizione tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), usare il comando [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create).

Il comando richiede l'URL dell'endpoint che richiama la funzione. L'esempio seguente illustra il modello di URL specifico della versione:

#### <a name="version-2x-and-higher-runtime"></a>Runtime versione 2. x (e versioni successive)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Versione di runtime 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

La chiave di sistema è una chiave di autorizzazione che deve essere inclusa nell'URL dell'endpoint di un trigger Griglia di eventi. La sezione seguente spiega come ottenere la chiave di sistema.

Di seguito è riportato un esempio di sottoscrizione a un account di archiviazione BLOB (con un segnaposto per la chiave di sistema):

#### <a name="version-2x-and-higher-runtime"></a>Runtime versione 2. x (e versioni successive)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Versione di runtime 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Per altre informazioni su come creare una sottoscrizione, vedere la [guida introduttiva all'archiviazione BLOB](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) o le altre guide introduttive a Griglia di eventi.

### <a name="get-the-system-key"></a>Ottenere la chiave di sistema

È possibile ottenere la chiave di sistema tramite l'API seguente (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Runtime versione 2. x (e versioni successive)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versione di runtime 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Poiché si tratta di un'API di amministrazione, è necessaria la [chiave master](functions-bindings-http-webhook-trigger.md#authorization-keys) dell'app per le funzioni. Non confondere la chiave di sistema (per richiamare una funzione trigger Griglia di eventi) con la chiave master (per l'esecuzione di attività amministrative nell'app per le funzioni). Quando si sottoscrive un argomento di Griglia di eventi, usare la chiave di sistema.

Di seguito è riportato un esempio di risposta fornita dalla chiave di sistema:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

È possibile ottenere la chiave master per l'app per le funzioni dalla scheda **Impostazioni dell'app per le funzioni** nel portale.

> [!IMPORTANT]
> La chiave master consente l'accesso di amministratore all'app per le funzioni. Non condividere questa chiave con terze parti né distribuirla in applicazioni client native.

Per altre informazioni, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys) nell'articolo di riferimento relativo al trigger HTTP.

In alternativa, è possibile inviare una richiesta HTTP PUT per specificare il valore della chiave autonomamente.

## <a name="local-testing-with-viewer-web-app"></a>Test locale con l'app visualizzatore Web

Per testare un trigger Griglia di eventi in locale, è necessario ottenere le richieste HTTP di Griglia di eventi inviate dalla rispettiva origine nel cloud al computer locale. A tale scopo, è possibile acquisire le richieste online e rinviarle manualmente al computer locale:

1. [Creare un'app visualizzatore Web](#create-a-viewer-web-app) che acquisisce i messaggi di evento.
1. [Creare una sottoscrizione di Griglia di eventi](#create-an-event-grid-subscription) per inviare gli eventi all'app visualizzatore.
1. [Generare una richiesta](#generate-a-request) e copiare il corpo della richiesta dall'app visualizzatore.
1. [Inviare manualmente la richiesta](#manually-post-the-request) all'URL localhost della funzione trigger Griglia di eventi.

Al termine del test, è possibile usare la stessa sottoscrizione per scopi di produzione aggiornando l'endpoint. Usare il comando dell'interfaccia della riga di comando di Azure [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update).

### <a name="create-a-viewer-web-app"></a>Creare un'app visualizzatore Web

Per semplificare l'acquisizione di messaggi di evento, è possibile distribuire un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

Selezionare **Distribuisci in Azure** per distribuire la soluzione nella sottoscrizione. Nel portale di Azure specificare i valori per i parametri.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Viene visualizzato il sito, ma nessun evento è ancora stato pubblicato.

![Visualizzare il nuovo sito](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Creare una sottoscrizione di Griglia di eventi

Creare una sottoscrizione di Griglia di eventi della tipologia da testare e assegnargli l'URL dall'app Web come endpoint per la notifica degli eventi. L'endpoint per l'app Web deve includere il suffisso `/api/updates/`. Pertanto, l'URL completo è `https://<your-site-name>.azurewebsites.net/api/updates`

Per altre informazioni su come creare sottoscrizioni tramite il portale di Azure, vedere [Creare eventi personalizzati con il portale di Azure](../event-grid/custom-event-quickstart-portal.md) nella documentazione relativa a Griglia di eventi.

### <a name="generate-a-request"></a>Generare una richiesta

Attivare un evento che genera traffico HTTP nell'app Web.  Ad esempio, se è stata creata una sottoscrizione di archiviazione BLOB, caricare o eliminare un BLOB. Quando nell'app Web viene visualizzata una richiesta, copiare il corpo della richiesta.

Si riceverà prima la richiesta di convalida della sottoscrizione. Ignorare tutte le richieste di convalida e copiare la richiesta di evento.

![Copiare il corpo della richiesta dall'app Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Inviare manualmente la richiesta

Eseguire la funzione di Griglia di eventi in locale.

Usare uno strumento, ad esempio [Postman](https://www.getpostman.com/) o [curl](https://curl.haxx.se/docs/httpscripting.html), per creare una richiesta HTTP POST:

* Impostare un'intestazione `Content-Type: application/json`.
* Impostare un'intestazione `aeg-event-type: Notification`.
* Incollare i dati di RequestBin nel corpo della richiesta.
* Inserire l'URL della funzione trigger griglia di eventi.
  * Per 2. x e versioni successive, usare il modello seguente:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Per 1. x usare:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Il parametro `functionName` deve corrispondere al nome specificato nell'attributo `FunctionName`.

Le schermate seguenti illustrano le intestazioni e il corpo della richiesta in Postman:

![Intestazioni in Postman](media/functions-bindings-event-grid/postman2.png)

![Corpo della richiesta in Postman](media/functions-bindings-event-grid/postman.png)

La funzione trigger Griglia di eventi viene eseguita e vengono visualizzati log simili all'esempio seguente:

![Log di esempio della funzione trigger Griglia di eventi](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Passaggi successivi

* [Inviare un evento di griglia di eventi](./functions-bindings-event-grid-trigger.md)
