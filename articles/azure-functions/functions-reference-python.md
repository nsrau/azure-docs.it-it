---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura serverless, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: d25082c429c58c074726c75f7ff6f248daee4151
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050613"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modello di programmazione

Una funzione di Azure deve essere un metodo senza stato nello script Python che elabora un input e restituisce un output. Per impostazione predefinita, il runtime si aspetta che il metodo per essere implementata come metodo globale denominato `main()` nella `__init__.py` file.

È possibile modificare la configurazione predefinita, specificando il `scriptFile` e `entryPoint` delle proprietà nel *Function. JSON* file. Ad esempio, il _Function. JSON_ di seguito indica al runtime di usare il `customentry()` metodo nella _main.py_ file, come punto di ingresso per la funzione di Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

I dati dal trigger e associazioni sono associati alla funzione tramite gli attributi del metodo usando il `name` definita nella proprietà di *Function. JSON* file. Ad esempio, il _Function. JSON_ di seguito viene descritta una semplice funzione attivata da una richiesta HTTP denominata `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Il file `__init__.py` contiene il codice funzione seguente:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Facoltativamente, per sfruttare i vantaggi di intellisense e funzionalità Completamento automatico fornita dall'editor di codice, è possibile anche dichiarare i tipi di attributo e tipo restituito della funzione tramite le annotazioni di tipo Python. 

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle per un progetto di Funzioni Python ha un aspetto simile al seguente:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

È presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json). 

Il codice condiviso deve essere mantenuto in una cartella separata. Per fare riferimento a moduli nella cartella SharedCode, si può usare la sintassi seguente:

```
from __app__.SharedCode import myFirstHelperFunction
```

Quando si distribuisce un progetto di funzione app per le funzioni in Azure, l'intero contenuto del *FunctionApp* cartella deve essere inclusi nel pacchetto, ma non nella cartella.

## <a name="triggers-and-inputs"></a>I trigger e input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono diversi nel `function.json` file, l'utilizzo è identico nel codice Python.  Stringhe di connessione o i segreti per le origini di input e trigger di eseguire il mapping ai valori nel `local.settings.json` file durante l'esecuzione locale e le impostazioni dell'applicazione durante l'esecuzione in Azure. 

Ad esempio, il codice seguente illustra la differenza tra i due:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Verrà recuperata dall'archivio Blob di Azure basata su una voce di _ID_ nell'URL della route e resi disponibili come `obj` nel corpo della funzione.  In questo caso l'account di archiviazione specificato viene trovata la stringa di connessione `AzureWebJobsStorage` che è lo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per generare più output, usare il metodo `set()` fornito dall'interfaccia `azure.functions.Out` per assegnare un valore all'associazione. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registrazione

L'accesso al logger di runtime di Funzioni di Azure è disponibile tramite un gestore radice [`logging`](https://docs.python.org/3/library/logging.html#module-logging) nell'app per le funzioni. Il logger è associato ad Application Insights e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione della funzione.

L'esempio seguente registra un messaggio informativo quando la funzione viene richiamata tramite un trigger HTTP.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Sono disponibili altri metodi di registrazione che consentono di scrivere nella console a livelli di traccia diversi:

| Metodo                 | Descrizione                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_messaggio_)**   | Scrive un messaggio con livello critico nel logger radice.  |
| logging.**error(_messaggio_)**   | Scrive un messaggio con livello errore nel logger radice.    |
| logging.**warning(_messaggio_)**    | Scrive un messaggio con livello avviso nel logger radice.  |
| logging.**info(_messaggio_)**    | Scrive un messaggio con livello informativo nel logger radice.  |
| logging.**debug(_messaggio_)** | Scrive un messaggio con livello debug nel logger radice.  |

## <a name="async"></a>Async

Si consiglia di scrivere funzioni di Azure come una coroutine asincrona usando il `async def` istruzione.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Se la funzione Main () è sincrona (nessun `async` qualificatore) è eseguire automaticamente la funzione un `asyncio` pool di thread.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere l'argomento `context` nella sua firma. 

Ad esempio:

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe **Context** ha i metodi seguenti:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Variabili globali

Non è garantito che lo stato dell'app verrà mantenuto per le future esecuzioni. Tuttavia, il runtime di funzioni di Azure spesso riusa lo stesso processo per più esecuzioni della stessa app. Per memorizzare nella cache i risultati di calcoli dispendiosi, dichiararlo come una variabile globale. 

```python
CACHED_DATA = None

def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Versione di Python e gestione dei pacchetti

Funzioni di Azure supporta attualmente solo Python 3.6.x (distribuzione ufficiale di CPython).

Quando si sviluppa in locale usando Azure Functions Core Tools oppure Visual Studio Code, aggiungere i nomi e le versioni dei pacchetti necessari al file `requirements.txt` e installarli con `pip`.

Ad esempio, per installare il pacchetto `requests` PyPI è possibile usare il file di requisiti e il comando pip seguenti.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Pubblicazione in Azure

Quando si è pronti a pubblicare, assicurarsi che tutte le dipendenze siano elencate nel *Requirements. txt* file, che si trova nella radice della directory del progetto. Se si usa un pacchetto che richiede un compilatore e non supporta l'installazione di pacchetti wheel compatibili con manylinux da PyPI, la pubblicazione in Azure avrà esito negativo con l'errore seguente: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Per creare e configurare automaticamente i file binari necessari, [installare Docker](https://docs.docker.com/install/) nel computer locale ed eseguire il comando seguente per pubblicare con [Azure Functions Core Tools](functions-run-local.md#v2) (func). Ricordare di sostituire `<app name>` con il nome dell'app per le funzioni in Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Core Tools userà Docker per eseguire l'immagine [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) come contenitore nel computer locale. Usando questo ambiente, creerà e installerà quindi i moduli necessari dalla distribuzione di origine, prima di inserirli in un pacchetto per la distribuzione finale in Azure.

Per compilare le dipendenze e la pubblicazione mediante un sistema di recapito continuo (CD), [Usa le pipeline di DevOps di Azure](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Unit test

Funzioni scritte in Python possono essere testate come altro codice Python con Framework di test standard. Per la maggior parte delle associazioni, è possibile creare un oggetto di input fittizio creando un'istanza di una classe adatta dal `azure.functions` pacchetto.

Ad esempio, ecco un test di simulazione di una funzione attivata tramite HTTP:

```python
# myapp/__init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function', 
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(), 
            'Hello, Test!',
        )
```

Ecco un altro esempio, con una funzione attivata dalla coda:

```python
# myapp/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp, 
            'msg body: test',
        )
``` 

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)
