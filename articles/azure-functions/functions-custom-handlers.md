---
title: Gestori personalizzati di funzioni di Azure (anteprima)
description: Informazioni su come usare funzioni di Azure con qualsiasi linguaggio o versione di Runtime.
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
# <a name="azure-functions-custom-handlers-preview"></a>Gestori personalizzati di funzioni di Azure (anteprima)

Ogni app per le funzioni viene eseguita da un gestore specifico della lingua. Sebbene funzioni di Azure supporti molti [gestori di linguaggio](./supported-languages.md) per impostazione predefinita, in alcuni casi può essere necessario un controllo aggiuntivo sull'ambiente di esecuzione dell'app. I gestori personalizzati forniscono questo controllo aggiuntivo.

I gestori personalizzati sono server Web leggeri che ricevono eventi dall'host di funzioni. Qualsiasi linguaggio che supporta le primitive HTTP può implementare un gestore personalizzato.

I gestori personalizzati sono ideali per le situazioni in cui si desidera:

- Implementare un'app per le funzioni in una lingua oltre le lingue ufficialmente supportate
- Implementare un'app per le funzioni in una versione o in un runtime della lingua non supportata per impostazione predefinita
- Avere un controllo granulare sull'ambiente di esecuzione dell'app

Con i gestori personalizzati, tutti i [trigger e le associazioni di input e output](./functions-triggers-bindings.md) sono supportati tramite i [bundle di estensione](./functions-bindings-register.md).

## <a name="overview"></a>Panoramica

Nel diagramma seguente viene illustrata la relazione tra l'host di funzioni e un server Web implementato come gestore personalizzato.

![Panoramica del gestore personalizzato di funzioni di Azure](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Gli eventi attivano una richiesta inviata all'host di funzioni. L'evento contiene un payload HTTP non elaborato (per le funzioni attivate da HTTP senza binding) o un payload che contiene dati di binding di input per la funzione.
- L'host Functions esegue quindi la delega della richiesta al server Web mediante l'emissione di un [payload della richiesta](#request-payload).
- Il server Web esegue la singola funzione e restituisce un payload di [risposta](#response-payload) all'host di funzioni.
- L'host di funzioni delega la risposta come payload dell'associazione di output alla destinazione.

Un'app funzioni di Azure implementata come gestore personalizzato deve configurare i file *host. JSON* e *Function. JSON* secondo alcune convenzioni.

## <a name="application-structure"></a>Struttura dell'applicazione

Per implementare un gestore personalizzato, per l'applicazione sono necessari gli aspetti seguenti:

- Un file *host. JSON* alla radice dell'app
- Un file *Function. JSON* per ogni funzione (all'interno di una cartella che corrisponde al nome della funzione)
- Un comando, uno script o un file eseguibile che esegue un server Web

Il diagramma seguente illustra il modo in cui questi file esaminano la file system per una funzione denominata "Order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configurazione

L'applicazione viene configurata tramite il file *host. JSON* . Questo file indica all'host di funzioni dove inviare le richieste puntando a un server Web in grado di elaborare eventi HTTP.

Un gestore personalizzato viene definito configurando il file *host. JSON* con informazioni dettagliate su come eseguire il server Web tramite `httpWorker` la sezione.

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

La `httpWorker` sezione punta a una destinazione in base a quanto `defaultExecutablePath`definito dall'oggetto. La destinazione di esecuzione può essere un comando, un eseguibile o un file in cui è implementato il server Web.

Per le app con script `defaultExecutablePath` , punta al runtime del linguaggio di scripting e `defaultWorkerPath` punta al percorso del file di script. Nell'esempio seguente viene illustrato come un'app JavaScript in node. js è configurata come gestore personalizzato.

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

È anche possibile passare argomenti usando la `arguments` matrice:

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

Gli argomenti sono necessari per molte configurazioni di debug. Per ulteriori informazioni, vedere la sezione [debug](#debugging) .

> [!NOTE]
> Il file *host. JSON* deve essere allo stesso livello nella struttura di directory del server Web in esecuzione. Per impostazione predefinita, alcuni linguaggi e toolchain potrebbero non posizionare il file nella radice dell'applicazione.

#### <a name="bindings-support"></a>Supporto di binding

I trigger standard e le associazioni di input e output sono disponibili facendo riferimento ai [bundle di estensione](./functions-bindings-register.md) nel file *host. JSON* .

### <a name="function-metadata"></a>Metadati della funzione

Quando viene usato con un gestore personalizzato, il contenuto di *Function. JSON* non è diverso da quello in cui definire una funzione in qualsiasi altro contesto. L'unico requisito è che i file *Function. JSON* debbano trovarsi in una cartella denominata in modo che corrisponda al nome della funzione.

### <a name="request-payload"></a>Payload della richiesta

Il payload della richiesta per le funzioni HTTP pure è il payload della richiesta HTTP non elaborata. Le funzioni HTTP pure sono definite come funzioni senza binding di input o di output, che restituiscono una risposta HTTP.

Qualsiasi altro tipo di funzione che include input, associazioni di output o viene attivato tramite un'origine evento diversa da HTTP ha un payload della richiesta personalizzato.

Il codice seguente rappresenta un payload di richiesta di esempio. Il payload include una struttura JSON con due membri: `Data` e `Metadata`.

Il `Data` membro include chiavi che corrispondono ai nomi di input e trigger come definito nella matrice bindings nel file *Function. JSON* .

Il `Metadata` membro include i [metadati generati dall'origine evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

Date le associazioni definite nel file *Function. JSON* seguente:

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

Viene restituito un payload della richiesta simile a questo esempio:

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

| <nobr>Chiave payload</nobr>   | Tipo di dati | Osservazioni                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Include i valori di risposta definiti dalla `bindings` matrice del file *Function. JSON* .<br /><br />Ad esempio, se una funzione è configurata con un'associazione di output di archiviazione BLOB denominata " `Outputs` BLOB", contiene `blob`una chiave denominata, che è impostata sul valore del BLOB. |
| `Logs`        | array     | I messaggi vengono visualizzati nei log di chiamata delle funzioni.<br /><br />Quando è in esecuzione in Azure, i messaggi vengono visualizzati in Application Insights. |
| `ReturnValue` | string    | Usato per fornire una risposta quando un output viene configurato come `$return` nel file *Function. JSON* . |

Vedere l' [esempio relativo a un payload di esempio](#bindings-implementation).

## <a name="examples"></a>Esempi

I gestori personalizzati possono essere implementati in qualsiasi linguaggio che supporti gli eventi HTTP. Mentre funzioni di Azure [supporta completamente JavaScript e node. js](./functions-reference-node.md), gli esempi seguenti illustrano come implementare un gestore personalizzato usando JavaScript in node. js a scopo di istruzione.

> [!TIP]
> Sebbene si tratti di una guida per apprendere come implementare un gestore personalizzato in altri linguaggi, gli esempi basati su node. js illustrati di seguito possono essere utili anche se si vuole eseguire un'app per le funzioni in una versione non supportata di node. js.

## <a name="http-only-function"></a>Funzione solo HTTP

Nell'esempio seguente viene illustrato come configurare una funzione attivata tramite HTTP senza ulteriori binding o output. Lo scenario implementato in questo esempio include una funzione denominata `http` che accetta `GET` o `POST` .

Il frammento di codice seguente rappresenta il modo in cui viene composta una richiesta alla funzione.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementazione

In una cartella denominata *http*, il file *Function. JSON* configura la funzione attivata tramite http.

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

La funzione è configurata in `GET` modo `POST` da accettare le richieste e e il valore del risultato viene `res`fornito tramite un argomento denominato.

Alla radice dell'app, il file *host. JSON* è configurato per eseguire node. js e puntare il `server.js` file.

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

Il file *Server. js* implementa una funzione http e un server Web.

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

In questo esempio, Express viene usato per creare un server Web per gestire gli eventi HTTP ed è impostato per l' `FUNCTIONS_HTTPWORKER_PORT`ascolto delle richieste tramite.

La funzione viene definita nel percorso di `/hello`. `GET`le richieste vengono gestite restituendo un semplice oggetto JSON e `POST` le richieste hanno accesso al corpo della richiesta `req.body`tramite.

La route per la funzione Order è `/hello` e non `/api/hello` perché l'host Functions sta inviando un proxy alla richiesta al gestore personalizzato.

>[!NOTE]
>Non `FUNCTIONS_HTTPWORKER_PORT` è la porta pubblica utilizzata per chiamare la funzione. Questa porta viene utilizzata dall'host di funzioni per chiamare il gestore personalizzato.

## <a name="function-with-bindings"></a>Funzione con binding

Lo scenario implementato in questo esempio include una funzione denominata `order` che accetta un `POST` oggetto con un payload che rappresenta un ordine del prodotto. Quando viene pubblicato un ordine nella funzione, viene creato un messaggio di archiviazione di Accodamento e viene restituita una risposta HTTP.

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

In una cartella denominata *Order*, il file *Function. JSON* configura la funzione attivata tramite http.

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

Questa funzione è definita come una [funzione attivata tramite http](./functions-bindings-http-webhook-trigger.md) che restituisce una [risposta http](./functions-bindings-http-webhook-output.md) e restituisce un messaggio di [archiviazione di Accodamento](./functions-bindings-storage-queue-output.md) .

Alla radice dell'app, il file *host. JSON* è configurato per eseguire node. js e puntare il `server.js` file.

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

Il file *Server. js* implementa una funzione http e un server Web.

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

In questo esempio, Express viene usato per creare un server Web per gestire gli eventi HTTP ed è impostato per l' `FUNCTIONS_HTTPWORKER_PORT`ascolto delle richieste tramite.

La funzione viene definita nel percorso di `/order` .  La route per la funzione Order è `/order` e non `/api/order` perché l'host Functions sta inviando un proxy alla richiesta al gestore personalizzato.

Poiché `POST` le richieste vengono inviate a questa funzione, i dati vengono esposti tramite pochi punti:

- Il corpo della richiesta è disponibile tramite`req.body`
- I dati inseriti nella funzione sono disponibili tramite`req.body.Data.req.Body`

La risposta della funzione viene formattata in una coppia chiave/valore in `Outputs` cui il membro include un valore JSON in cui le chiavi corrispondono agli output come definito nel file *Function. JSON* .

Impostando `message` uguale al messaggio fornito dalla richiesta e `res` alla risposta http prevista, questa funzione genera un messaggio nell'archivio di Accodamento e restituisce una risposta http.

## <a name="debugging"></a>Debug

Per eseguire il debug dell'app del gestore personalizzato funzioni, è necessario aggiungere argomenti appropriati per il linguaggio e il runtime per abilitare il debug.

Ad esempio, per eseguire il debug di un'applicazione Node. `--inspect` js, il flag viene passato come argomento nel file *host. JSON* .

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
> La configurazione di debug fa parte del file *host. JSON* , il che significa che potrebbe essere necessario rimuovere alcuni argomenti prima della distribuzione nell'ambiente di produzione.

Con questa configurazione, è possibile avviare il processo host della funzione usando il comando seguente:

```bash
func host start
```

Una volta avviato il processo, è possibile aggiungere un debugger e raggiungere i punti di interruzione.

### <a name="visual-studio-code"></a>Visual Studio Code

L'esempio seguente è una configurazione di esempio che illustra come è possibile configurare il file *Launch. JSON* per connettere l'app al debugger Visual Studio Code.

Questo esempio è per node. js, quindi potrebbe essere necessario modificare questo esempio per altri linguaggi o Runtime.

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

Un gestore personalizzato può essere distribuito a quasi tutte le opzioni di hosting di funzioni di Azure (vedere [restrizioni](#restrictions)). Se il gestore richiede dipendenze personalizzate (ad esempio un runtime del linguaggio), potrebbe essere necessario usare un [contenitore personalizzato](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Restrizioni

- I gestori personalizzati non sono supportati nei piani a consumo Linux.
- Il server Web deve iniziare entro 60 secondi.

## <a name="samples"></a>Esempi

Per esempi di come implementare funzioni in un'ampia gamma di linguaggi diversi, vedere il [repository GitHub di esempio di gestore personalizzato](https://github.com/Azure-Samples/functions-custom-handlers) .
