---
title: Sviluppare ed eseguire localmente le funzioni di Azure | Documentazione Microsoft
description: Informazioni su come scrivere codice per le funzioni di Azure e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codice per le funzioni di Azure e testarle in locale

Sebbene il [portale di Azure] offra un set di strumenti completo per lo sviluppo e il test delle funzioni di Azure, molti sviluppatori preferiscono un'esperienza di sviluppo locale. Funzioni di Azure semplifica l'uso dell'editor di codice e degli strumenti di sviluppo locale preferiti per sviluppare e testare le funzioni nel computer locale. Le funzioni possono attivare gli eventi in Azure ed è possibile eseguire il debug delle funzioni JavaScript e C# nel computer locale. 

Se si sviluppatori di Visual Studio C#, Funzioni di Azure [si integra anche con Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Installare gli strumenti di base per Funzioni di Azure

Strumenti di base di Funzioni di Azure è una versione locale del runtime di Funzioni di Azure che è possibile eseguire nel computer Windows locale. Non è un emulatore o simulatore. Si tratta dello stesso runtime presente in Funzioni di Azure.

Gli [strumenti di base di Funzioni di Azure] vengono offerti come pacchetto npm. È innanzitutto necessario [installare NodeJS](https://docs.npmjs.com/getting-started/installing-node), che include npm.  

>[!NOTE]
>A questo punto, il pacchetto degli strumenti di base di Funzioni di Azure può essere installato solo nei computer Windows. Questa restrizione è dovuta a una limitazione temporanea nell'host di Funzioni.

[strumenti di base di Funzioni di Azure] aggiunge gli alias di comando seguenti:
* **func**
* **azfun**
* **azurefunctions**

Tutti questi alias possono essere usati al posto di `func` illustrato negli esempi di questo argomento.

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
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>", 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Impostazione      | Descrizione                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Se impostato su **true**, tutti i valori sono crittografati tramite una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è **false**. |
| **Valori** | Raccolta di impostazioni dell'applicazione usate durante l'esecuzione in locale. Aggiungere le impostazioni dell'applicazione a questo oggetto.  |
| **AzureWebJobsStorage** | Consente di impostare la stringa di connessione all'account di archiviazione di Azure usato internamente dal runtime di Funzioni di Azure. L'account di archiviazione supporta i trigger della funzione. Questa impostazione di connessione per l'account di archiviazione è obbligatorio per tutte le funzioni ad eccezione delle funzioni attivate da HTTP.  |
| **AzureWebJobsDashboard** | Consente di impostare la stringa di connessione all'account di archiviazione di Azure usato per archiviare i log della funzione. Questo valore facoltativo rende accessibili i log dal portale.|
| **Host** | Le impostazioni in questa sezione consentono di personalizzare il processo host di Funzioni durante l'esecuzione in locale. | 
| **LocalHttpPort** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questo valore. |
| **CORS** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (**\***) che consente le richieste provenienti da qualsiasi origine. |
| **ConnectionStrings** | Contiene le stringhe di connessione del database per le funzioni. Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di **System.Data.SqlClient**.  | 

La maggior parte dei trigger e delle associazioni presentano una proprietà **Connection** che mappa il nome di una variabile di ambiente o di un'impostazione dell'app. Per ogni proprietà di connessione, deve essere definita un'impostazione dell'app nel file local.settings.json. 

Queste impostazioni possono anche essere lette nel codice come variabili di ambiente. In C# usare [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) o [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). In JavaScript usare `process.env`. Le impostazioni specificate come variabile di ambiente di sistema hanno la precedenza sui valori nel file local.settings.json. 

Le impostazioni nel file local.settings.json vengono usate solo per gli strumenti delle funzioni durante l'esecuzione in locale. Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure. Utilizzare lo switch `--publish-local-settings` [durante la pubblicazione](#publish) per assicurarsi che queste impostazioni vengano aggiunte all'app della funzione in Azure.

Quando non è impostata alcuna stringa di connessione di archiviazione valida per **AzureWebJobsStorage**, viene visualizzato il messaggio di errore seguente:  

>Valore mancante per AzureWebJobsStorage in local.settings.json. È necessario per tutti i trigger diversi da HTTP. È possibile eseguire "func azure functionary fetch-app-settings <functionAppName>" o specificare una stringa di connessione in local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Per impostare un valore per le stringhe di connessione, è possibile eseguire una delle operazioni seguenti:
* Immettere la stringa di connessione da [Azure Storage Explorer](http://storageexplorer.com/).
* Usare uno di questi comandi:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    Per entrambi i comandi è necessario innanzitutto accedere ad Azure.

## <a name="create-a-function"></a>Creare una funzione

Eseguire il comando seguente per creare una funzione:

```
func new
``` 
`func new` supporta gli argomenti opzionali seguenti:

| Argomento     | Descrizione                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Il linguaggio di programmazione del modello, come C#, F# o JavaScript. |
| **`--template -t`** | Il nome del modello. |
| **`--name -n`** | Il nome della funzione. |

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

`func host start` supporta le opzioni seguenti:

| Opzione     | Descrizione                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | La porta locale su cui ascoltare. Valore predefinito: 7071. |
| **`--debug <type>`** | Le opzioni sono `VSCode` e `VS`. |
| **`--cors`** | Un elenco delimitato dalla virgola di origini CORS, senza spazi. |
| **`--nodeDebugPort -n`** | La porta per il debugger di nodo da usare. Predefinito: un valore di launch.json o 5858. |
| **`--debugLevel -d`** | Il livello di traccia della console (off, verbose, info, warning o error). Predefinito: Info.|
| **`--timeout -t`** | Il timeout per l'host di Funzioni t da avviare, in secondi. Impostazione predefinita: 20 secondi.|
| **`--useHttps`** | Associare https://localhost:{port} anziché http://localhost:{port}. Per impostazione predefinita, questa opzione crea un certificato attendibile nel computer in uso.|
| **`--pause-on-error`** | Sospendere per l'input aggiuntivo prima dell'uscita dal processo. Utile quando si avvia Strumenti di base di Funzioni di Azure da un ambiente di sviluppo integrato (IDE).|

Quando viene avviato l'host di Funzioni, restituisce come output l'URL delle funzioni attivate da HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Eseguire il debug in Visual Studio Code o Visual Studio

Per associare un debugger, passare l'argomento `--debug`. Per eseguire il debug di funzioni JavaScript, usare Visual Studio Code. Per le funzioni C#, usare Visual Studio.

Per eseguire il debug di funzioni C#, usare `--debug vs`. È anche possibile usare [Strumenti di Visual Studio 2017 per Funzioni di Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Per avviare l'host e configurare il debug di JavaScript, eseguire:

```
func host start --debug vscode
```

Quindi, in Visual Studio Code, nella visualizzazione **Debug**, selezionare **Attach to Azure Functions** (Associa a Funzioni di Azure). È possibile associare punti di interruzione, controllare variabili ed eseguire il codice passo per passo.

![Debug di JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Passaggio di dati di test a una funzione

È anche possibile richiamare una funzione direttamente tramite `func run <FunctionName>` e offrire dati di input per la funzione. Questo comando è simile all'esecuzione di una funzione con la scheda **Test** nel portale di Azure. Questo comando avvia l'intero host di Funzioni.

`func run` supporta le opzioni seguenti:

| Opzione     | Descrizione                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenuto inline. |
| **`--debug -d`** | Associare un debugger al processo host prima di eseguire la funzione.|
| **`--timeout -t`** | Tempo di attesa (in secondi) fino a quando l'host locale di Funzioni è pronto.|
| **`--file -f`** | Il nome del file da usare come contenuto.|
| **`--no-interactive`** | Non richiede input. Utile per scenari di automazione.|

Ad esempio, per chiamare una funzione attivata da HTTP e passare il corpo del contenuto, eseguire il comando seguente:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a> Pubblicazione in Azure

Per pubblicare un progetto Funzioni in un'app per le funzioni in Azure, usare il comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

È possibile usare le opzioni seguenti:

| Opzione     | Descrizione                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Pubblicare le impostazioni di local.settings.json in Azure, suggerendo di sovrascrivere eventuali impostazioni esistenti.|
| **`--overwrite-settings -y`** | Deve essere usato con `-i`. Sovrascrive AppSettings in Azure con il valore locale se diverso. Viene suggerito il valore predefinito.|

Il comando `publish` carica il contenuto della directory del progetto Funzioni. Se si eliminano i file in locale, il comando `publish` non li elimina da Azure. È possibile eliminare i file in Azure usando lo [strumento Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) nel [portale di Azure].

## <a name="next-steps"></a>Passaggi successivi

Strumenti di base di Funzioni di Azure è [open source ed è ospitato su GitHub](https://github.com/azure/azure-functions-cli).  
Per registrare una richiesta per un bug o una funzionalità [aprire un problema di GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[portale di Azure]: https://portal.azure.com 

