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
ms.openlocfilehash: e0e649045e3efe488804fd37c030fe01991ad232
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803616"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md). 

Per i progetti di esempio di funzione autonoma in Python, vedere gli [esempi di funzioni di Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modello di programmazione

Funzioni di Azure prevede che una funzione sia un metodo senza stato nello script Python che elabora l'input e genera l'output. Per impostazione predefinita, il runtime prevede che il metodo venga implementato come metodo globale denominato `main()` nel file `__init__.py`. È anche possibile [specificare un punto di ingresso alternativo](#alternate-entry-point).

I dati dei trigger e delle associazioni vengono associati alla funzione tramite gli attributi del metodo usando la proprietà `name` definita nel file *Function. JSON* . Il file _Function. JSON_ seguente, ad esempio, descrive una semplice funzione attivata da una richiesta HTTP denominata `req`:

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

è anche possibile dichiarare in modo esplicito i tipi di attributo e il tipo restituito nella funzione usando le annotazioni di tipo Python. Questo consente di usare le funzionalità di IntelliSense e di completamento automatico fornite da molti editor di codice Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="alternate-entry-point"></a>Punto di ingresso alternativo

È possibile modificare il comportamento predefinito di una funzione specificando facoltativamente le proprietà `scriptFile` e `entryPoint` nel file *Function. JSON* . Ad esempio, il file _Function. JSON_ seguente indica al runtime di usare il metodo `customentry()` nel file _Main.py_ , come punto di ingresso per la funzione di Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle per un progetto di funzioni Python ha un aspetto simile all'esempio seguente:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
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

Per fare riferimento a moduli locali a una funzione, è possibile usare la sintassi di importazione relativa, come indicato di seguito:

```
from . import example
```

Quando si distribuisce un progetto di funzione nell'app per le funzioni in Azure, l'intero contenuto della cartella *FunctionApp* deve essere incluso nel pacchetto, ma non nella cartella.

## <a name="triggers-and-inputs"></a>Trigger e input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Sebbene siano diversi nel file `function.json`, l'utilizzo è identico nel codice Python.  Le stringhe di connessione o i segreti per i trigger e le origini di input eseguono il mapping ai valori nel file `local.settings.json` quando vengono eseguiti localmente e le impostazioni dell'applicazione durante l'esecuzione in Azure. 

Ad esempio, nel codice seguente viene illustrata la differenza tra i due:

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

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Una voce verrà recuperata dall'archivio BLOB di Azure in base all' _ID_ nell'URL della route e resa disponibile come `obj` nel corpo della funzione.  L'account di archiviazione specificato è la stringa di connessione trovata in, ovvero lo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>outputs

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per produrre più output, utilizzare il metodo `set()` fornito dall'interfaccia [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) per assegnare un valore all'associazione. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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
| **`critical(_message_)`**   | Scrive un messaggio con livello critico nel logger radice.  |
| **`error(_message_)`**   | Scrive un messaggio con livello errore nel logger radice.    |
| **`warning(_message_)`**    | Scrive un messaggio con livello avviso nel logger radice.  |
| **`info(_message_)`**    | Scrive un messaggio con livello informativo nel logger radice.  |
| **`debug(_message_)`** | Scrive un messaggio con livello debug nel logger radice.  |

Per altre informazioni sulla registrazione, vedere [monitorare funzioni di Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Trigger e associazioni HTTP

Il trigger HTTP è definito nel file function. Jon. Il `name` dell'associazione deve corrispondere al parametro denominato nella funzione. Negli esempi precedenti viene usato un nome di binding `req`. Questo parametro è un oggetto [HttpRequest] e viene restituito un oggetto [HttpResponse] .

Dall'oggetto [HttpRequest] è possibile recuperare le intestazioni della richiesta, i parametri di query, i parametri di route e il corpo del messaggio. 

L'esempio seguente è basato sul [modello di trigger http per Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In questa funzione, il valore del parametro di query `name` viene ottenuto dal parametro `params` dell'oggetto [HttpRequest] . Il corpo del messaggio con codifica JSON viene letto usando il metodo `get_json`. 

Analogamente, è possibile impostare il `status_code` e `headers` per il messaggio di risposta nell'oggetto [HttpResponse] restituito.

## <a name="concurrency"></a>Concorrenza

Per impostazione predefinita, il runtime di Python di funzioni può elaborare solo una chiamata di una funzione alla volta. Questo livello di concorrenza potrebbe non essere sufficiente in una o più delle condizioni seguenti:

+ Si sta tentando di gestire una serie di chiamate eseguite contemporaneamente.
+ Si sta elaborando un numero elevato di eventi di I/O.
+ L'applicazione è associata a I/O.

In queste situazioni, è possibile migliorare le prestazioni eseguendo in modo asincrono e utilizzando più processi di lavoro in linguaggio.  

### <a name="async"></a>Async

Si consiglia di utilizzare l'istruzione `async def` per fare in modo che la funzione venga eseguita come una coroutine asincrona.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Quando la funzione `main()` è sincrona (senza il qualificatore `async`), la funzione viene eseguita automaticamente in un pool di thread `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usare più processi di lavoro in linguaggio

Per impostazione predefinita, ogni istanza host di funzioni ha un singolo processo di lavoro in linguaggio. Tuttavia, è supportato l'esistenza di più processi di lavoro in linguaggio per ogni istanza host. Le chiamate di funzione possono quindi essere distribuite in modo uniforme tra questi processi di lavoro del linguaggio. Per modificare questo valore, utilizzare l'impostazione dell'applicazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . 

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere l'argomento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) nella firma. 

Ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**context**](/python/api/azure-functions/azure.functions.context?view=azure-python) presenta gli attributi stringa seguenti:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Variabili globali

Non è garantito che lo stato dell'app verrà mantenuto per le esecuzioni future. Tuttavia, il runtime di funzioni di Azure spesso riutilizza lo stesso processo per più esecuzioni della stessa app. Per memorizzare nella cache i risultati di un calcolo oneroso, dichiararli come una variabile globale. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variabili di ambiente

Nelle funzioni, [le impostazioni dell'applicazione](functions-app-settings.md), ad esempio le stringhe di connessione del servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni dichiarando `import os` e quindi utilizzando `setting = os.environ["setting-name"]`.

Nell'esempio seguente viene ottenuta l' [impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings)con la chiave denominata `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Per lo sviluppo locale, le impostazioni dell'applicazione vengono [mantenute nel file local. Settings. JSON](functions-run-local.md#local-settings-file).  

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

Quando si è pronti per la pubblicazione, assicurarsi che tutte le dipendenze siano elencate nel file *requirements. txt* che si trova nella radice della directory del progetto. Funzioni di Azure può [compilare in remoto](functions-deployment-technologies.md#remote-build) queste dipendenze.

I file di progetto e le cartelle escluse dalla pubblicazione, inclusa la cartella dell'ambiente virtuale, sono elencati nel file con estensione funcignore. 

Sia la [Azure Functions Core Tools](functions-run-local.md#v2) che l' [estensione di funzioni di Azure per vs code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) eseguiranno una compilazione remota per impostazione predefinita. Ad esempio, usare il comando seguente:

```bash
func azure functionapp publish <app name>
```

Se si vuole compilare l'app localmente anziché in Azure, [installare Docker](https://docs.docker.com/install/) nel computer locale ed eseguire il comando seguente per pubblicare usando il [Azure Functions Core Tools](functions-run-local.md#v2) (Func). Ricordare di sostituire `<app name>` con il nome dell'app per le funzioni in Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Core Tools userà Docker per eseguire l'immagine [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) come contenitore nel computer locale. Usando questo ambiente, i moduli necessari vengono compilati e installati dalla distribuzione di origine, prima di crearli per la distribuzione finale in Azure.

Per compilare le dipendenze e pubblicare usando un sistema di recapito continuo (CD), [usare Azure Pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Testing unità

Le funzioni scritte in Python possono essere testate come altro codice Python usando Framework di test standard. Per la maggior parte delle associazioni, è possibile creare un oggetto di input fittizio creando un'istanza di una classe appropriata dal pacchetto `azure.functions`. Poiché il pacchetto di [`azure.functions`](https://pypi.org/project/azure-functions/) non è immediatamente disponibile, assicurarsi di installarlo tramite il file di `requirements.txt`, come descritto nella sezione precedente relativa alla [versione di Python e alla gestione dei pacchetti](#python-version-and-package-management) .

Ad esempio, di seguito è riportato un test fittizio di una funzione attivata tramite HTTP:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Di seguito è riportato un altro esempio con una funzione attivata dalla coda:

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
## <a name="temporary-files"></a>File temporanei

Il metodo `tempfile.gettempdir()` restituisce una cartella temporanea, che in Linux è `/tmp`. L'applicazione può usare questa directory per archiviare i file temporanei generati e usati dalle funzioni durante l'esecuzione. 

> [!IMPORTANT]
> Non è garantito che i file scritti nella directory temporanea vengano mantenuti tra le chiamate. Durante la scalabilità orizzontale, i file temporanei non vengono condivisi tra le istanze. 

Nell'esempio seguente viene creato un file temporaneo denominato nella directory temporanea (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

### <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

Funzioni di Azure supporta la condivisione di risorse tra le origini (CORS). CORS viene configurato [nel portale](functions-how-to-use-azure-function-app-settings.md#cors) e tramite l'interfaccia della riga di comando di [Azure](/cli/azure/functionapp/cors). L'elenco delle origini consentite di CORS si applica a livello di app per le funzioni. Con CORS abilitato, le risposte includono l'intestazione `Access-Control-Allow-Origin`. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](functions-how-to-use-azure-function-app-settings.md#cors).

L'elenco delle origini consentite [non è attualmente supportato](https://github.com/Azure/azure-functions-python-worker/issues/444) per le app per le funzioni Python. A causa di questa limitazione, è necessario impostare esplicitamente l'intestazione `Access-Control-Allow-Origin` nelle funzioni HTTP, come illustrato nell'esempio seguente:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Assicurarsi di aggiornare anche function. JSON per supportare il metodo HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Questo metodo viene utilizzato dal browser Chrome per negoziare l'elenco di origini consentite. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Documentazione dell'API del pacchetto di funzioni di Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
