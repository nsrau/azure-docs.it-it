---
title: Usare Strumenti di base di Funzioni di Azure | Microsoft Docs
description: Informazioni su come scrivere codice per le funzioni di Azure dal prompt dei comandi o dal terminale e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 5c582b080ec6f2cff801758fc4bff4f7d07fd7df
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083070"
---
# <a name="work-with-azure-functions-core-tools"></a>Usare Strumenti di base di Funzioni di Azure

Strumenti di base di Funzioni di Azure consente di sviluppare e testare le funzioni nel computer locale dal prompt dei comandi o terminale. Le funzioni locali possono connettersi ai servizi di Azure attivi ed è possibile eseguire il debug delle funzioni nel computer locale usando il runtime completo di Funzioni di Azure. È anche possibile distribuire un'app per le funzioni all'abbonamento di Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Le versioni degli strumenti di base

Sono disponibili due versioni degli strumenti di base di Funzioni di Azure. La versione in uso dipende dall'ambiente di sviluppo locale, dalla scelta della lingua e dal livello di supporto richiesto:

+ [Versione 1.x](#v1): supporta la versione 1.x del runtime, che è disponibile a livello generale. Questa versione degli strumenti è supportata solo nei computer Windows e viene installata da un [pacchetto npm](https://docs.npmjs.com/getting-started/what-is-npm). Con questa versione, è possibile creare funzioni nei linguaggi sperimentali che non sono ufficialmente supportati. Per altre informazioni, vedere [Linguaggi supportati nelle Funzioni di Azure](supported-languages.md)

+ [Versione 2.x](#v2): supporta la versione 2.x del runtime. Questa versione supporta [Windows](#windows-npm), [macOS](#brew) e [Linux](#linux). Usa gestori di pacchetti specifici della piattaforma o npm per l'installazione. Come la versione 2.x del runtime, questa versione di strumenti di base è attualmente in anteprima.

Se non specificato diversamente, gli esempi in questo articolo si riferiscono alla versione 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Installare gli strumenti di base per Funzioni di Azure

[Strumenti di base di Funzioni di Azure] comprende una versione dello stesso runtime che alimenta Funzioni di Azure che è possibile eseguire nel computer di sviluppo locale. Fornisce anche i comandi per creare le funzioni, connettersi ad Azure e distribuire i progetti della funzione.

### <a name="v1"></a>Versione 1.x

La versione originale degli strumenti usa il runtime 1.x di Funzioni. Questa versione usa .NET Framework (4.7.1) ed è supportata solo nei computer Windows. Prima di installare la versione 1.x degli strumenti, è necessario [installare NodeJS](https://docs.npmjs.com/getting-started/installing-node) che include npm.

Usare il comando seguente per installare gli strumenti in versione 1.x:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Versione 2.x

>[!NOTE]
> Il runtime 2.0 di Funzioni di Azure è disponibile in anteprima e attualmente non tutte le funzionalità di Funzioni di Azure sono supportate. Per altre informazioni, vedere [Panoramica delle versioni del runtime per Funzioni di Azure](functions-versions.md). 

La versione 2.x degli strumenti usa il runtime di Funzioni di Azure 2.x basata su .NET Core. Questa versione è supportata su tutte le piattaforme supportate da .NET Core 2.x, incluse [Windows](#windows-npm), [macOS](#brew) e [Linux](#linux).

#### <a name="windows-npm"></a>Windows

I passaggi seguenti usano npm per installare gli strumenti di base in Windows. È anche possibile usare [Chocolatey](https://chocolatey.org/). Per altre informazioni, vedere il [file leggimi degli strumenti di base](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installare [.NET Core 2.0 per Windows](https://www.microsoft.com/net/download/windows).

2. Installare [Node.js], che include npm. Per la versione 2.x degli strumenti, sono supportate solo le versioni Node.js 8.5 e successive.

3. Installare il pacchetto degli strumenti di base:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>MacOS con Homebrew

I passaggi seguenti usano Homebrew per installare gli strumenti di base su macOS.

1. Installare [.NET Core 2.0 per macOS](https://www.microsoft.com/net/download/macos).

2. Installare [Homebrew](https://brew.sh/), se non è già installato.

3. Installare il pacchetto degli strumenti di base:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) con APT

La procedura seguente usa [APT](https://wiki.debian.org/Apt) per installare gli strumenti di base nella distribuzione Ubuntu/Debian Linux. Per altre distribuzioni Linux, vedere il [file leggimi degli strumenti di base](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installare [.NET Core 2.0 per Linux](https://www.microsoft.com/net/download/linux)

2. Registrare il codice Product Key di Microsoft come attendibile:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Verificare che il server Ubuntu esegua uno delle versioni appropriate nella tabella seguente. Per aggiungere l'origine apt, eseguire:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Distribuzione Linux | Version |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. Installare il pacchetto degli strumenti di base:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Creare un progetto Funzioni locale

Una directory del progetto funzioni contiene i file [host. JSON](functions-host-json.md) e [local.settings.json](#local-settings-file), nelle sottocartelle che contengono il codice per le singole funzioni. Questa directory è l'equivalente di un'app per le funzioni in Azure. Per altre informazioni sulla struttura delle cartelle di Funzioni, vedere la [Guida per sviluppatori di Funzioni di Azure](functions-reference.md#folder-structure).

La versione 2.x richiede la selezione di una lingua predefinita per il progetto quando esso viene inizializzato e tutte le funzioni aggiunte usano modelli di lingua predefiniti. Nella versione 1.x, specificare la lingua ogni volta che si crea una funzione.

Nella finestra del terminale o da un prompt dei comandi, eseguire il comando seguente per creare il progetto e l’archivio Git locale:

```bash
func init MyFunctionProj
```

Nella versione 2.x, quando si esegue il comando è necessario scegliere un runtime per il progetto. Se si prevede di sviluppare funzioni di JavaScript, scegliere **nodo**:

```output
Select a worker runtime:
dotnet
node
```

Usare le frecce su/giù per scegliere una lingua, quindi premere INVIO. L'output ha un aspetto simile all'esempio seguente per un progetto JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Per creare il progetto senza un archivio Git locale, utilizzare l’opzione `--no-source-control [-n]`.

## <a name="register-extensions"></a>Registrare le estensioni

Nella versione 2.x del runtime di Funzioni di Azure è necessario registrare in modo esplicito le estensioni delle associazioni(tipi di associazioni) usate nell'app per le funzioni.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Per altre informazioni, vedere [Concetti relativi a trigger e associazioni in Funzioni di Azure](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>File di impostazioni locali

Il file local.settings.json archivia le impostazioni di app, le stringhe di connessione e le impostazioni per Strumenti di base di Funzioni di Azure. Presenta la struttura seguente:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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

| Impostazione      | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Se impostato su **true**, tutti i valori sono crittografati tramite una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è **false**. |
| **Valori** | Raccolta di impostazioni dell'applicazione e stringhe di connessioni usate durante l'esecuzione in locale. Questi valori corrispondono alle impostazioni delle app nell'app in Azure, funzione, ad esempio **AzureWebJobsStorage** e **AzureWebJobsDashboard**. Molti trigger e associazioni includono una proprietà che fa riferimento a un'impostazione della stringa di connessione, ad esempio **Connection** per [Trigger di archiviazione del BLOB](functions-bindings-storage-blob.md#trigger---configuration). Per tali proprietà, è necessaria un'impostazione dell'applicazione definita nella matrice **Values**. <br/>**AzureWebJobsStorage** è un'impostazione di app obbligatoria per i trigger diversi da HTTP. Quando si dispone dell'[emulatore di archiviazione di Azure](../storage/common/storage-use-emulator.md) installato localmente, è possibile impostare **AzureWebJobsStorage** su `UseDevelopmentStorage=true` e gli strumenti di base usano l'emulatore. Ciò è utile durante lo sviluppo, ma è consigliabile testare con una connessione di archiviazione reale prima della distribuzione. |
| **Host** | Le impostazioni in questa sezione consentono di personalizzare il processo host di Funzioni durante l'esecuzione in locale. |
| **LocalHttpPort** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questo valore. |
| **CORS** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **ConnectionStrings** | Non usare questa raccolta per le stringhe di connessione usate per l'associazione di funzione. Questa raccolta viene usata solo dai Framework che devono ottenere le stringhe di connessione dalla sezione **ConnectionStrings** ad una configurazione del file, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Gli elementi in questa raccolta non vengono pubblicati in Azure con altre impostazioni di app. È necessario aggiungere in modo esplicito questi valori per la sezione **Stringhe di connessione** delle **Impostazioni dell'applicazione** per l'app di funzione. |

I valori delle impostazioni dell'app di funzione possono anche essere letti nel codice come variabili di ambiente. Per altre informazioni, vedere la sezione Variabili di ambiente negli argomenti di riferimento specifici del linguaggio seguenti:

+ [C# precompilato](functions-dotnet-class-library.md#environment-variables)
+ [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Le impostazioni nel file local.settings.json vengono usate solo per gli strumenti delle funzioni durante l'esecuzione in locale. Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure. Utilizzare lo switch `--publish-local-settings` [durante la pubblicazione](#publish) per assicurarsi che queste impostazioni vengano aggiunte all'app della funzione in Azure. I valori in **ConnectionStrings** non vengono mai pubblicati.

Quando non è impostata alcuna stringa di connessione di archiviazione valida per **AzureWebJobsStorage** e non viene usato l'emulatore, viene visualizzato il messaggio di errore seguente:  

>Valore mancante per AzureWebJobsStorage in local.settings.json. È necessario per tutti i trigger diversi da HTTP. È possibile eseguire "func azure functionapp fetch-app-settings <functionAppName>" o specificare una stringa di connessione in local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Ottenere le stringhe di connessione di archiviazione

Anche quando si usa l'emulatore di archiviazione per lo sviluppo, si può desiderare di eseguire il test con una connessione di archiviazione effettiva. Se si dispone che si è già [creato un account di archiviazione](../storage/common/storage-create-storage-account.md), è possibile ottenere una stringa di connessione di archiviazione valida in uno dei modi seguenti:

+ Nel [portale di Azure]. Passare all'account di archiviazione, selezionare **Chiavi di accesso** in **Impostazioni**, quindi copiare uno dei valori della **Stringa di connessione**.

  ![Copiare la stringa di connessione dal portale di Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Usare [Azure Storage Explorer](http://storageexplorer.com/) per collegarsi all'account di Azure. In **Explorer**, espandere la propria sottoscrizione, selezionare l'account di archiviazione e copiare la stringa di connessione primaria o secondaria. 

  ![Copiare la stringa di connessione da Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Usare strumenti di base per scaricare la stringa di connessione da Azure con uno dei seguenti comandi:

    + Scaricare tutte le impostazioni di un'app di funzione esistente:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Ottenere la stringa di connessione per uno specifico account di archiviazione:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Quando l'accesso ad Azure non è ancora stato eseguito, viene richiesto di farlo.

## <a name="create-func"></a>Creare una funzione

Eseguire il comando seguente per creare una funzione:

```bash
func new
```

Nella versione 2.x, quando si esegue `func new` viene richiesto di scegliere un modello nella lingua predefinita dell'app per le funzioni, quindi viene anche richiesto di scegliere un nome per la funzione. Nella versione 1.x, viene anche richiesto di scegliere la lingua.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Il codice della funzione viene generato in una sottocartella con il nome della funzione fornito, come è possibile vedere nell'output del trigger della coda seguente:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

È anche possibile specificare queste opzioni nel comando usando gli argomenti seguenti:

| Argomento     | DESCRIZIONE                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| Il linguaggio di programmazione del modello, come C#, F# o JavaScript. Questa opzione è necessaria nella versione 1.x. Nella versione 2.x, non usare questa opzione o scegliere la lingua predefinita del progetto. |
| **`--template -t`** | Il nome del modello, che può essere uno dei valori:<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | Il nome della funzione. |

Ad esempio, per creare un trigger HTTP JavaScript in un singolo comando, eseguire:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Per creare una funzione attivata dalla coda in un singolo comando, eseguire:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Eseguire funzioni localmente

Per eseguire un progetto Funzioni, eseguire l'host di Funzioni. L'host abilita i trigger per tutte le funzioni del progetto:

```bash
func host start
```

`func host start` supporta le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | La porta locale su cui ascoltare. Valore predefinito: 7071. |
| **`--debug <type>`** | Viene avviato l'host con la porta di debug aperta in modo che sia possibile connettersi al processo **func.exe** da [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) o [Visual Studio 2017](functions-dotnet-class-library.md). Le opzioni di *\<tipo\>* sono `VSCode` e `VS`.  |
| **`--cors`** | Un elenco delimitato dalla virgola di origini CORS, senza spazi. |
| **`--nodeDebugPort -n`** | La porta per il debugger di nodo da usare. Predefinito: un valore di launch.json o 5858. |
| **`--debugLevel -d`** | Il livello di traccia della console (off, verbose, info, warning o error). Predefinito: Info.|
| **`--timeout -t`** | Il timeout per l'host di Funzioni da avviare, in secondi. Impostazione predefinita: 20 secondi.|
| **`--useHttps`** | Eseguire l'associazione a `https://localhost:{port}` anziché a `http://localhost:{port}`. Per impostazione predefinita, questa opzione crea un certificato attendibile nel computer in uso.|
| **`--pause-on-error`** | Sospendere per l'input aggiuntivo prima dell'uscita dal processo. Usato quando si avvia strumenti di base in Visual Studio o Visual Studio Code.|

Quando viene avviato l'host di Funzioni, restituisce come output l'URL delle funzioni attivate da HTTP:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>Passaggio di dati di test a una funzione

Per testare le funzioni localmente, [avviare l'host di Funzioni](#start) e chiamare gli endpoint nel server locale usando richieste HTTP. L'endpoint chiamato dipende dal tipo di funzione.

>[!NOTE]  
> Gli esempi in questo argomento usano lo strumento cURL per inviare richieste HTTP dal terminale o da un prompt dei comandi. È possibile usare lo strumento preferito per inviare richieste HTTP al server locale. Lo strumento cURL è disponibile per impostazione predefinita nei sistemi basati su Linux. In Windows è necessario prima scaricare e installare lo [strumento cURL](https://curl.haxx.se/).

Per informazioni più generali sui test delle funzioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funzioni attivate tramite HTTP e webhook

È possibile chiamare l'endpoint seguente per eseguire localmente funzioni attivate tramite HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Assicurarsi di usare lo stesso nome server e la stessa porta su cui è in ascolto l'host di Funzioni. Questi valori sono visualizzati nell'output generato all'avvio dell'host di Funzioni. È possibile chiamare questo URL usando qualsiasi metodo HTTP supportato dal trigger. 

Il comando cURL seguente attiva la funzione di avvio rapido `MyHttpTrigger` da una richiesta GET con il parametro _name_ passato nella stringa di query. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
L'esempio seguente è la stessa funzione chiamata da una richiesta POST passando _name_ nel corpo della richiesta:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

È possibile effettuare richieste GET da un browser passando dati nella stringa di query. Per tutti gli altri metodi HTTP è necessario usare cURL, Fiddler, Postman o uno strumento analogo per i test HTTP.  

#### <a name="non-http-triggered-functions"></a>Funzione attivate non da HTTP

Per tutti i tipi di funzioni diverse dai trigger HTTP e dai webhook, è possibile testare localmente le funzioni chiamando un endpoint di amministrazione. Chiamando questo endpoint con una richiesta HTTP POST sul server locale si attiva la funzione. È facoltativamente possibile passare dati di test all'esecuzione nel corpo del messaggio della richiesta POST. Questa funzionalità è analoga alla scheda **Test** del portale di Azure.  

Chiamare l'endpoint di amministrazione seguente per attivare funzioni non HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Per passare dati di test all'endpoint di amministrazione di una funzione, è necessario fornire i dati nel corpo di un messaggio di richiesta POST. Il corpo del messaggio deve avere il formato JSON seguente:

```JSON
{
    "input": "<trigger_input>"
}
````

Il valore `<trigger_input>` contiene dati nel formato previsto dalla funzione. L'esempio cURL seguente è una richiesta POST per una funzione `QueueTriggerJS`. In questo caso l'input è una stringa che equivale al messaggio previsto nella coda.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Uso del comando `func run` nella versione 1.x

>[!IMPORTANT]  
> Il comando `func run` non è supportato nella versione 2.x degli strumenti. Per altre informazioni, vedere l'argomento [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

È anche possibile richiamare una funzione direttamente tramite `func run <FunctionName>` e offrire dati di input per la funzione. Questo comando è simile all'esecuzione di una funzione con la scheda **Test** nel portale di Azure. 

`func run` supporta le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenuto inline. |
| **`--debug -d`** | Associare un debugger al processo host prima di eseguire la funzione.|
| **`--timeout -t`** | Tempo di attesa (in secondi) fino a quando l'host locale di Funzioni è pronto.|
| **`--file -f`** | Il nome del file da usare come contenuto.|
| **`--no-interactive`** | Non richiede input. Utile per scenari di automazione.|

Ad esempio, per chiamare una funzione attivata da HTTP e passare il corpo del contenuto, eseguire il comando seguente:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Visualizzazione dei file di log in locale

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a> Pubblicazione in Azure

Per pubblicare un progetto Funzioni in un'app per le funzioni in Azure, usare il comando `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

È possibile usare le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Pubblicare le impostazioni di local.settings.json in Azure, suggerendo di sovrascrivere eventuali impostazioni esistenti. Se si usa l'emulatore di archiviazione, si modifica l'impostazione dell'app portandola a [connessione di archiviazione effettiva](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Deve essere usato con `-i`. Sovrascrive AppSettings in Azure con il valore locale se diverso. Viene suggerito il valore predefinito.|

Questo comando consente di pubblicare un'app per le funzioni esistente in Azure. Si verifica un errore se `<FunctionAppName>` non esiste nella propria sottoscrizione. Per informazioni su come creare un'app per le funzioni dal prompt dei comandi o dalla finestra del terminale usando l'interfaccia della riga di comando di Azure, vedere [Creare un'app per le funzioni per l'esecuzione senza server](./scripts/functions-cli-create-serverless.md).

Il comando `publish` carica il contenuto della directory del progetto Funzioni. Se si eliminano i file in locale, il comando `publish` non li elimina da Azure. È possibile eliminare i file in Azure usando lo [strumento Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) nel [portale di Azure].  

>[!IMPORTANT]  
> Quando si crea un'app per le funzioni in Azure, questa utilizza la versione 1.x del runtime di Funzioni per impostazione predefinita. Per fare in modo che l’app per le funzioni utilizzi la versione 2.x del runtime, aggiungere l'impostazione dell'applicazione `FUNCTIONS_EXTENSION_VERSION=beta`.  
Utilizzare il seguente codice dell’interfaccia della riga di comando di Azure per aggiungere questa impostazione all'app per le funzioni:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Passaggi successivi

Strumenti di base di Funzioni di Azure è [open source ed è ospitato su GitHub](https://github.com/azure/azure-functions-cli).  
Per registrare una richiesta per un bug o una funzionalità [aprire un problema di GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portale di Azure]: https://portal.azure.com 
[Node.JS]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
