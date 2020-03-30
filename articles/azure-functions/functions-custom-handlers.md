---
title: Gestori personalizzati di Funzioni di Azure (anteprima)Azure Functions custom handlers (preview)
description: Informazioni su come usare Funzioni di Azure con qualsiasi lingua o versione di runtime.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479254"
---
# <a name="azure-functions-custom-handlers-preview"></a>Gestori personalizzati di Funzioni di Azure (anteprima)Azure Functions custom handlers (preview)

Ogni app Funzioni viene eseguita da un gestore specifico del linguaggio. Mentre Funzioni di Azure supporta molti gestori del [linguaggio](./supported-languages.md) per impostazione predefinita, in alcuni casi potrebbe essere necessario un controllo aggiuntivo sull'ambiente di esecuzione dell'app. I gestori personalizzati offrono questo controllo aggiuntivo.

I gestori personalizzati sono server Web leggeri che ricevono eventi dall'host Funzioni.Custom handlers are lightweight web servers that receive events from the Functions host. Qualsiasi linguaggio che supporta primitive HTTP può implementare un gestore personalizzato.

I gestori personalizzati sono più adatti per le situazioni in cui si desidera:Custom handlers are best suited for situations where you want to:

- Implementare un'app Funzioni in una lingua diversa dalle lingue ufficialmente supportateImplement a Functions app in a language beyond the officially supported languages
- Implementare un'app Funzioni in una versione della lingua o in un runtime non supportato per impostazione predefinitaImplement a Functions app in a language version or runtime not supported by default
- Avere un controllo granulare sull'ambiente di esecuzione delle app

Con i gestori personalizzati, tutti i [trigger e le associazioni](./functions-triggers-bindings.md) di input e output sono supportati tramite bundle di [estensione.](./functions-bindings-register.md)

## <a name="overview"></a>Panoramica

Nel diagramma seguente viene illustrata la relazione tra l'host Funzioni e un server Web implementato come gestore personalizzato.

![Panoramica del gestore personalizzato di Funzioni di AzureAzure Functions custom handler overview](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Gli eventi attivano una richiesta inviata all'host Funzioni. L'evento contiene un payload HTTP non elaborato (per le funzioni attivate da HTTP senza associazioni) o un payload che contiene i dati di associazione di input per la funzione.
- L'host Funzioni invia quindi tramite proxy la richiesta al server Web inviando un payload della [richiesta.](#request-payload)
- Il server Web esegue la singola funzione e restituisce un payload di [risposta](#response-payload) all'host funzioni.
- L'host Funzioni invia tè il proxy della risposta come payload di associazione di output alla destinazione.

Un'app Funzioni di Azure implementata come gestore personalizzato deve configurare i file *host.json* e *function.json* in base a alcune convenzioni.

## <a name="application-structure"></a>Struttura dell'applicazione

Per implementare un gestore personalizzato, sono necessari gli aspetti seguenti per l'applicazione:To implement a custom handler, you need the following aspects to your application:

- Un file *host.json* nella radice dell'app
- Un file *function.json* per ogni funzione (all'interno di una cartella che corrisponde al nome della funzione)
- Un comando, uno script o un eseguibile, che esegue un server Web

Il diagramma seguente mostra l'aspetto di questi file nel file system per una funzione denominata "order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configurazione

L'applicazione viene configurata tramite il file *host.json.* Questo file indica all'host Funzioni dove inviare le richieste puntando a un server Web in grado di elaborare eventi HTTP.

Un gestore personalizzato viene definito configurando il file *host.json* con `httpWorker` i dettagli su come eseguire il server Web tramite la sezione.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

La `httpWorker` sezione punta a una `defaultExecutablePath`destinazione come definito dal file . La destinazione di esecuzione può essere un comando, un eseguibile o un file in cui è implementato il server Web.

Per le app `defaultExecutablePath` con script, punta al `defaultWorkerPath` runtime del linguaggio di script e al percorso del file di script. L'esempio seguente mostra come un'app JavaScript in Node.js viene configurata come gestore personalizzato.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

È inoltre possibile passare `arguments` argomenti utilizzando la matrice:You can also pass arguments using the array:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Gli argomenti sono necessari per molte impostazioni di debug. Vedere la sezione [Debug](#debugging) per ulteriori dettagli.

> [!NOTE]
> Il file *host.json* deve trovarsi allo stesso livello nella struttura di directory del server Web in esecuzione. Alcuni linguaggi e toolchain potrebbero non inserire il file this nella radice dell'applicazione per impostazione predefinita.

#### <a name="bindings-support"></a>Supporto per i binding

I trigger standard e le associazioni di input e output sono disponibili facendo riferimento [ai pacchetti](./functions-bindings-register.md) di estensione nel file *host.json.*

### <a name="function-metadata"></a>Metadati delle funzioni

Se utilizzato con un gestore personalizzato, il contenuto di *function.json* non è diverso da come definire becco una funzione in qualsiasi altro contesto. L'unico requisito è che i file *function.json* devono trovarsi in una cartella denominata in modo che corrisponda al nome della funzione.

### <a name="request-payload"></a>Richiedi payload

Il payload della richiesta per le funzioni HTTP pure è il payload della richiesta HTTP non elaborata. Le funzioni HTTP pure sono definite come funzioni senza associazioni di input o output, che restituiscono una risposta HTTP.

Qualsiasi altro tipo di funzione che include associazioni di input, output o viene attivato tramite un'origine eventi diversa da HTTP dispone di un payload della richiesta personalizzato.

Il codice seguente rappresenta un payload della richiesta di esempio. Il payload include una struttura `Data` JSON `Metadata`con due membri: e .

Il `Data` membro include chiavi che corrispondono ai nomi di input e trigger definiti nella matrice di associazioni nel file *function.json.*

Il `Metadata` membro include [i metadati generati dall'origine evento.](./functions-bindings-expressions-patterns.md#trigger-metadata)

Date le associazioni definite nel file *function.json* seguente:

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

Viene restituito un payload di richiesta simile a questo esempio:A request payload similar to this example is returned:

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

### <a name="response-payload"></a>Carico utile della risposta

Per convenzione, le risposte alle funzioni vengono formattate come coppie chiave/valore. Le chiavi supportate includono:

| <nobr>Chiave di carico utile</nobr>   | Tipo di dati | Osservazioni                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Contiene i valori di `bindings` risposta definiti dalla matrice del file *function.json.*<br /><br />Ad esempio, se una funzione è configurata con un'associazione di output di archiviazione BLOB denominata "blob", `Outputs` contiene una chiave denominata `blob`, che è impostata sul valore del BLOB. |
| `Logs`        | array     | I messaggi vengono visualizzati nei registri di chiamata delle funzioni.<br /><br />Quando viene eseguito in Azure, i messaggi vengono visualizzati in Application Insights.When running in Azure, messages appear in Application Insights. |
| `ReturnValue` | string    | Utilizzato per fornire una risposta quando `$return` un output è configurato come nel file *function.json.* |

Vedere [l'esempio per un payload di esempio](#bindings-implementation).

## <a name="examples"></a>Esempi

I gestori personalizzati possono essere implementati in qualsiasi linguaggio che supporti gli eventi HTTP. Mentre Funzioni di Azure [supporta completamente JavaScript e Node.js](./functions-reference-node.md), negli esempi seguenti viene illustrato come implementare un gestore personalizzato usando JavaScript in Node.js ai fini dell'istruzione.

> [!TIP]
> Pur essendo una guida per imparare a implementare un gestore personalizzato in altri linguaggi, gli esempi basati su Node.js illustrati di seguito possono essere utili anche se si desidera eseguire un'app Funzioni in una versione non supportata di Node.js.

## <a name="http-only-function"></a>Funzione solo HTTP

Nell'esempio seguente viene illustrato come configurare una funzione attivata da HTTP senza associazioni o output aggiuntivi. Lo scenario implementato in questo `http` esempio include `GET` una `POST` funzione denominata che accetta un oggetto o .

Il frammento di codice seguente rappresenta la modalità di composizione di una richiesta alla funzione.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementazione

In una cartella denominata *http*, il file *function.json* configura la funzione attivata da HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

La funzione è configurata per accettare entrambi `GET` e `POST` le `res`richieste e il valore del risultato viene fornito tramite un argomento denominato .

Nella radice dell'app, il file *host.json* è configurato per `server.js` eseguire Node.js e puntare il file.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Il file *server.js* implementa un server Web e una funzione HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

In questo esempio, Express viene utilizzato per creare un server Web per `FUNCTIONS_HTTPWORKER_PORT`gestire gli eventi HTTP ed è impostato per l'ascolto delle richieste tramite l'oggetto .

La funzione viene definita `/hello`nel percorso di . `GET`le richieste vengono gestite restituendo `POST` un semplice oggetto JSON `req.body`e le richieste hanno accesso al corpo della richiesta tramite .

La route per la `/hello` funzione `/api/hello` order è e non perché l'host di funzioni inoltra la richiesta al gestore personalizzato.

>[!NOTE]
>Non `FUNCTIONS_HTTPWORKER_PORT` è la porta pubblica utilizzata per chiamare la funzione. Questa porta viene utilizzata dall'host Functions per chiamare il gestore personalizzato.

## <a name="function-with-bindings"></a>Funzione con associazioni

Lo scenario implementato in questo `order` esempio include `POST` una funzione denominata che accetta un con un payload che rappresenta un ordine di prodotto. Quando un ordine viene inviato alla funzione, viene creato un messaggio di archiviazione della coda e viene restituita una risposta HTTP.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementazione

In una cartella denominata *order*, il file *function.json* configura la funzione attivata da HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

Questa funzione è definita come [una funzione attivata HTTP](./functions-bindings-http-webhook-trigger.md) che restituisce una risposta [HTTP](./functions-bindings-http-webhook-output.md) e restituisce un messaggio di archiviazione [della coda.](./functions-bindings-storage-queue-output.md)

Nella radice dell'app, il file *host.json* è configurato per `server.js` eseguire Node.js e puntare il file.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Il file *server.js* implementa un server Web e una funzione HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

In questo esempio, Express viene utilizzato per creare un server Web per `FUNCTIONS_HTTPWORKER_PORT`gestire gli eventi HTTP ed è impostato per l'ascolto delle richieste tramite l'oggetto .

La funzione viene definita `/order` nel percorso di .  La route per la `/order` funzione `/api/order` order è e non perché l'host di funzioni inoltra la richiesta al gestore personalizzato.

Man `POST` mano che le richieste vengono inviate a questa funzione, i dati vengono esposti attraverso alcuni punti:As requests are sent to this function, data is exposed through a few points:

- Il corpo della richiesta è disponibile tramite`req.body`
- I dati inviati alla funzione sono disponibili tramite`req.body.Data.req.Body`

La risposta della funzione viene formattata in una `Outputs` coppia chiave/valore in cui il membro contiene un valore JSON in cui le chiavi corrispondono agli output definiti nel file *function.json.*

Impostando `message` uguale al messaggio proveniente dalla `res` richiesta e alla risposta HTTP prevista, questa funzione restituisce un messaggio nell'archivio della coda e restituisce una risposta HTTP.

## <a name="debugging"></a>Debug

Per eseguire il debug dell'app del gestore personalizzato Funzioni, è necessario aggiungere argomenti appropriati per il linguaggio e il runtime per abilitare il debug.

Ad esempio, per eseguire il debug `--inspect` di un'applicazione Node.js, il flag viene passato come argomento nel file *host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> La configurazione di debug fa parte del file *host.json,* il che significa che potrebbe essere necessario rimuovere alcuni argomenti prima della distribuzione nell'ambiente di produzione.

Con questa configurazione, è possibile avviare il processo host della funzione utilizzando il comando seguente:

```bash
func host start
```

Una volta avviato il processo, è possibile collegare un debugger e raggiungere i punti di interruzione.

### <a name="visual-studio-code"></a>Visual Studio Code

L'esempio seguente è una configurazione di esempio che illustra come configurare il file launch.json per connettere l'app al debugger del codice di Visual Studio.The following example is a sample configuration that demonstrates how you can set up your *launch.json* file to connect your app to the Visual Studio Code debugger.

Questo esempio è per Node.js, pertanto potrebbe essere necessario modificare questo esempio per altre lingue o runtime.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Distribuzione

Un gestore personalizzato può essere distribuito in quasi tutte le opzioni di hosting di Funzioni di Azure (vedere [restrizioni](#restrictions)). Se il gestore richiede dipendenze personalizzate, ad esempio un runtime del linguaggio, potrebbe essere necessario utilizzare un [contenitore personalizzato.](./functions-create-function-linux-custom-image.md)

## <a name="restrictions"></a>Restrizioni

- I gestori personalizzati non sono supportati nei piani di consumo Linux.Custom handlers are not supported in Linux consumption plans.
- Il server Web deve essere avviato entro 60 secondi.

## <a name="samples"></a>Esempi

Fare riferimento agli esempi di [gestori personalizzati GitHub repository](https://github.com/Azure-Samples/functions-custom-handlers) per esempi di come implementare le funzioni in una varietà di linguaggi diversi.
