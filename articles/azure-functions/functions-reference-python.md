---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
ms.topic: article
ms.date: 04/16/2018
ms.openlocfilehash: 7c8ce87fdf396bc488a7deaf576eea28f989e0e4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226654"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md). 

For standalone Function sample projects in Python, see the [Python Functions samples](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modello di programmazione

Azure Functions expects a function to be a stateless method in your Python script that processes input and produces output. By default, the runtime expects the method to be implemented as a global method called `main()` in the `__init__.py` file. You can also [specify an alternate entry point](#alternate-entry-point).

Data from triggers and bindings is bound to the function via method attributes using the `name` property defined in the *function.json* file. For example, the  _function.json_ below describes a simple function triggered by an HTTP request named `req`:

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

you can also explicitly declare the attribute types and return type in the function using Python type annotations. This helps you use the intellisense and autocomplete features provided by many Python code editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="alternate-entry-point"></a>Alternate entry point

You can change the default behavior of a function by optionally specifying the `scriptFile` and `entryPoint` properties in the *function.json* file. For example, the _function.json_ below tells the runtime to use the `customentry()` method in the _main.py_ file, as the entry point for your Azure Function.

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

The recommended folder structure for a Python Functions project looks like the following example:

```
 __app__
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
 tests
```
The main project folder (\_\_app\_\_) can contain the following files:

* *local.settings.json*: Used to store app settings and connection strings when running locally. Questo file non viene pubblicato in Azure. To learn more, see [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Contains the list of packages the system installs when publishing to Azure.
* *host.json*: Contains global configuration options that affect all functions in a function app. Questo file viene pubblicato in Azure. Not all options are supported when running locally. To learn more, see [host.json](functions-host-json.md).
* *funcignore*: (Optional) declares files that shouldn't get published to Azure.
* *gitignore*: (Optional) declares files that are excluded from a git repo, such as local.settings.json.

Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json). 

Shared code should be kept in a separate folder in \_\_app\_\_. Per fare riferimento a moduli nella cartella SharedCode, si può usare la sintassi seguente:

```python
from __app__.SharedCode import myFirstHelperFunction
```

To reference modules local to a function, you can use the relative import syntax as follows:

```python
from . import example
```

When deploying your project to a function app in Azure, the entire content of the *FunctionApp* folder should be included in the package, but not the folder itself. We recommend that you maintain your tests in a folder separate from the project folder, in this example `tests`. This keeps you from deploying test code with your app. For more information, see [Unit Testing](#unit-testing).

## <a name="triggers-and-inputs"></a>Triggers and Inputs

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Although they are different in the `function.json` file, usage is identical in Python code.  Connection strings or secrets for trigger and input sources map to values in the `local.settings.json` file when running locally, and the application settings when running in Azure. 

For example, the following code demonstrates the difference between the two:

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

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. An entry will be retrieved from the Azure Blob Storage based on the _ID_ in the route URL and made available as `obj` in the function body.  Here the storage account specified is the connection string found in  , which is the same storage account used by the function app.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

To produce multiple outputs, use the `set()` method provided by the [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interface to assign a value to the binding. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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

| Metodo                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Scrive un messaggio con livello critico nel logger radice.  |
| **`error(_message_)`**   | Scrive un messaggio con livello errore nel logger radice.    |
| **`warning(_message_)`**    | Scrive un messaggio con livello avviso nel logger radice.  |
| **`info(_message_)`**    | Scrive un messaggio con livello informativo nel logger radice.  |
| **`debug(_message_)`** | Scrive un messaggio con livello debug nel logger radice.  |

To learn more about logging, see [Monitor Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP Trigger and bindings

The HTTP trigger is defined in the function.jon file. The `name` of the binding must match the named parameter in the function. In the previous examples, a binding name `req` is used. This parameter is an [HttpRequest] object, and an [HttpResponse] object is returned.

From the [HttpRequest] object, you can get request headers, query parameters, route parameters, and the message body. 

The following example is from the [HTTP trigger template for Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

In this function, the value of the `name` query parameter is obtained from the `params` parameter of the [HttpRequest] object. The JSON-encoded message body is read using the `get_json` method. 

Likewise, you can set the `status_code` and `headers` for the response message in the returned [HttpResponse] object.

## <a name="concurrency"></a>Concorrenza

By default, the Functions Python runtime can only process one invocation of a function at a time. This concurrency level might not be sufficient under one or more of the following conditions:

+ You're trying to handle a number of invocations being made at the same time.
+ You're processing a large number of I/O events.
+ Your application is I/O bound.

In these situations, you can improve performance by running asynchronously and by using multiple language worker processes.  

### <a name="async"></a>Async

We recommend that you use the `async def` statement to make your function run as an asynchronous coroutine.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

When the `main()` function is synchronous (without the `async` qualifier), the function is automatically run in an `asyncio` thread-pool.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Use multiple language worker processes

By default, every Functions host instance has a single language worker process. However there's support to have multiple language worker processes per host instance. Function invocations can then be evenly distributed among these language worker processes. Use the [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) application setting to change this value. 

## <a name="context"></a>Context

To get the invocation context of a function during execution, include the [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) argument in its signature. 

ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

The [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) class has the following string attributes:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Global variables

It is not guaranteed that the state of your app will be preserved for future executions. However, the Azure Functions runtime often reuses the same process for multiple executions of the same app. In order to cache the results of an expensive computation, declare it as a global variable. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variabili di ambiente

In Functions, [application settings](functions-app-settings.md), such as service connection strings, are exposed as environment variables during execution. You can access these settings by declaring `import os` and then using, `setting = os.environ["setting-name"]`.

The following example gets the [application setting](functions-how-to-use-azure-function-app-settings.md#settings), with the key named `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

For local development, application settings are [maintained in the local.settings.json file](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versione Python 

Currently, Azure Functions supports both Python 3.6.x and 3.7.x (official CPython distributions). When running locally, the runtime uses the available Python version. To request a specific Python version when you create your function app in Azure, use the `--runtime-version` option of the [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) command.  

## <a name="package-management"></a>Gestione pacchetti

Quando si sviluppa in locale usando Azure Functions Core Tools oppure Visual Studio Code, aggiungere i nomi e le versioni dei pacchetti necessari al file `requirements.txt` e installarli con `pip`. 

Ad esempio, per installare il pacchetto `requests` PyPI è possibile usare il file di requisiti e il comando pip seguenti.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Pubblicazione in Azure

When you're ready to publish, make sure that all your publicly available dependencies are listed in the requirements.txt file, which is located at the root of your project directory. 

Project files and folders that are excluded from publishing, including the virtual environment folder, are listed in the .funcignore file.

There are three build actions supported for publishing your Python project to Azure:

+ Remote build: Dependencies are obtained remotely based on the contents of the requirements.txt file. [Remote build](functions-deployment-technologies.md#remote-build) is the recommended build method. Remote is also the default build option of Azure tooling. 
+ Local build: Dependencies are obtained locally based on the contents of the requirements.txt file. 
+ Custom dependencies: Your project uses packages not publicly available to our tools. (Requires Docker.)

To build your dependencies and publish using a continuous delivery (CD) system, [use Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Remote build

By default, the Azure Functions Core Tools requests a remote build when you use the following [func azure functionapp publish](functions-run-local.md#publish) command to publish your Python project to Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

The [Azure Functions Extension for Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) also requests a remote build by default. 

### <a name="local-build"></a>Local build

You can prevent doing a remote build by using the following [func azure functionapp publish](functions-run-local.md#publish) command to publish with a local build. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure. 

Using the `--build local` option, project dependencies are read from the requirements.txt file and those dependent packages are downloaded and installed locally. Project files and dependencies are deployed from your local computer to Azure. This results in a larger deployment package being uploaded to Azure. If for some reason, dependencies in your requirements.txt file can't be acquired by Core Tools, you must use the custom dependencies option for publishing. 

### <a name="custom-dependencies"></a>Custom dependencies

If your project uses packages not publicly available to our tools, you can make them available to your app by putting them in the \_\_app\_\_/.python_packages directory. Before publishing, run the following command to install the dependencies locally:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

When using custom dependencies, you should use the `--no-build` publishing option, since you have already installed the dependencies.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

## <a name="unit-testing"></a>Unit Testing

Functions written in Python can be tested like other Python code using standard testing frameworks. For most bindings, it's possible to create a mock input object by creating an instance of an appropriate class from the `azure.functions` package. Since the [`azure.functions`](https://pypi.org/project/azure-functions/) package is not immediately available, be sure to install it via your `requirements.txt` file as described in the [package management](#package-management) section above. 

For example, following is a mock test of an HTTP triggered function:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

Here is another example, with a queue triggered function:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Temporary files

The `tempfile.gettempdir()` method returns a temporary folder, which on Linux is `/tmp`. Your application can use this directory to store temporary files generated and used by your functions during execution. 

> [!IMPORTANT]
> Files written to the temporary directory aren't guaranteed to persist across invocations. During scale out, temporary files aren't shared between instances. 

The following example creates a named temporary file in the temporary directory (`/tmp`):

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

We recommend that you maintain your tests in a folder separate from the project folder. This keeps you from deploying test code with your app. 

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

### <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

Azure Functions supports cross-origin resource sharing (CORS). CORS is configured [in the portal](functions-how-to-use-azure-function-app-settings.md#cors) and through the [Azure CLI](/cli/azure/functionapp/cors). The CORS allowed origins list applies at the function app level. With CORS enabled, responses include the `Access-Control-Allow-Origin` header. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](functions-how-to-use-azure-function-app-settings.md#cors).

The allowed origins list [isn't currently supported](https://github.com/Azure/azure-functions-python-worker/issues/444) for Python function apps. Because of this limitation, you must expressly set the `Access-Control-Allow-Origin` header in your HTTP functions, as shown in the following example:

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

Make sure that you also update your function.json to support the OPTIONS HTTP method:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

This method is used by the Chrome browser to negotiate the allowed origins list. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Azure Functions package API documentation](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
