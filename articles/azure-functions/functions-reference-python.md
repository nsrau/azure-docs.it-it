---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276686"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md). 

Per i progetti di esempio di funzioni autonome in Python, vedere gli esempi di [funzioni Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modello di programmazione

Funzioni di Azure prevede che una funzione sia un metodo senza stato nello script Python che elabora l'input e produce output. Per impostazione predefinita, il runtime prevede che il `main()` metodo `__init__.py` venga implementato come metodo globale chiamato nel file. È inoltre possibile [specificare un punto](#alternate-entry-point)di ingresso alternativo.

I dati da trigger e associazioni vengono associati alla `name` funzione tramite gli attributi del metodo utilizzando la proprietà definita nel file *function.json.* Ad esempio, il _file function.json_ riportato di seguito `req`descrive una semplice funzione attivata da una richiesta HTTP denominata :

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

In base a `__init__.py` questa definizione, il file che contiene il codice della funzione potrebbe essere simile all'esempio seguente:Based to this definition, the file that contains the function code might look like the following example:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

È anche possibile dichiarare in modo esplicito i tipi di attributo e il tipo restituito nella funzione usando le annotazioni di tipo Python.You can also explicitly declare the attribute types and return type in the function using Python type annotations. In questo modo è possibile usare le funzionalità di intellisense e completamento automatico fornite da molti editor di codice Python.This helps you use the intellisense and autocomplete features provided by many Python code editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="alternate-entry-point"></a>Punto di ingresso alternativo

È possibile modificare il comportamento predefinito di una `scriptFile` `entryPoint` funzione specificando facoltativamente le proprietà e nel file *function.json.* Ad esempio, il _file function.json riportato di_ seguito indica al runtime di usare il metodo nel file `customentry()` _main.py,_ come punto di ingresso per la funzione di Azure.For example, the function.json below tells the runtime to use the method in the main.py file, as the entry point for your Azure Function.

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

La struttura di cartelle consigliata per un progetto di funzioni Python è simile all'esempio seguente:The recommended folder structure for a Python Functions project looks like the following example:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
La cartella principale\_\_\_\_del progetto ( app ) può contenere i seguenti file:

* *local.settings.json*: Utilizzato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure. Per ulteriori informazioni, vedere [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: contiene l'elenco dei pacchetti che il sistema installa durante la pubblicazione in Azure.
* *host.json*: Contiene le opzioni di configurazione globale che influiscono su tutte le funzioni in un'app per le funzioni.host.json : Contains global configuration options that affect all functions in a function app. Questo file viene pubblicato in Azure. Non tutte le opzioni sono supportate durante l'esecuzione locale. Per ulteriori informazioni, consultate [host.json](functions-host-json.md).
* *.funcignore*: (Facoltativo) dichiara i file che non devono essere pubblicati in Azure.funcignore : (Optional) declares files that should't get published to Azure.
* *.gitignore*: (Facoltativo) dichiara i file esclusi da un repository git, ad esempio local.settings.json.

Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json). 

Quando si distribuisce il progetto in un'app per le funzioni in Azure, l'intero contenuto della cartella del progetto principale (*\_\_app\_*) deve essere incluso nel pacchetto, ma non nella cartella stessa. Si consiglia di mantenere i test in una cartella separata `tests`dalla cartella del progetto, in questo esempio . In questo modo si impedisce la distribuzione di codice di test con l'app. Per ulteriori informazioni, vedere [Unit Test](#unit-testing).

## <a name="import-behavior"></a>Importare il comportamento

È possibile importare moduli nel codice della funzione usando riferimenti relativi e assoluti espliciti. In base alla struttura di cartelle illustrata in precedenza, le seguenti importazioni funzionano dall'interno * \_ \_\_\_dell'app del file di funzione: la mia\_prima\_funzione\\_\_init\_\_.py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Le seguenti importazioni *non funzionano* dall'interno dello stesso file:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Il codice condiviso deve essere contenuto in una cartella separata * \_ \_nell'app\_*. Per fare riferimento ai moduli nella cartella del *codice condiviso,\_* è possibile utilizzare la sintassi seguente:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Trigger e ingressi

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono `function.json` diversi nel file, l'utilizzo è identico nel codice Python.  Le stringhe di connessione o i segreti `local.settings.json` per le origini trigger e input eseguono il mapping ai valori nel file durante l'esecuzione in locale e alle impostazioni dell'applicazione durante l'esecuzione in Azure.Connection strings or secrets for trigger and input sources map to values in the file when running locally, and the application settings when running in Azure. 

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

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Verrà recuperata una voce dall'archivio BLOB di Azure in `obj` base all'ID nell'URL della route e resa disponibile come nel corpo della funzione. _ID_  In questo caso, l'account di archiviazione specificato è la stringa di connessione trovata nell'impostazione dell'app AzureWebJobsStorage, che è lo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per produrre più output, `set()` utilizzare il [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) metodo fornito dall'interfaccia per assegnare un valore all'associazione. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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

L'accesso al logger di runtime [`logging`](https://docs.python.org/3/library/logging.html#module-logging) di Funzioni di Azure è disponibile tramite un gestore radice nell'app per le funzioni. Il logger è associato ad Application Insights e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione della funzione.

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

Per altre informazioni sulla registrazione, vedere [Monitorare le funzioni](functions-monitoring.md)di Azure.To learn more about logging, see Monitor Azure Functions .

## <a name="http-trigger-and-bindings"></a>Trigger E binding HTTP

Il trigger HTTP è definito nel file function.jon. L'associazione `name` deve corrispondere al parametro denominato nella funzione. Negli esempi precedenti viene `req` utilizzato un nome di associazione. Questo parametro è un [oggetto HttpRequest] e viene restituito un oggetto [HttpResponse.]

Dall'oggetto [HttpRequest] è possibile ottenere intestazioni di richiesta, parametri di query, parametri di route e corpo del messaggio. 

L'esempio seguente è tratto dal modello di [trigger HTTP per Python.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python) 

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

In questa funzione, il `name` valore del parametro di query viene ottenuto dal `params` parametro dell'oggetto [HttpRequest.] Il corpo del messaggio con `get_json` codifica JSON viene letto utilizzando il metodo . 

Analogamente, è `status_code` possibile `headers` impostare e per il messaggio di risposta nell'oggetto [Restituito HttpResponse] oggetto.

## <a name="scaling-and-concurrency"></a>Scalabilità e concorrenza

Per impostazione predefinita, Funzioni di Azure monitora automaticamente il carico sull'applicazione e crea istanze host aggiuntive per Python in base alle esigenze. Funzioni utilizza soglie predefinite (non configurabili dall'utente) per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio la validità dei messaggi e la dimensione della coda per QueueTrigger. Per ulteriori informazioni, vedere [Funzionamento dei piani Consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Questo comportamento di ridimensionamento è sufficiente per molte applicazioni. Le applicazioni con una qualsiasi delle seguenti caratteristiche, tuttavia, potrebbero non essere ridimensionate nel modo più efficace:

- L'applicazione deve gestire molte chiamate simultanee.
- L'applicazione elabora un numero elevato di eventi di I/O.The application processes a large number of I/O events.
- L'applicazione è associata a I/O.The application is I/O bound.

In questi casi, è possibile migliorare ulteriormente le prestazioni utilizzando modelli asincroni e utilizzando più processi di lavoro linguistici.

### <a name="async"></a>Async

Poiché Python è un runtime a thread singolo, un'istanza host per Python può elaborare una sola chiamata di funzione alla volta. Per le applicazioni che elaborano un numero elevato di eventi di I/O e/o è associato a I/O, è possibile migliorare le prestazioni eseguendo le funzioni in modo asincrono.

Per eseguire una funzione in `async def` modo asincrono, usare l'istruzione , che esegue direttamente la funzione con [asyncio:To](https://docs.python.org/3/library/asyncio.html) run a function asynchronously, use the statement, which runs the function with asyncio directly:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Una funzione `async` senza la parola chiave viene eseguita automaticamente in un pool di thread asyncio:A function without the keyword is run automatically in an asyncio thread-pool:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Utilizzare più processi di lavoro linguistico

Per impostazione predefinita, ogni istanza host di Functions dispone di un singolo processo di lavoro del linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) utilizzando l'impostazione [dell'applicazione FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) Funzioni di Azure tenta quindi di distribuire in modo uniforme le chiamate di funzione simultanee tra questi worker. 

Il FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni durante la scalabilità orizzontale dell'applicazione per soddisfare la domanda. 

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) una funzione durante l'esecuzione, includere l'argomento nella relativa firma. 

Ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) ha i seguenti attributi di stringa:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Variabili globali

Non è garantito che lo stato dell'app verrà mantenuto per le esecuzioni future. Tuttavia, il runtime funzioni di Azure spesso riutilizza lo stesso processo per più esecuzioni della stessa app. Per memorizzare nella cache i risultati di un calcolo costoso, dichiararlo come variabile globale. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variabili di ambiente

In [Funzioni, le impostazioni dell'applicazione,](functions-app-settings.md)ad esempio le stringhe di connessione del servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a `import os` queste impostazioni `setting = os.environ["setting-name"]`dichiarando e quindi utilizzando , .

Nell'esempio seguente viene ottenuta [l'impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings), con la chiave denominata `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Per lo sviluppo locale, le impostazioni dell'applicazione vengono [mantenute nel file local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versione Python 

Funzioni di Azure supporta le versioni di Python seguenti:Azure Functions supports the following Python versions:

| Versione di Funzioni | Versioni<sup>*</sup> Python |
| ----- | ----- |
| 3.x | 3.8<br/>3,7<br/>3.6 |
| 2.x | 3,7<br/>3.6 |

<sup>*</sup>Distribuzioni ufficiali di CPython

Per richiedere una versione specifica di Python quando si `--runtime-version` crea [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) l'app per le funzioni in Azure, usare l'opzione del comando. La versione di runtime `--functions-version` di Funzioni è impostata dall'opzione. La versione Python viene impostata quando viene creata l'app per le funzioni e non può essere modificata.  

Quando viene eseguito in locale, il runtime utilizza la versione di Python disponibile. 

## <a name="package-management"></a>Gestione dei pacchetti

Quando si sviluppa in locale usando Azure Functions Core Tools oppure Visual Studio Code, aggiungere i nomi e le versioni dei pacchetti necessari al file `requirements.txt` e installarli con `pip`. 

Ad esempio, per installare il pacchetto `requests` PyPI è possibile usare il file di requisiti e il comando pip seguenti.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Pubblicazione in Azure

Quando si è pronti per la pubblicazione, assicurarsi che tutte le dipendenze disponibili pubblicamente siano elencate nel file requirements.txt, che si trova nella radice della directory del progetto. 

I file e le cartelle di progetto esclusi dalla pubblicazione, inclusa la cartella dell'ambiente virtuale, sono elencati nel file .funcignore.

Sono supportate tre azioni di compilazione per la pubblicazione del progetto Python in Azure:There are three build actions supported for publishing your Python project to Azure:

+ Compilazione remota: le dipendenze vengono ottenute in remoto in base al contenuto del file requirements.txt. [La compilazione remota](functions-deployment-technologies.md#remote-build) è il metodo di compilazione consigliato. Remoto è anche l'opzione di compilazione predefinita degli strumenti di Azure.Remote also is the default build option of Azure tooling. 
+ Compilazione locale: le dipendenze vengono ottenute localmente in base al contenuto del file requirements.txt. 
+ Dipendenze personalizzate: il progetto utilizza pacchetti non disponibili pubblicamente per i nostri strumenti. (Richiede Docker.)

Per compilare le dipendenze e pubblicare usando un sistema di recapito continuo (CD), usare Le pipeline di Azure.To build your dependencies and publish using a continuous delivery (CD) system, [use Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Compilazione remota

Per impostazione predefinita, gli strumenti di base di Funzioni di Azure richiedono una compilazione remota quando si usa il comando di [pubblicazione func azure functionapp](functions-run-local.md#publish) seguente per pubblicare il progetto Python in Azure.By default, the Azure Functions Core Tools requests a remote build when you use the following func azure functionapp publish command to publish your Python project to Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

L'estensione Funzioni di [Azure per il codice](functions-create-first-function-vs-code.md#publish-the-project-to-azure) di Visual Studio richiede anche una compilazione remota per impostazione predefinita. 

### <a name="local-build"></a>Compilazione locale

È possibile impedire l'esecuzione di una compilazione remota usando il comando [func azure functionapp publish](functions-run-local.md#publish) seguente per pubblicare con una compilazione locale. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure. 

Utilizzando `--build local` l'opzione, le dipendenze del progetto vengono lette dal file requirements.txt e i pacchetti dipendenti vengono scaricati e installati localmente. I file di progetto e le dipendenze vengono distribuiti dal computer locale ad Azure.Project files and dependencies are deployed from your local computer to Azure. Ciò comporta il caricamento di un pacchetto di distribuzione più grande in Azure.This results in a larger deployment package being uploaded to Azure. Se per qualche motivo le dipendenze nel file requirements.txt non possono essere acquisite da Core Tools, è necessario utilizzare l'opzione delle dipendenze personalizzate per la pubblicazione. 

### <a name="custom-dependencies"></a>Dipendenze personalizzate

Se il progetto utilizza pacchetti non disponibili pubblicamente per i nostri strumenti, \_ \_\_\_puoi renderli disponibili per la tua app inserendoli nella directory /.python_packages dell'app. Prima della pubblicazione, eseguire il comando seguente per installare localmente le dipendenze:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Quando si usano dipendenze personalizzate, è necessario utilizzare l'opzione `--no-build` di pubblicazione, poiché le dipendenze sono già state installate.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

## <a name="unit-testing"></a>Testing unità

Le funzioni scritte in Python possono essere testate come altro codice Python usando framework di test standard. Per la maggior parte delle associazioni, è possibile creare un oggetto di `azure.functions` input fittizio creando un'istanza di una classe appropriata dal pacchetto. Poiché [`azure.functions`](https://pypi.org/project/azure-functions/) il pacchetto non è immediatamente disponibile, `requirements.txt` assicurarsi di installarlo tramite il file come descritto nella sezione precedente relativa alla gestione dei [pacchetti.](#package-management) 

Ad esempio, di seguito è riportato un test fittizio di una funzione attivata HTTP:For example, following is a mock test of an HTTP triggered function:

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

Ecco un altro esempio, con una funzione attivata dalla coda:Here is another example, with a queue triggered function:

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
## <a name="temporary-files"></a>File temporanei

Il `tempfile.gettempdir()` metodo restituisce una cartella `/tmp`temporanea, che su Linux è . L'applicazione può utilizzare questa directory per archiviare i file temporanei generati e utilizzati dalle funzioni durante l'esecuzione. 

> [!IMPORTANT]
> I file scritti nella directory temporanea non sono garantiti per mantenere tra le chiamate. Durante la scalabilità orizzontale, i file temporanei non vengono condivisi tra le istanze. 

Nell'esempio seguente viene creato un file`/tmp`temporaneo denominato nella directory temporanea ( ):

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

Si consiglia di mantenere i test in una cartella separata dalla cartella del progetto. In questo modo si impedisce la distribuzione di codice di test con l'app. 

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

### <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

Funzioni di Azure supporta la condivisione di risorse tra origini (CORS). CORS è configurato [nel portale](functions-how-to-use-azure-function-app-settings.md#cors) e tramite l'interfaccia della riga di comando di [Azure.](/cli/azure/functionapp/cors) L'elenco delle origini consentite CORS si applica a livello di app per le funzioni. Con CORS abilitato, le `Access-Control-Allow-Origin` risposte includono l'intestazione. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](functions-how-to-use-azure-function-app-settings.md#cors).

L'elenco delle origini consentite non è attualmente supportato per le app per le funzioni Python.The allowed origins list [isn't currently supported](https://github.com/Azure/azure-functions-python-worker/issues/444) for Python function apps. A causa di questa limitazione, `Access-Control-Allow-Origin` è necessario impostare espressamente l'intestazione nelle funzioni HTTP, come illustrato nell'esempio seguente:Because of this limitation, you must expressly set the header in your HTTP functions, as shown in the following example:

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

Assicurarsi di aggiornare anche il file function.json per supportare il metodo HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Questo metodo HTTP viene utilizzato dai browser Web per negoziare l'elenco delle origini consentite. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Documentazione dell'API del pacchetto di Funzioni di AzureAzure Functions package API documentation](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di AzureAzure Functions triggers and bindings](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest (Richiesta http)]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
