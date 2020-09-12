---
title: Gestori personalizzati di funzioni di Azure (anteprima)
description: Informazioni su come usare funzioni di Azure con qualsiasi linguaggio o versione di Runtime.
author: anthonychu
ms.author: antchu
ms.date: 8/18/2020
ms.topic: article
ms.openlocfilehash: f3106553def982eb90ccc90822206e75a11ce354
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294595"
---
# <a name="azure-functions-custom-handlers-preview"></a>Gestori personalizzati di funzioni di Azure (anteprima)

Ogni app per le funzioni viene eseguita da un gestore specifico della lingua. Sebbene funzioni di Azure supporti molti [gestori di linguaggio](./supported-languages.md) per impostazione predefinita, in alcuni casi potrebbe essere necessario usare altri linguaggi o Runtime.

I gestori personalizzati sono server Web leggeri che ricevono eventi dall'host di funzioni. Qualsiasi linguaggio che supporta le primitive HTTP può implementare un gestore personalizzato.

I gestori personalizzati sono ideali per le situazioni in cui si desidera:

- Implementare un'app per le funzioni in una lingua attualmente non supportata, ad esempio go e Rust.
- Implementare un'app per le funzioni in un runtime che non è attualmente supportato, ad esempio deno.

Con i gestori personalizzati, è possibile usare i [trigger e le associazioni di input e output](./functions-triggers-bindings.md) tramite i [bundle di estensione](./functions-bindings-register.md).

## <a name="overview"></a>Panoramica

Nel diagramma seguente viene illustrata la relazione tra l'host di funzioni e un server Web implementato come gestore personalizzato.

![Panoramica del gestore personalizzato di funzioni di Azure](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Ogni evento attiva una richiesta inviata all'host di funzioni. Un evento è qualsiasi trigger supportato da funzioni di Azure.
1. L'host Functions invia quindi un [payload della richiesta](#request-payload) al server Web. Il payload include i dati di associazione di trigger e input e altri metadati per la funzione.
1. Il server Web esegue la singola funzione e restituisce un payload di [risposta](#response-payload) all'host di funzioni.
1. L'host di funzioni passa i dati dalla risposta alle associazioni di output della funzione per l'elaborazione.

Un'app funzioni di Azure implementata come gestore personalizzato deve configurare la *host.json*, *local.settings.js*e *function.jsnei file in* base a alcune convenzioni.

## <a name="application-structure"></a>Struttura dell'applicazione

Per implementare un gestore personalizzato, per l'applicazione sono necessari gli aspetti seguenti:

- Un *host.jssul* file alla radice dell'app
- Un *local.settings.jssul* file alla radice dell'app
- *function.jssu* file per ogni funzione (all'interno di una cartella che corrisponde al nome della funzione)
- Un comando, uno script o un file eseguibile che esegue un server Web

Il diagramma seguente illustra il modo in cui questi file esaminano la file system per una funzione denominata "MyQueueFunction" e un eseguibile di un gestore personalizzato denominato *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Configurazione

L'applicazione viene configurata tramite il *host.js* e *local.settings.jssui* file.

#### <a name="hostjson"></a>host.json

*host.jssu* indica all'host di funzioni dove inviare le richieste puntando a un server Web in grado di elaborare eventi http.

Un gestore personalizzato viene definito configurando il *host.jssu* file con i dettagli su come eseguire il server Web tramite la `customHandler` sezione.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

La `customHandler` sezione punta a una destinazione in base a quanto definito dall'oggetto `defaultExecutablePath` . La destinazione di esecuzione può essere un comando, un eseguibile o un file in cui è implementato il server Web.

Utilizzare la `arguments` matrice per passare gli argomenti al file eseguibile. Gli argomenti supportano l'espansione delle variabili di ambiente (impostazioni dell'applicazione) con la `%%` notazione.

È inoltre possibile modificare la directory di lavoro utilizzata dal file eseguibile con `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Supporto di binding

I trigger standard insieme alle associazioni di input e di output sono disponibili facendo riferimento ai [bundle di estensione](./functions-bindings-register.md) nel *host.jssu* file.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* definisce le impostazioni dell'applicazione usate durante l'esecuzione locale dell'app per le funzioni. Poiché può contenere segreti, *local.settings.jssu* deve essere escluso dal controllo del codice sorgente. In Azure usare invece le impostazioni dell'applicazione.

Per i gestori personalizzati, impostare `FUNCTIONS_WORKER_RUNTIME` `Custom` su in *local.settings.json*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` potrebbe non essere riconosciuto come runtime valido nei piani di servizio app o Premium Linux. Se si tratta della destinazione di distribuzione, impostare `FUNCTIONS_WORKER_RUNTIME` su una stringa vuota.

### <a name="function-metadata"></a>Metadati della funzione

Quando viene usato con un gestore personalizzato, i *function.jssul* contenuto non sono diversi da come definire una funzione in qualsiasi altro contesto. L'unico requisito è che *function.js* nei file deve trovarsi in una cartella denominata in modo che corrisponda al nome della funzione.

Nell' *function.js* seguente viene configurata una funzione che include un trigger della coda e un'associazione di output della coda. Poiché si trova in una cartella denominata *MyQueueFunction*, definisce una funzione denominata *MyQueueFunction*.

**MyQueueFunction/function.js**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Payload della richiesta

Quando viene ricevuto un messaggio in coda, l'host di funzioni Invia una richiesta HTTP post al gestore personalizzato con un payload nel corpo.

Il codice seguente rappresenta un payload di richiesta di esempio. Il payload include una struttura JSON con due membri: `Data` e `Metadata` .

Il `Data` membro include chiavi che corrispondono ai nomi di input e trigger, come definito nella matrice bindings nel *function.jssu* file.

Il `Metadata` membro include i [metadati generati dall'origine evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Payload della risposta

Per convenzione, le risposte di funzione sono formattate come coppie chiave/valore. Le chiavi supportate includono:

| <nobr>Chiave payload</nobr>   | Tipo di dati | Commenti                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | Include i valori di risposta definiti dalla `bindings` matrice in *function.json*.<br /><br />Ad esempio, se una funzione è configurata con un'associazione di output della coda denominata "myQueueOutput", `Outputs` contiene una chiave denominata `myQueueOutput` , che viene impostata dal gestore personalizzato per i messaggi inviati alla coda. |
| `Logs`        | array     | I messaggi vengono visualizzati nei log di chiamata delle funzioni.<br /><br />Quando è in esecuzione in Azure, i messaggi vengono visualizzati in Application Insights. |
| `ReturnValue` | string    | Utilizzato per fornire una risposta quando un output viene configurato come `$return` nell' *function.jssu* file. |

Questo è un esempio di payload di risposta.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Esempi

I gestori personalizzati possono essere implementati in qualsiasi linguaggio che supporti la ricezione di eventi HTTP. Negli esempi seguenti viene illustrato come implementare un gestore personalizzato utilizzando il linguaggio di programmazione go.

### <a name="function-with-bindings"></a>Funzione con binding

Lo scenario implementato in questo esempio include una funzione denominata `order` che accetta un oggetto `POST` con un payload che rappresenta un ordine del prodotto. Quando viene pubblicato un ordine nella funzione, viene creato un messaggio di archiviazione di Accodamento e viene restituita una risposta HTTP.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementazione

In una cartella denominata *Order*, il *function.jssu* file configura la funzione attivata tramite http.

**ordine/function.js**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Questa funzione è definita come una [funzione attivata tramite http](./functions-bindings-http-webhook-trigger.md) che restituisce una [risposta http](./functions-bindings-http-webhook-output.md) e restituisce un messaggio di [archiviazione di Accodamento](./functions-bindings-storage-queue-output.md) .

Alla radice dell'app, il *host.js* nel file è configurato per l'esecuzione di un file eseguibile denominato `handler.exe` ( `handler` in Linux o MacOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Si tratta della richiesta HTTP inviata al runtime di funzioni.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Il runtime di funzioni invierà quindi la richiesta HTTP seguente al gestore personalizzato:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Alcune parti del payload sono state rimosse per brevità.

*handler.exe* è il programma di gestione personalizzato go compilato che esegue un server Web e risponde alle richieste di chiamata di funzione dall'host di funzioni.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In questo esempio, il gestore personalizzato esegue un server Web per gestire gli eventi HTTP ed è impostato per l'ascolto delle richieste tramite `FUNCTIONS_CUSTOMHANDLER_PORT` .

Anche se l'host di funzioni ha ricevuto una richiesta HTTP originale in `/api/order` , richiama il gestore personalizzato usando il nome della funzione (il nome della cartella). In questo esempio la funzione viene definita nel percorso di `/order` . L'host invia al gestore personalizzato una richiesta HTTP nel percorso di `/order` .

Poiché le `POST` richieste vengono inviate a questa funzione, i metadati dei dati e delle funzioni del trigger sono disponibili tramite il corpo della richiesta HTTP. È possibile accedere al corpo della richiesta HTTP originale nel payload `Data.req.Body` .

La risposta della funzione è formattata in coppie chiave/valore in cui il `Outputs` membro include un valore JSON in cui le chiavi corrispondono agli output come definito nell' *function.jssul* file.

Si tratta di un payload di esempio restituito dal gestore all'host di funzioni.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Impostando l' `message` output uguale ai dati dell'ordine forniti dalla richiesta, la funzione restituisce i dati dell'ordine nella coda configurata. L'host Functions restituisce inoltre la risposta HTTP configurata nel `res` chiamante.

### <a name="http-only-function"></a>Funzione solo HTTP

Per le funzioni attivate da HTTP senza ulteriori binding o output, è possibile fare in modo che il gestore funzioni direttamente con la richiesta e la risposta HTTP anziché con i payload di [richiesta](#request-payload) e [risposta](#response-payload) del gestore personalizzato. Questo comportamento può essere configurato in *host.js* usando l' `enableForwardingHttpRequest` impostazione.

> [!IMPORTANT]
> Lo scopo principale della funzionalità gestori personalizzati è quello di abilitare linguaggi e Runtime che attualmente non dispongono di un supporto di prima classe in funzioni di Azure. Sebbene possa essere possibile eseguire applicazioni Web con gestori personalizzati, funzioni di Azure non è un proxy inverso standard. Alcune funzionalità, ad esempio streaming di risposta, HTTP/2 e WebSocket, non sono disponibili. È possibile che alcuni componenti della richiesta HTTP, ad esempio determinate intestazioni e route, siano limitati. È anche possibile che l'applicazione riscontri un [avvio a freddo](functions-scale.md#cold-start)eccessivo.
>
> Per risolvere queste circostanze, provare a eseguire le app Web nel [servizio app Azure](../app-service/overview.md).

Nell'esempio seguente viene illustrato come configurare una funzione attivata tramite HTTP senza ulteriori binding o output. Lo scenario implementato in questo esempio include una funzione denominata `hello` che accetta `GET` o `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementazione

In una cartella denominata *Hello*, il *function.jssu* file configura la funzione attivata tramite http.

**Hello/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

La funzione è configurata in modo da accettare le `GET` `POST` richieste e e il valore del risultato viene fornito tramite un argomento denominato `res` .

Alla radice dell'app, il *host.jsnel* file è configurato per l'esecuzione `handler.exe` e `enableForwardingHttpRequest` è impostato su `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Quando `enableForwardingHttpRequest` è `true` , il comportamento delle funzioni solo http differisce dal comportamento predefinito dei gestori personalizzati nei modi seguenti:

* La richiesta HTTP non contiene il payload della [richiesta](#request-payload) dei gestori personalizzati. Al contrario, l'host di funzioni richiama il gestore con una copia della richiesta HTTP originale.
* L'host Functions richiama il gestore con lo stesso percorso della richiesta originale, inclusi tutti i parametri della stringa di query.
* L'host di funzioni restituisce una copia della risposta HTTP del gestore come risposta alla richiesta originale.

Di seguito è riportata una richiesta POST all'host Functions. L'host Functions invia quindi una copia della richiesta al gestore personalizzato nello stesso percorso.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Il file *handler. go* implementa un server Web e una funzione http.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In questo esempio, il gestore personalizzato crea un server Web per gestire gli eventi HTTP ed è impostato per l'ascolto delle richieste tramite `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` le richieste vengono gestite restituendo una stringa e `POST` le richieste hanno accesso al corpo della richiesta.

La route per la funzione Order è la `/api/hello` stessa della richiesta originale.

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT`Non è la porta pubblica utilizzata per chiamare la funzione. Questa porta viene utilizzata dall'host di funzioni per chiamare il gestore personalizzato.

## <a name="deploying"></a>Distribuzione

Un gestore personalizzato può essere distribuito a ogni opzione di hosting di funzioni di Azure. Se il gestore richiede il sistema operativo o le dipendenze della piattaforma (ad esempio un runtime del linguaggio), potrebbe essere necessario usare un [contenitore personalizzato](./functions-create-function-linux-custom-image.md).

Quando si crea un'app per le funzioni in Azure per i gestori personalizzati, si consiglia di selezionare .NET Core come stack. In futuro verrà aggiunto uno stack "Custom" per i gestori personalizzati.

Per distribuire un'app di gestione personalizzata usando Azure Functions Core Tools, eseguire il comando seguente.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Verificare che tutti i file necessari per eseguire il gestore personalizzato si trovino nella cartella e inclusi nella distribuzione. Se il gestore personalizzato è un eseguibile binario o presenta dipendenze specifiche della piattaforma, verificare che questi file corrispondano alla piattaforma di distribuzione di destinazione.

## <a name="restrictions"></a>Restrizioni

- Il server Web del gestore personalizzato deve iniziare entro 60 secondi.

## <a name="samples"></a>Esempi

Per esempi di come implementare funzioni in un'ampia gamma di linguaggi diversi, vedere il [repository GitHub di esempio di gestore personalizzato](https://github.com/Azure-Samples/functions-custom-handlers) .

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="trace-logging"></a>Registrazione di traccia

Se non è possibile avviare il processo del gestore personalizzato o se si verificano problemi di comunicazione con l'host di funzioni, è possibile aumentare il livello di registrazione dell'app per `Trace` visualizzare altri messaggi di diagnostica dall'host.

Per modificare il livello di registrazione predefinito dell'app per le funzioni, configurare l' `logLevel` impostazione nella `logging` sezione di *host.json*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

L'host di funzioni restituisce messaggi di log aggiuntivi, incluse le informazioni correlate al processo del gestore personalizzato. Usare i log per esaminare i problemi che avviano il processo di gestione personalizzato o richiamare funzioni nel gestore personalizzato.

In locale, i log vengono stampati nella console.

In Azure, [eseguire una query Application Insights tracce](functions-monitoring.md#query-telemetry-data) per visualizzare i messaggi di log. Se l'app produce un volume elevato di log, a Application Insights viene inviato solo un subset di messaggi di log. [Disabilitare il campionamento](functions-monitoring.md#configure-sampling) per assicurarsi che tutti i messaggi vengano registrati.

### <a name="test-custom-handler-in-isolation"></a>Test del gestore personalizzato in isolamento

Le app del gestore personalizzate sono un processo server Web, pertanto può essere utile avviarlo autonomamente e testare le chiamate di funzione inviando [richieste http](#request-payload) fittizie usando uno strumento come [curl](https://curl.haxx.se/) o [postazione](https://www.postman.com/).

È anche possibile usare questa strategia nelle pipeline CI/CD per eseguire test automatizzati sul gestore personalizzato.

### <a name="execution-environment"></a>Ambiente di esecuzione

I gestori personalizzati vengono eseguiti nello stesso ambiente di una tipica app funzioni di Azure. Testare il gestore per assicurarsi che l'ambiente contenga tutte le dipendenze necessarie per l'esecuzione. Per le app che richiedono dipendenze aggiuntive, potrebbe essere necessario eseguirle usando un' [immagine del contenitore personalizzata](functions-create-function-linux-custom-image.md) ospitata nel [piano Premium](functions-premium-plan.md)di funzioni di Azure.

### <a name="get-support"></a>Supporto

Se è necessaria assistenza per un'app per le funzioni con gestori personalizzati, è possibile inviare una richiesta tramite i normali canali di supporto. Tuttavia, a causa dell'ampia gamma di linguaggi possibili utilizzati per creare app per gestori personalizzati, il supporto non è illimitato.

Il supporto è disponibile se l'host di funzioni presenta problemi durante l'avvio o la comunicazione con il processo di gestione personalizzato. Per i problemi specifici del processo di gestione personalizzato, ad esempio i problemi con il linguaggio o il framework scelto, il team di supporto non è in grado di fornire assistenza in questo contesto.
