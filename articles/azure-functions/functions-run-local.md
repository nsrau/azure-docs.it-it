---
title: Sviluppare ed eseguire localmente le funzioni di Azure | Documentazione Microsoft
description: Informazioni su come scrivere codice per le funzioni di Azure e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2016
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codice per le funzioni di Azure e testarle in locale

È possibile usare l'editor di codice e gli strumenti di sviluppo locale preferiti per eseguire il runtime di Funzioni di Azure localmente. Attivare gli eventi in Azure ed eseguire il debug delle funzioni C# e JavaScript.

Per iniziare, installare [Strumenti di base di Funzioni di Azure] da npm. Strumenti di base di Funzioni di Azure è una versione locale del runtime di Funzioni di Azure che è possibile eseguire nel computer Windows locale. Non è un emulatore o simulatore. Si tratta dello stesso runtime eseguito in Azure.

[Strumenti di base di Funzioni di Azure] aggiunge gli alias di comando seguenti:
- `func`
- `azfun`
- `azurefunctions`

Strumenti di base di Funzioni di Azure è [open source ed è ospitato su GitHub](https://github.com/azure/azure-functions-cli). Per registrare una richiesta per un bug o una funzionalità [aprire un problema di GitHub](https://github.com/azure/azure-functions-cli/issues).

## <a name="create-a-local-functions-project"></a>Creare un progetto Funzioni locale

Quando è eseguito in locale, un progetto Funzioni è una directory che presenta i file host.json e local.settings.json. Questa directory è l'equivalente di un'app per le funzioni in Azure. Per altre informazioni sulla struttura delle cartelle di Funzioni di Azure, vedere la [Guida per sviluppatori di Funzioni di Azure](functions-reference.md#folder-structure).

Al prompt dei comandi, eseguire il seguente comando:

```
func init MyFunctionProj
```

L'output ha un aspetto simile all'esempio seguente:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Per rifiutare esplicitamente la creazione di un repository Git, usare l'opzione `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>File di impostazioni locali

Il file local.settings.json archivia le impostazioni di app, le stringhe di connessione e le impostazioni per Strumenti di base di Funzioni di Azure. Presenta la struttura seguente:

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

Specificare le impostazioni app nella raccolta **Values**. Queste impostazioni possono essere lette come variabili di ambiente. In C# usare `System.Environment.GetEnvironmentVariable` o `ConfigurationManager.AppSettings`. In JavaScript usare `process.env`. Se la stessa impostazione viene specificata come variabile di ambiente di sistema, ha la precedenza sui valori in local.settings.json.

L'impostazione app **AzureWebJobsStorage** è un'impostazione speciale richiesta dal runtime di Funzioni di Azure per tutti i trigger diversi da HTTP. Internamente il runtime crea code per gestire i trigger in questo account di archiviazione. Se non viene specificato un valore per **AzureWebJobsStorage** e si usano trigger diversi da HTTP, viene visualizzato il messaggio seguente:

*Valore mancante per AzureWebJobsStorage in local.settings.json. È necessario per tutti i trigger diversi da HTTP. È possibile eseguire "func azure functionapp fetch-app-settings <functionAppName>" o specificare una stringa di connessione in local.settings.json.*

> [!NOTE]
> È possibile usare l'emulatore di archiviazione locale, tramite la stringa di connessione "AzureWebJobsStorage": "UseDevelopmentStorage=true". Tuttavia potrebbero esservi differenze nel comportamento rispetto al servizio Archiviazione di Azure.

Le seguenti impostazioni personalizzano l'host di Funzioni locale:
- `LocalHttpPort`. La porta predefinita da usare per `func host start` `func run`. L'opzione `--port` della riga di comando ha la precedenza su questo valore.
- `CORS`. Le origini consentite per CORS, come elenco delimitato dalla virgola senza spazi. Usare "*" per consentirli tutti.

È possibile fornire stringhe di connessione nell'oggetto `ConnectionStrings`. Vengono aggiunti all'ambiente con il nome di provider **System.Data.SqlClient**.

La maggior parte dei trigger e associazioni presentano una proprietà **connessione** che è il nome di un'impostazione di app o variabile di ambiente in local.settings.json. Se il valore dell'impostazione app manca, viene visualizzato il messaggio seguente:

*Avviso: Impossibile trovare il valore denominato "MyStorageConnection" in local.settings.json che corrisponde alla proprietà "connessione" impostata su "blobTrigger" in "BlobTriggerCSharp\function.json". È possibile eseguire "func azure functionapp fetch-app-settings <functionAppName>" o specificare una stringa di connessione in local.settings.json.*

Il file local.settings.json viene usato solo da Strumenti di base di Funzioni di Azure. Per impostare le impostazioni app e le stringhe di connessione in Azure, usare il pannello **Impostazioni dell'applicazione**.

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Per impostare un valore per le stringhe di connessione, è possibile eseguire una delle operazioni seguenti:
- Immettere manualmente una stringa di connessione da [Azure Storage Explorer](http://storageexplorer.com/).
- Usare **func azure functionapp fetch-app-settings \<FunctionAppName\>**. Richiede un **account Azure**.
- Usare **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**. Richiede un **account Azure**.

## <a name="create-a-function"></a>Creare una funzione

Per creare una funzione, eseguire `func new`. Questo comando presenta i seguenti argomenti opzionali:

- `--language [-l]`. Il linguaggio di programmazione del modello, come C#, F# o JavaScript.
- `--template [-t]`. Il nome del modello.
- `--name [-n]`. Il nome della funzione.

Ad esempio, per creare un trigger HTTP JavaScript, eseguire:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Per creare una funzione attivata dalla coda, eseguire:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>Eseguire funzioni localmente

Per eseguire un progetto Funzioni, eseguire l'host di Funzioni. L'host abilita i trigger per tutte le funzioni del progetto:

```
func host start
```

È possibile usare le opzioni seguenti con `func host start`:

- `--port [-p]`. La porta locale su cui ascoltare. Valore predefinito: 7071.
- `--debug <type>`. Le opzioni sono VSCode e VS.
- `--cors`. Un elenco delimitato dalla virgola di origini CORS, senza spazi.
- `--nodeDebugPort [-n]`. La porta per il debugger di nodo da usare. Predefinito: un valore di launch.json o 5858.
- `--debugLevel [-d]`. Il livello di traccia della console (off, verbose, info, warning o error). Predefinito: Info.
- `--timeout [-t]`. Il timeout per l'host di Funzioni da avviare, in secondi. Impostazione predefinita: 20 secondi.
- `--useHttps`. Associare https://localhost:{port} anziché http://localhost:{port}. Per impostazione predefinita, questa opzione crea un certificato attendibile nel computer in uso.
- `--pause-on-error`. Sospendere per l'input aggiuntivo prima dell'uscita dal processo. Utile quando si avvia Strumenti di base di Funzioni di Azure da un ambiente di sviluppo integrato (IDE).

Quando viene avviato l'host di Funzioni, restituisce come output l'URL delle funzioni attivate da HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>Debug

Per associare un debugger, passare l'argomento `--debug`. Per eseguire il debug di funzioni JavaScript, usare Visual Studio Code. Per le funzioni C#, usare Visual Studio.

Per eseguire il debug di funzioni C#, usare `--debug vs`. In alternativa, usare [Strumenti di Visual Studio 2017 per Funzioni di Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Per avviare l'host e configurare il debug di JavaScript, eseguire:

```
func host start --debug vscode
```

Quindi, in Visual Studio Code, nella visualizzazione **Debug**, selezionare **Attach to Azure Functions** (Associa a Funzioni di Azure). È possibile associare punti di interruzione, controllare variabili ed eseguire il codice passo per passo.

![Debug di JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>Chiamare una funzione con dati di test

È inoltre possibile chiamare una funzione direttamente tramite `func run <FunctionName>`. Questo comando è simile alla scheda **Test** del portale di Azure, in cui è possibile fornire dati di input per la funzione. Questo comando avvia l'intero host di Funzioni.

È possibile usare le opzioni seguenti con `func run`:

- `--content [-c]`. Contenuto inline.
- `--debug [-d]`. Associare un debugger al processo host prima di eseguire la funzione.
- `--timeout [-t]`. Tempo di attesa (in secondi) fino a quando l'host locale di Funzioni è pronto.
- `--file [-f]`. Il nome del file da usare come contenuto.
- `--no-interactive`. Non richiede input. Utile per scenari di automazione.

Ad esempio, per chiamare una funzione attivata da HTTP e passare il corpo del contenuto, eseguire il comando seguente:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>Pubblicare un'app per le funzioni

Per pubblicare un progetto Funzioni in un'app per le funzioni in Azure, usare il comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

È possibile usare le opzioni seguenti:

- `--publish-local-settings [-i]`.  Pubblicare le impostazioni di local.settings.json in Azure, suggerendo di sovrascrivere eventuali impostazioni esistenti.
- `--overwrite-settings [-y]`. Deve essere usato con `-i`. Sovrascrive AppSettings in Azure con il valore locale se diverso. Viene suggerito il valore predefinito.

Il comando `publish` carica il contenuto della directory del progetto Funzioni. Se si eliminano i file localmente, questo comando non li elimina da Azure. Per eliminare questi file, nel portale di Funzioni di Azure, usare Kudu. Per avviare Kudu, nel portale di Funzioni di Azure, selezionare **Funzionalità della piattaforma** > **Strumenti avanzati (Kudu)**. 


<!-- LINKS -->

[Strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
