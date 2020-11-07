---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: cc99a8c10ecefc063fdb89c61bdaeb0e686b1a82
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358049"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md).

Gli sviluppatori di Python possono anche essere interessati a uno degli articoli seguenti:

| Introduzione | Concetti| Scenari/esempi |
| -- | -- | -- | 
| <ul><li>[Funzione Python con Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)</li><li>[Funzione Python con terminale/prompt dei comandi](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python)</li></ul> | <ul><li>[Guida per sviluppatori](functions-reference.md)</li><li>[Opzioni di hosting](functions-scale.md)</li><li>[Considerazioni sulle prestazioni &nbsp;](functions-best-practices.md)</li></ul> | <ul><li>[Classificazione di immagini con PyTorch](machine-learning-pytorch.md)</li><li>[Esempio di automazione di Azure](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine Learning con TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Esplora esempi di Python](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

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

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true) per associare input e output ai metodi dell'utente.

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
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
La cartella principale del progetto (<project_root>) può contenere i file seguenti:

* *local.settings.json* : viene usato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure. Per altre informazioni, vedere [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt* : contiene l'elenco dei pacchetti Python installati dal sistema durante la pubblicazione in Azure.
* *host.json* : contiene le opzioni di configurazione globali che interessano tutte le funzioni in un'app per le funzioni. Questo file viene pubblicato in Azure. Non tutte le opzioni sono supportate durante l'esecuzione in locale. Per altre informazioni, vedere [host.json](functions-host-json.md).
* *. VSCODE/* : (facoltativo) contiene la configurazione VSCODE dell'archivio. Per altre informazioni, vedere [impostazione di VSCode](https://code.visualstudio.com/docs/getstarted/settings).
* *. venv/* : (facoltativo) contiene un ambiente virtuale Python utilizzato dallo sviluppo locale.
* *Dockerfile* : (facoltativo) usato per la pubblicazione del progetto in un [contenitore personalizzato](functions-create-function-linux-custom-image.md).
* *test/* : (facoltativo) contiene i test case dell'app per le funzioni.
* *. funcignore* : (facoltativo) dichiara i file che non devono essere pubblicati in Azure. In genere, questo file contiene `.vscode/` per ignorare l'impostazione dell'editor, `.venv/` per ignorare l'ambiente virtuale Python locale, `tests/` per ignorare i test case e `local.settings.json` per impedire la pubblicazione delle impostazioni dell'app locale.

Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json).

Quando si distribuisce il progetto in un'app per le funzioni in Azure, l'intero contenuto della cartella principale del progetto ( *<project_root>* ) deve essere incluso nel pacchetto, ma non nella cartella stessa, che significa che `host.json` deve trovarsi nella radice del pacchetto. In questo esempio si consiglia di mantenere i test in una cartella insieme ad altre funzioni `tests/` . Per altre informazioni, vedere [Testing unità](#unit-testing).

## <a name="import-behavior"></a>Importare il comportamento

È possibile importare moduli nel codice della funzione utilizzando riferimenti assoluti e relativi. In base alla struttura di cartelle illustrata in precedenza, le importazioni seguenti funzionano dal file di funzione *<project_root> \My \_ prima \_ funzione \\ _ \_ init \_ \_ . py* :

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  Il *shared_code/* cartella deve contenere un \_ \_ file init \_ \_ . py per contrassegnarlo come pacchetto python quando si usa la sintassi di importazione assoluta.

La seguente \_ \_ \_ \_ importazione di app e oltre l'importazione relativa di primo livello sono deprecate, perché non è supportata dal controllo dei tipi statici e non è supportata dai framework di test Python:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
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

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Verrà recuperata una voce da Archiviazione BLOB di Azure in base all' _ID_ nell'URL di route e verrà resa disponibile come `obj` nel corpo della funzione.  In questo caso, l'account di archiviazione specificato è la stringa di connessione disponibile nell'impostazione dell'app AzureWebJobsStorage, che corrisponde allo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per generare più output, usare il metodo `set()` fornito dall'interfaccia [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) per assegnare un valore al binding. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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

## <a name="scaling-and-performance"></a>Scalabilità e prestazioni

È importante comprendere come funzionano le funzioni e in che modo le prestazioni influiscono sul modo in cui l'app per le funzioni viene ridimensionata. Questa operazione è particolarmente importante quando si progettano app a prestazioni elevate. Di seguito sono riportati alcuni fattori da considerare durante la progettazione, la scrittura e la configurazione delle app per le funzioni.

### <a name="horizontal-scaling"></a>Scalabilità orizzontale
Per impostazione predefinita, Funzioni di Azure monitora automaticamente il carico dell'applicazione e crea istanze host aggiuntive per Python, se necessario. Funzioni utilizza soglie predefinite per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio l'età dei messaggi e le dimensioni della coda per QueueTrigger. Queste soglie non sono configurabili dall'utente. Per altre informazioni, vedere [Come funzionano i piani a consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="improving-throughput-performance"></a>Miglioramento delle prestazioni della velocità effettiva

Una chiave per migliorare le prestazioni consiste nel comprendere il modo in cui l'app usa le risorse e la possibilità di configurare l'app per le funzioni di conseguenza.

#### <a name="understanding-your-workload"></a>Informazioni sul carico di lavoro

Le configurazioni predefinite sono adatte alla maggior parte delle applicazioni di funzioni di Azure. Tuttavia, è possibile migliorare le prestazioni della velocità effettiva delle applicazioni usando le configurazioni basate sul profilo del carico di lavoro. Il primo passaggio consiste nel comprendere il tipo di carico di lavoro in esecuzione.

|&nbsp;| Carico di lavoro associato a I/O | Carico di lavoro associato alla CPU |
|--| -- | -- |
|Caratteristiche delle app per le funzioni| <ul><li>L'app deve gestire molte chiamate simultanee.</li> <li> L'app elabora un numero elevato di eventi di I/O, ad esempio chiamate di rete e lettura/scrittura su disco.</li> </ul>| <ul><li>L'app esegue calcoli a esecuzione prolungata, ad esempio il ridimensionamento delle immagini.</li> <li>L'app esegue la trasformazione dei dati.</li> </ul> |
|Esempi| <ul><li>API Web</li><ul> | <ul><li>Elaborazione dati</li><li> Inferenza di Machine Learning</li><ul>|


> [!NOTE]
>  Poiché il carico di lavoro delle funzioni reali è spesso una combinazione di I/O e di CPU, è consigliabile profilare il carico di lavoro in base ai carichi di produzione realistici.


#### <a name="performance-specific-configurations"></a>Configurazioni specifiche delle prestazioni

Dopo aver compreso il profilo del carico di lavoro dell'app per le funzioni, di seguito sono riportate le configurazioni che è possibile usare per migliorare le prestazioni della velocità effettiva delle funzioni.

##### <a name="async"></a>Async

Poiché [Python è un runtime a thread singolo](https://wiki.python.org/moin/GlobalInterpreterLock), un'istanza host per Python può elaborare solo una chiamata di funzione alla volta. Per le applicazioni che elaborano un numero elevato di eventi di I/o e/o è associato a I/o, è possibile migliorare significativamente le prestazioni eseguendo funzioni in modo asincrono.

Per eseguire una funzione in modo asincrono, usare l'istruzione `async def`, che esegue la funzione direttamente con [asyncio](https://docs.python.org/3/library/asyncio.html):

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Di seguito è riportato un esempio di una funzione con trigger HTTP che usa il client http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Una funzione senza la parola chiave `async` viene eseguita automaticamente in un pool di thread asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

Per ottenere il massimo vantaggio delle funzioni in esecuzione in modo asincrono, è necessario implementare anche la libreria e l'operazione di I/O usata nel codice. L'utilizzo di operazioni di I/O sincrone in funzioni definite come asincrone **può compromettere** le prestazioni complessive.

Di seguito sono riportati alcuni esempi di librerie client che hanno implementato il modello asincrono:
- [aiohttp](https://pypi.org/project/aiohttp/) -client/server http per asyncio 
- [Flussi](https://docs.python.org/3/library/asyncio-stream.html) di primitive API per async/await pronti per l'uso con la connessione di rete
- [Coda Janus](https://pypi.org/project/janus/) -coda thread-safe Asyncio per Python
- [pyzmq](https://pypi.org/project/pyzmq/) -binding Python per ZeroMQ
 

##### <a name="use-multiple-language-worker-processes"></a>Usare più processi di lavoro del linguaggio

Per impostazione predefinita, ogni istanza host di Funzioni include un singolo processo di lavoro del linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) usando l'impostazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) dell'applicazione. Funzioni di Azure prova quindi a distribuire uniformemente le chiamate di funzioni simultanee tra questi processi di lavoro.

Per le app associate alla CPU, è necessario impostare il numero di Language worker in modo che sia uguale o superiore al numero di core disponibili per ogni app per le funzioni. Per altre informazioni, vedere [SKU di istanze disponibili](functions-premium-plan.md#available-instance-skus). 

Le app con binding i/O possono anche trarre vantaggio dall'aumento del numero di processi di lavoro oltre il numero di core disponibili. Tenere presente che l'impostazione del numero di ruoli di lavoro troppo elevata può influisca sulle prestazioni complessive a causa del numero maggiore di cambi di contesto richiesti. 

FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni quando le istanze dell'applicazione vengono aumentate per soddisfare la domanda.


## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere l'argomento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) nella relativa firma.

Ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) prevede gli attributi di stringa seguenti:

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

Prendere *my_second_function* come esempio, di seguito è riportato un test fittizio di una funzione attivata tramite http:

Per prima cosa è necessario creare *<project_root>/my_second_function/function.jssul* file e definire questa funzione come trigger http.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
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

A questo punto, è possibile implementare *my_second_function* e *shared_code. My _second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

È possibile iniziare a scrivere test case per il trigger http.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Nell' `.venv` ambiente virtuale python installare il Framework di test Python preferito, ad esempio. `pip install pytest` È sufficiente eseguire `pytest tests` per verificare il risultato del test.

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

* [Documentazione dell'API del pacchetto di Funzioni di Azure](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python&preserve-view=true
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python&preserve-view=true
