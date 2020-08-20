---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: f9b81a7263dc9a1bdae9fd881519ac734da2c6bc
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642198"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md).

Per alcuni esempi di progetti di Funzioni autonomi in Python, vedere [Esempi di funzioni in Python](/samples/browse/?products=azure-functions&languages=python).

## <a name="programming-model"></a>Modello di programmazione

Funzioni di Azure si aspetta che una funzione sia un metodo senza stato nello script di Python che elabora l'input e genera l'output. Per impostazione predefinita, il runtime si aspetta che il metodo venga implementato come metodo globale denominato `main()` nel file `__init__.py`. È anche possibile [specificare un punto di ingresso alternativo](#alternate-entry-point).

I dati provenienti da trigger e binding sono associati alla funzione tramite attributi di metodo con la proprietà `name` definita nel file *function.json*. Ad esempio, il file _function.json_ seguente descrive una semplice funzione attivata da una richiesta HTTP denominata `req`:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

In base a questa definizione, il file `__init__.py` che contiene il codice della funzione potrebbe essere simile all'esempio seguente:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

È anche possibile dichiarare esplicitamente i tipi di attributi e il tipo restituito nella funzione usando le annotazioni di tipo di Python. In questo modo è possibile usare le funzionalità di IntelliSense e di completamento automatico fornite da molti editor di codice Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="alternate-entry-point"></a>Punto di ingresso alternativo

È possibile cambiare il comportamento predefinito di una funzione specificando facoltativamente le proprietà `scriptFile` e `entryPoint` nel file *function.json*. Ad esempio, il file _function.json_ seguente indica al runtime di usare il metodo `customentry()` nel file _main.py_ come punto di ingresso per la funzione di Azure.

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

La struttura di cartelle per consigliata per un progetto di Funzioni per Python è simile all'esempio seguente:

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
 | - Dockerfile
 tests
```
La cartella principale del progetto (\_\_app\_\_) può contenere i file seguenti:

* *local.settings.json*: viene usato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure. Per altre informazioni, vedere [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: contiene l'elenco di pacchetti che il sistema installa durante la pubblicazione in Azure.
* *host.json*: contiene le opzioni di configurazione globali che interessano tutte le funzioni in un'app per le funzioni. Questo file viene pubblicato in Azure. Non tutte le opzioni sono supportate durante l'esecuzione in locale. Per altre informazioni, vedere [host.json](functions-host-json.md).
* *.funcignore*: (facoltativo) dichiara i file che non devono essere pubblicati in Azure.
* *Dockerfile*: (facoltativo) viene usato per la pubblicazione del progetto in un [contenitore personalizzato](functions-create-function-linux-custom-image.md).

Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json).

Quando si distribuisce un progetto in un'app per le funzioni in Azure, è necessario includere nel pacchetto l'intero contenuto della cartella principale del progetto, *\_\_app\_\_* , ma non la cartella stessa. È consigliabile mantenere i test in una cartella separata da quella del progetto, in questo esempio `tests`. In questo modo si evita di distribuire il codice di test con l'app. Per altre informazioni, vedere [Testing unità](#unit-testing).

## <a name="import-behavior"></a>Importare il comportamento

È possibile importare moduli nel codice della funzione usando riferimenti espliciti relativi e assoluti. In base alla struttura delle cartelle illustrata in precedenza, le importazioni seguenti funzionano dal file della funzione *\_\_app\_\_\my\_first\_function\\_\_init\_\_.py*:

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

Le importazioni seguenti *non funzionano* dall'interno dello stesso file:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Il codice condiviso deve essere mantenuto in una cartella separata in *\_\_app\_\_* . Per fare riferimento a moduli nella cartella *shared\_code*, è possibile usare la sintassi seguente:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Trigger e input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono diversi nel file `function.json`, l'utilizzo nel codice Python è identico.  Le stringhe di connessione o i segreti per le origini di trigger e input vengono mappati ai valori del file `local.settings.json` durante l'esecuzione in locale e alle impostazioni dell'applicazione durante l'esecuzione in Azure.

Ad esempio, il codice seguente dimostra la differenza tra i due scenari:

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

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Verrà recuperata una voce da Archiviazione BLOB di Azure in base all'_ID_ nell'URL di route e verrà resa disponibile come `obj` nel corpo della funzione.  In questo caso, l'account di archiviazione specificato è la stringa di connessione disponibile nell'impostazione dell'app AzureWebJobsStorage, che corrisponde allo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per generare più output, usare il metodo `set()` fornito dall'interfaccia [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) per assegnare un valore al binding. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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

Per altre informazioni sulla registrazione, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Trigger e binding HTTP

Il trigger HTTP è definito nel function.jssu file. Il valore `name` del binding deve corrispondere al parametro denominato nella funzione.
Negli esempi precedenti viene usato il nome di binding `req`. Questo parametro è un oggetto [HttpRequest] e viene restituito un oggetto [HttpResponse].

Dall'oggetto [HttpRequest] è possibile ottenere intestazioni di richieste, parametri di query, parametri di route e il corpo del messaggio.

L'esempio seguente fa riferimento al [modello di trigger HTTP per Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

In questa funzione il valore del parametro di query `name` si ottiene dal parametro `params` dell'oggetto [HttpRequest]. Il corpo del messaggio con codifica JSON viene letto usando il metodo `get_json`.

Allo stesso modo, è possibile impostare `status_code` e `headers` per il messaggio di risposta nell'oggetto [HttpResponse] restituito.

## <a name="scaling-and-concurrency"></a>Scalabilità e concorrenza

Per impostazione predefinita, Funzioni di Azure monitora automaticamente il carico dell'applicazione e crea istanze host aggiuntive per Python, se necessario. Funzioni usa soglie predefinite (non configurabili dall'utente) per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio l'età dei messaggi e le dimensioni della coda per QueueTrigger. Per altre informazioni, vedere [Come funzionano i piani a consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Questo comportamento di scalabilità è sufficiente per molte applicazioni. Le applicazioni con le caratteristiche seguenti, tuttavia, potrebbero non essere dimensionate in modo altrettanto efficace:

- L'applicazione deve gestire molte chiamate simultanee.
- L'applicazione elabora un numero elevato di eventi di I/O.
- L'applicazione è associata all'I/O.

In questi casi, è possibile migliorare ulteriormente le prestazioni tramite modelli asincroni e usando più processi di lavoro del linguaggio.

### <a name="async"></a>Async

Poiché Python è un runtime a thread singolo, un'istanza host per Python può elaborare solo una chiamata di funzione alla volta. Per le applicazioni che elaborano un numero elevato di eventi di I/O e/o sono associate all'I/O, è possibile migliorare le prestazioni eseguendo le funzioni in modo asincrono.

Per eseguire una funzione in modo asincrono, usare l'istruzione `async def`, che esegue la funzione direttamente con [asyncio](https://docs.python.org/3/library/asyncio.html):

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Una funzione senza la parola chiave `async` viene eseguita automaticamente in un pool di thread asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usare più processi di lavoro del linguaggio

Per impostazione predefinita, ogni istanza host di Funzioni include un singolo processo di lavoro del linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) usando l'impostazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) dell'applicazione. Funzioni di Azure prova quindi a distribuire uniformemente le chiamate di funzioni simultanee tra questi processi di lavoro.

FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni quando le istanze dell'applicazione vengono aumentate per soddisfare la domanda.

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere l'argomento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) nella relativa firma.

Ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) prevede gli attributi di stringa seguenti:

`function_directory` La directory in cui è in esecuzione la funzione.

`function_name` Il nome della funzione.

`invocation_id` L'ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Variabili globali

Non è garantito che lo stato dell'app verrà preservato per le esecuzioni future. Tuttavia, il runtime di Funzioni di Azure spesso riutilizza lo stesso processo per più esecuzioni della stessa app. Per memorizzare nella cache i risultati di un calcolo oneroso, dichiararli come variabile globale.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni le [impostazioni dell'applicazione](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni dichiarando `import os` e quindi usando `setting = os.environ["setting-name"]`.

L'esempio seguente ottiene l'[impostazione applicazione](functions-how-to-use-azure-function-app-settings.md#settings) con la chiave denominata `myAppSetting`:

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

Funzioni di Azure supporta le versioni di Python seguenti:

| Versione di Funzioni | Versioni di Python<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3,7<br/>3.6 |
| 2.x | 3,7<br/>3.6 |

<sup>*</sup>Distribuzioni CPython ufficiali

Per richiedere una versione specifica di Python quando si crea l'app per le funzioni in Azure, usare l'opzione `--runtime-version` del comando [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create). La versione del runtime di Funzioni è impostata dall'opzione `--functions-version`. La versione di Python viene impostata quando l'app per le funzioni viene creata e non si può cambiare.

Per l'esecuzione in locale, il runtime usa la versione di Python disponibile.

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

I file e le cartelle di progetto esclusi dalla pubblicazione, inclusa la cartella dell'ambiente virtuale, sono elencati nel file con estensione funcignore.

Sono supportate tre azioni di compilazione per la pubblicazione del progetto Python in Azure: compilazione remota, compilazione locale e compilazioni usando dipendenze personalizzate.

È anche possibile usare Azure Pipelines per compilare le dipendenze e pubblicare usando il recapito continuo (CD). Per altre informazioni, vedere [recapito continuo con Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Compilazione remota

Quando si usa la compilazione remota, le dipendenze ripristinate nel server e le dipendenze native corrispondono all'ambiente di produzione. In questo modo si ottiene un pacchetto di distribuzione più piccolo da caricare. Usare la compilazione remota quando si sviluppano app Python in Windows. Se il progetto ha dipendenze personalizzate, è possibile [usare la compilazione remota con l'URL dell'indice aggiuntivo](#remote-build-with-extra-index-url).

Le dipendenze vengono ottenute in modalità remota in base al contenuto del file di requirements.txt. La [compilazione remota](functions-deployment-technologies.md#remote-build) è il metodo di compilazione consigliato. Per impostazione predefinita, Azure Functions Core Tools richiede una compilazione remota quando si usa il comando [func azure functionapp publish](functions-run-local.md#publish) per pubblicare il progetto Python in Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

Anche l'[estensione Funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) richiede una compilazione remota per impostazione predefinita.

### <a name="local-build"></a>Compilazione locale

Le dipendenze vengono ottenute localmente in base al contenuto del file di requirements.txt. È possibile evitare l'esecuzione di una compilazione remota usando il comando [func azure functionapp publish](functions-run-local.md#publish) per pubblicare con una compilazione locale.

```command
func azure functionapp publish <APP_NAME> --build local
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

Usando l'opzione `--build local`, le dipendenze del progetto vengono lette dal file requirements.txt e i pacchetti dipendenti vengono scaricati e installati localmente. I file e le dipendenze del progetto vengono distribuiti dal computer locale in Azure. Questo comporta il caricamento di un pacchetto di distribuzione di dimensioni maggiori in Azure. Se per qualche motivo le dipendenze nel file requirements.txt non possono essere acquisite da Core Tools, è necessario usare l'opzione delle dipendenze personalizzate per la pubblicazione.

Non è consigliabile usare le compilazioni locali per lo sviluppo in locale in Windows.

### <a name="custom-dependencies"></a>Dipendenze personalizzate

Quando il progetto ha dipendenze non trovate nell' [indice del pacchetto python](https://pypi.org/), esistono due modi per compilare il progetto. Il metodo di compilazione dipende dalla modalità di compilazione del progetto.

#### <a name="remote-build-with-extra-index-url"></a>Compilazione remota con URL dell'indice aggiuntivo

Quando i pacchetti sono disponibili da un indice di pacchetto personalizzato accessibile, utilizzare una compilazione remota. Prima della pubblicazione, assicurarsi di [creare un'impostazione dell'app](functions-how-to-use-azure-function-app-settings.md#settings) denominata `PIP_EXTRA_INDEX_URL` . Il valore di questa impostazione è l'URL dell'indice del pacchetto personalizzato. L'utilizzo di questa impostazione indica che la compilazione remota viene eseguita `pip install` utilizzando l' `--extra-index-url` opzione. Per altre informazioni, vedere la [documentazione sull'installazione PIP di Python](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

È anche possibile usare le credenziali di autenticazione di base con gli URL degli indici dei pacchetti aggiuntivi. Per altre informazioni, vedere [credenziali di autenticazione di base](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) nella documentazione di Python.

#### <a name="install-local-packages"></a>Installare i pacchetti locali

Se il progetto usa pacchetti non disponibili pubblicamente per gli strumenti di Azure, è possibile renderli disponibili nell'app inserendoli nella directory \_\_app\_\_/.python_packages. Prima della pubblicazione, eseguire il comando seguente per installare le dipendenze in locale:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Quando si utilizzano dipendenze personalizzate, è necessario utilizzare l' `--no-build` opzione di pubblicazione, dal momento che le dipendenze sono già state installate nella cartella del progetto.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

## <a name="unit-testing"></a>Testing unità

Le funzioni scritte in Python possono essere testate come qualsiasi altro codice Python usando framework di test standard. Per la maggior parte dei binding, è possibile creare un oggetto di input fittizio creando un'istanza di una classe appropriata dal pacchetto `azure.functions`. Poiché il pacchetto [`azure.functions`](https://pypi.org/project/azure-functions/) non è immediatamente disponibile, assicurarsi di installarlo tramite il file `requirements.txt`, come descritto nella sezione [Gestione dei pacchetti](#package-management) precedente.

Ad esempio, di seguito è riportato un test fittizio di una funzione attivata tramite HTTP:

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

Ecco un altro esempio, con una funzione attivata da una coda:

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

Il metodo `tempfile.gettempdir()` restituisce una cartella temporanea, che in Linux è `/tmp`. L'applicazione può usare questa directory per archiviare i file temporanei generati e usati dalle funzioni durante l'esecuzione.

> [!IMPORTANT]
> Non è garantito che i file scritti nella directory temporanea vengano mantenuti tra le chiamate. Durante l'aumento di istanze, i file temporanei non vengono condivisi tra le istanze.

L'esempio seguente crea un file temporaneo denominato nella directory temporanea (`/tmp`):

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

È consigliabile mantenere i test in una cartella separata da quella del progetto. In questo modo si evita di distribuire il codice di test con l'app.

## <a name="preinstalled-libraries"></a>Librerie preinstallate

Sono disponibili alcune librerie con il runtime di funzioni di Python.

### <a name="python-standard-library"></a>Libreria standard Python

La libreria standard Python contiene un elenco di moduli Python predefiniti che vengono forniti con ogni distribuzione di Python. La maggior parte di queste librerie consente di accedere alle funzionalità del sistema, ad esempio l'I/O di file. Nei sistemi Windows, queste librerie vengono installate con Python. Nei sistemi basati su UNIX vengono forniti dalle raccolte di pacchetti.

Per visualizzare i dettagli completi dell'elenco di queste librerie, visitare i collegamenti seguenti:

* [Libreria standard Python 3,6](https://docs.python.org/3.6/library/)
* [Libreria standard Python 3,7](https://docs.python.org/3.7/library/)
* [Libreria standard Python 3,8](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Dipendenze di lavoro Python in funzioni di Azure

Il ruolo di lavoro Python di funzioni richiede un set specifico di librerie. È anche possibile usare queste librerie nelle funzioni, ma non fanno parte dello standard Python. Se le funzioni si basano su una di queste librerie, potrebbero non essere disponibili per il codice durante l'esecuzione all'esterno di funzioni di Azure. È possibile trovare un elenco dettagliato delle dipendenze nella sezione **installazione \_ richiesta** del file [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Se il requirements.txt dell'app per le funzioni contiene una `azure-functions-worker` voce, rimuoverla. Il ruolo di lavoro funzioni è gestito automaticamente dalla piattaforma funzioni di Azure e viene aggiornato periodicamente con nuove funzionalità e correzioni di bug. L'installazione manuale di una versione precedente del ruolo di lavoro in requirements.txt può causare problemi imprevisti.

### <a name="azure-functions-python-library"></a>Libreria Python di funzioni di Azure

Ogni aggiornamento del ruolo di lavoro Python include una nuova versione della [libreria Python di funzioni di Azure (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Questo approccio rende più semplice aggiornare continuamente le app per le funzioni Python, perché ogni aggiornamento è compatibile con le versioni precedenti. È possibile trovare un elenco di versioni di questa libreria in [funzioni di Azure pypi](https://pypi.org/project/azure-functions/#history).

La versione della libreria di runtime è fissa da Azure e non può essere sottoposta a override da requirements.txt. La `azure-functions` voce requirements.txt è solo per la divulgazione e la consapevolezza dei clienti.

Usare il codice seguente per tenere traccia della versione effettiva della libreria di funzioni Python nel runtime:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Librerie di sistema di runtime

Per un elenco delle librerie di sistema preinstallate nelle immagini Docker per il ruolo di lavoro Python, seguire i collegamenti seguenti:

|  Runtime di Funzioni  | Versione di Debian | Versioni di Python |
|------------|------------|------------|
| Versione 2.x | Estendi  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versione 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS è completamente supportato per le app per le funzioni Python.

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Di seguito è riportato un elenco di guide per la risoluzione dei problemi comuni:

* [ModuleNotFoundError e ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Impossibile importare ' cygrpc '](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Documentazione dell'API del pacchetto di Funzioni di Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
