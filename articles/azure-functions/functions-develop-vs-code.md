---
title: Sviluppare funzioni di Azure usando il codice di Visual StudioDevelop Azure Functions by using Visual Studio Code
description: Informazioni su come sviluppare e testare Funzioni di Azure usando l'estensione Funzioni di Azure per il codice di Visual Studio.Learn how to develop and test Azure Functions by using the Azure Functions extension for Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277167"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Sviluppare funzioni di Azure usando il codice di Visual StudioDevelop Azure Functions by using Visual Studio Code

L'estensione Funzioni di [Azure per Visual Studio Code] consente di sviluppare localmente funzioni e distribuirle in Azure.The Azure Functions extension for Visual Studio Code lets you locally develop functions and deploy them to Azure. Se questa è la prima esperienza con Funzioni di Azure, è consigliabile leggere altre informazioni in [Introduzione a Funzioni di Azure](functions-overview.md).

L'estensione Funzioni di Azure offre i vantaggi seguenti:The Azure Functions extension provides these benefits:

* Modifica, compilazione ed esecuzione di funzioni nel computer di sviluppo locale.
* Pubblicazione del proprio progetto di Funzioni di Azure direttamente in Azure.
* Scrivere le funzioni in vari linguaggi sfruttando i vantaggi del codice di Visual Studio.

L'estensione può essere usata con i linguaggi seguenti, supportati dal runtime di Funzioni di Azure a partire dalla versione 2.x:The extension can be used with the following languages, which are supported by the Azure Functions runtime starting with version 2.x:

* [Compilato in C](functions-dotnet-class-library.md)
* [Script di C](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>È necessario [impostare lo script di C' come linguaggio predefinito del progetto.](#c-script-projects)

In questo articolo, gli esempi sono attualmente disponibili solo per le funzioni JavaScript (Node.js) e la libreria di classi C.  

Questo articolo illustra in dettaglio come usare l'estensione Funzioni di Azure per sviluppare funzioni e pubblicarle in Azure.This article provides details about how to use the Azure Functions extension to develop functions and publish them to Azure. Prima di leggere questo articolo, è necessario [creare la prima funzione utilizzando Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Non combinare lo sviluppo locale e lo sviluppo del portale per una singola app per le funzioni. Quando si pubblica da un progetto locale a un'app per le funzioni, il processo di distribuzione sovrascrive le funzioni sviluppate nel portale.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare ed eseguire [l'estensione Funzioni]di Azure per il codice di Visual Studio , è necessario soddisfare i requisiti seguenti:Before you install and run the Azure Functions extension Azure Functions extension extension for Visual Studio[Code], you must meet these requirements:

* [Codice](https://code.visualstudio.com/) di Visual Studio installato in una delle [piattaforme supportate.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Altre risorse necessarie, ad esempio un account di archiviazione di Azure, vengono create nella sottoscrizione quando si [pubblica tramite Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> È possibile sviluppare funzioni in locale e pubblicarle in Azure senza doverle avviare ed eseguirle in locale. To run your functions locally, you'll need to meet some additional requirements, including an automatic download of Azure Functions Core Tools. Per ulteriori informazioni, vedere [Requisiti aggiuntivi per l'esecuzione](#additional-requirements-for-running-a-project-locally)di un progetto in locale.

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

L'estensione Funzioni consente di creare un progetto di app per le funzioni, insieme alla prima funzione. I passaggi seguenti illustrano come creare una funzione attivata da HTTP in un nuovo progetto di funzioni. [Il trigger HTTP](functions-bindings-http-webhook.md) è il modello di trigger di funzione più semplice da illustrare.

1. Da Azure: Funzioni , selezionare l'icona **Crea funzione:From** **Azure: Functions,** select the Create Function icon:

    ![Creare una funzione](./media/functions-develop-vs-code/create-function.png)

1. Selezionare la cartella per il progetto dell'app per le funzioni, quindi **selezionare una lingua per il progetto di funzione.**

1. Se non sono già stati installati gli strumenti di base, viene chiesto di **selezionare una versione** degli strumenti di base da installare. Scegliere la versione 2.x o una versione successiva. 

1. Selezionare il modello di funzione **trigger HTTP** oppure selezionare Ignora per **il momento** per creare un progetto senza una funzione. È sempre possibile [aggiungere una funzione al progetto](#add-a-function-to-your-project) in un secondo momento.

    ![Scegliere il modello Trigger HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digitare **HttpExample** come nome della funzione e selezionare INVIO, quindi selezionare **Autorizzazione funzione.** Questo livello di autorizzazione richiede di fornire un [tasto funzione](functions-bindings-http-webhook-trigger.md#authorization-keys) quando si chiama l'endpoint della funzione.

    ![Selezionare Autorizzazione funzione](./media/functions-develop-vs-code/create-function-auth.png)

    Viene creata una funzione nella lingua scelta e nel modello per una funzione attivata da HTTP.

    ![Modello di funzione attivato da HTTP nel codice di Visual StudioHTTP-triggered function template in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>File di progetto generati

Il modello di progetto crea un progetto nella lingua scelta e installa le dipendenze richieste. Per qualsiasi lingua, il nuovo progetto ha questi file:

* **host.json**: consente di configurare l'host di Funzioni. Queste impostazioni si applicano quando si eseguono funzioni in locale e quando vengono eseguite in Azure.These settings apply when you're running functions locally and when you're running them in Azure. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

* **local.settings.json**: Mantiene le impostazioni utilizzate durante l'esecuzione delle funzioni in locale. Queste impostazioni vengono utilizzate solo quando si eseguono funzioni in locale. Per ulteriori informazioni, consultate [File di impostazioni locali.](#local-settings-file)

    >[!IMPORTANT]
    >Poiché il file local.settings.json può contenere segreti, è necessario escluderlo dal controllo del codice sorgente del progetto.

A seconda della lingua, vengono creati gli altri file seguenti:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs file della libreria](functions-dotnet-class-library.md#functions-class-library-project) di classi che implementa la funzione.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Un file package.json nella cartella radice.

* Una cartella HttpExample che contiene il [file di definizione function.json](functions-reference-node.md#folder-structure) e il [file index.js](functions-reference-node.md#exporting-a-function), un file Node.js che contiene il codice della funzione.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

A questo punto, è possibile aggiungere associazioni di input e output alla funzione [modificando il file function.json](#add-a-function-to-your-project) o [aggiungendo un parametro a una funzione della libreria di](#add-a-function-to-your-project)classi C .

È inoltre possibile [aggiungere una nuova funzione al progetto.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Installare le estensioni di binding

Ad eccezione dei trigger HTTP e timer, le associazioni vengono implementate nei pacchetti di estensione. È necessario installare i pacchetti di estensione per i trigger e le associazioni che ne hanno bisogno. Il processo di installazione delle estensioni di associazione dipende dal linguaggio del progetto.

# <a name="c"></a>[C\#](#tab/csharp)

Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) nella finestra Terminale per installare i pacchetti di estensione necessari nel progetto. Il comando seguente installa l'estensione di Archiviazione di Azure, che implementa le associazioni per l'archiviazione BLOB, coda e tabelle.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Aggiungere una funzione al progetto

È possibile aggiungere una nuova funzione a un progetto esistente utilizzando uno dei modelli di trigger di funzioni predefinite. Per aggiungere un nuovo trigger di funzione, selezionare F1 per aprire la tavolozza dei comandi, quindi cercare ed eseguire il comando **Funzioni di Azure: Crea funzione**. Seguire le istruzioni visualizzate per scegliere il tipo di trigger e definire gli attributi obbligatori del trigger. Se il trigger richiede una chiave di accesso o una stringa di connessione per connettersi a un servizio, prepararlo prima di creare il trigger di funzione.

I risultati di questa azione dipendono dalla lingua del progetto:

# <a name="c"></a>[C\#](#tab/csharp)

Al progetto viene aggiunto un nuovo file di libreria di classi C.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Nel progetto viene creata una nuova cartella. La cartella contiene un nuovo file function.json e il nuovo file di codice JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Aggiungere associazioni di input e outputAdd input and output bindings

È possibile espandere la funzione aggiungendo associazioni di input e output. Il processo di aggiunta di associazioni dipende dal linguaggio del progetto. Per altre informazioni sulle associazioni, vedere Concetti relativi ai trigger e alle associazioni di Funzioni di Azure.To learn more about bindings, see [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

Gli esempi seguenti si connettono a una coda di archiviazione denominata , in cui la stringa di connessione per l'account di archiviazione viene impostata nell'impostazione dell'applicazione in local.settings.json.The following examples connect to a storage queue named `outqueue`, where the connection string for the storage account is set in the `MyStorageConnection` application setting in local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Aggiornare il metodo della funzione `Run` per aggiungere il parametro seguente alla definizione del metodo:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Questo codice richiede di `using` aggiungere l'istruzione seguente:This code requires you to add the following statement:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. Aggiungere uno o più messaggi alla raccolta. Questi messaggi vengono inviati alla coda al completamento della funzione.

Per altre informazioni, vedere la documentazione relativa [all'associazione all'output di archiviazione delle](functions-bindings-storage-queue-output.md) code.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Visual Studio Code consente di aggiungere associazioni al file function.json seguendo un pratico set di prompt. Per creare un'associazione, fare clic con il pulsante destro del mouse (CTRL e fare clic su macOS) sul file **function.json** nella cartella delle funzioni e selezionare **Aggiungi associazione**:

![Aggiungere un'associazione a una funzione JavaScript esistenteAdd a binding to an existing JavaScript function ](media/functions-develop-vs-code/function-add-binding.png)

Di seguito sono riportati i prompt di esempio per definire una nuova associazione di output di archiviazione:Following are example prompts to define a new storage output binding:

| Prompt | valore | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Selezionare l'associazione con la direzione** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Il nome usato per identificare questo binding nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione da "local.settings.json"** | `MyStorageConnection` | Nome di un'impostazione dell'applicazione che contiene la stringa di connessione per l'account di archiviazione. L'impostazione `AzureWebJobsStorage` contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

In questo esempio, la seguente `bindings` associazione viene aggiunta alla matrice nel file function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

È anche possibile aggiungere la stessa definizione di associazione direttamente a function.json.You can also add the same binding definition directly to your function.json.

Nel codice della `msg` funzione, l'associazione è accessibile da `context`, come in questo esempio:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Per altre informazioni, vedere informazioni di riferimento sull'associazione di [output di archiviazione della coda.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Visual Studio Code lets you publish your Functions project directly to Azure. Durante il processo vengono create un'app per le funzioni e le risorse correlate nella sottoscrizione di Azure. L'app per le funzioni fornisce un contesto di esecuzione per le funzioni. Il progetto viene inserito in un pacchetto e distribuito nella nuova app per le funzioni nella sottoscrizione di Azure.

Quando si pubblica da Visual Studio Code in una nuova app per le funzioni in Azure, viene offerto sia un percorso di creazione dell'app con funzioni rapide che un percorso avanzato. 

Quando si pubblica da Visual Studio Code, si sfrutta la tecnologia di [distribuzione zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Creazione rapida di app

Quando si sceglie Crea nuova app per le **funzioni in Azure...**, l'estensione genera automaticamente i valori per le risorse di Azure necessarie per l'app per le funzioni. Questi valori sono basati sul nome dell'app per le funzioni scelto. Per un esempio di utilizzo delle impostazioni predefinite per pubblicare il progetto in una nuova app per le funzioni in Azure, vedere l'articolo guida introduttiva codice di Visual Studio.For an example of using defaults to publish your project to a new function app in Azure, see the [Visual Studio Code quickstart article](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se si desidera fornire nomi espliciti per le risorse create, è necessario scegliere il percorso di creazione avanzato.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Pubblicare un progetto in una nuova app per le funzioni in Azure usando le opzioni avanzatePublish a project to a new function app in Azure by using advanced options

I passaggi seguenti pubblicano il progetto in una nuova app per le funzioni creata con opzioni di creazione avanzate:The following steps publish your project to a new function app created with advanced create options:

1. Nell'area **Azure: Funzioni** selezionare l'icona **Distribuisci in app per** le funzioni.

    ![Impostazioni dell'app per le funzioni](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se non è stato effettuato l'accesso, viene richiesto di **accedere ad Azure**. È anche possibile **creare un account Azure gratuito.** Dopo aver effettuato l'accesso dal browser, tornare a Visual Studio Code.After signing in from the browser, go torna a Visual Studio Code.

1. Se si dispone di più sottoscrizioni, **selezionare una sottoscrizione** per l'app per le funzioni e quindi selezionare Crea nuova app per le funzioni **in Azure... _Avanzate_**. Questa opzione _avanzata_ offre un maggiore controllo sulle risorse create in Azure.This Advanced option gives you more control over the resources you create in Azure. 

1. Seguendo i prompt, fornire queste informazioni:

    | Prompt | valore | Descrizione |
    | ------ | ----- | ----------- |
    | Selezionare l'app per le funzioni in AzureSelect function app in Azure | Creare una nuova app per le funzioni in AzureCreate New Function App in Azure | Al prompt successivo, digitare un nome univoco globale che identifichi la nuova app per le funzioni e quindi selezionare INVIO. I caratteri validi per un nome di app per le funzioni sono `a-z`, `0-9` e `-`. |
    | Selezionare un sistema operativo | WINDOWS | L'app per le funzioni viene eseguita in Windows. |
    | Selezionare un piano di hosting | Piano a consumo | Viene utilizzato un host del [piano di consumo](functions-scale.md#consumption-plan) senza server. |
    | Selezionare un runtime per la nuova app | Il linguaggio del progetto | Il runtime deve corrispondere al progetto che si sta pubblicando. |
    | Selezionare un gruppo di risorse per le nuove risorse | Crea nuovo gruppo di risorse | Al prompt successivo digitare il nome `myResourceGroup`di un gruppo di risorse, ad esempio , quindi selezionare INVIO. È anche possibile selezionare un gruppo di risorse esistente. |
    | Selezionare un account di archiviazioneSelect a storage account | Creare un nuovo account di archiviazione | Al prompt successivo digitare un nome univoco globale per il nuovo account di archiviazione usato dall'app per le funzioni e quindi selezionare INVIO. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È inoltre possibile selezionare un account esistente. |
    | Selezionare una località per le nuove risorse | region | Selezionare una posizione in [un'area](https://azure.microsoft.com/regions/) vicina a te o vicino ad altri servizi a cui accedono le funzioni. |

    Dopo la creazione dell'app per le funzioni e l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create.

## <a name="republish-project-files"></a>Ripubblicare i file di progetto

Quando si configura la [distribuzione continua,](functions-continuous-deployment.md)l'app per le funzioni in Azure viene aggiornata ogni volta che i file di origine vengono aggiornati nel percorso di origine connesso. È consigliabile la distribuzione continua, ma è anche possibile ripubblicare gli aggiornamenti dei file di progetto da Visual Studio Code.We recommend continuous deployment, but you can also republish your project file updates from Visual Studio Code.

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.

1. In Visual Studio Code selezionare F1 per aprire la tavolozza dei comandi. Nella tavolozza dei comandi cercare e selezionare Funzioni di **Azure: Distribuisci in un'app per**le funzioni .

1. Se non è stato effettuato l'accesso, viene richiesto di **accedere ad Azure**. Dopo aver eseguito l'accesso dal browser, tornare a Visual Studio Code.After you sign in from the browser, go torna a Visual Studio Code. Se si dispone di più sottoscrizioni, **selezionare una sottoscrizione** che contiene l'app per le funzioni.

1. Selezionare l'app per le funzioni esistente in Azure.Select your existing function app in Azure. Quando viene visualizzato un avviso relativo alla sovrascrittura di tutti i file nell'app per le funzioni, selezionare **Distribuisci** per confermare l'avviso e continuare.

Il progetto viene ricompilato, riconfezionato e caricato in Azure.The project is rebuilt, repackaged, and uploaded to Azure. Il progetto esistente viene sostituito dal nuovo pacchetto e l'app per le funzioni viene riavviata.

## <a name="get-the-url-of-the-deployed-function"></a>Ottenere l'URL della funzione distribuitaGet the URL of the deployed function

Per chiamare una funzione attivata da HTTP, è necessario l'URL della funzione quando viene distribuita nell'app per le funzioni. Questo URL include tutti i [tasti funzione](functions-bindings-http-webhook-trigger.md#authorization-keys)necessari. È possibile utilizzare l'estensione per ottenere questi URL per le funzioni distribuite.

1. Selezionare F1 per aprire la tavolozza dei comandi, quindi cercare ed eseguire il comando **Funzioni di Azure: Copia URL funzione**.

1. Seguire le istruzioni visualizzate per selezionare l'app per le funzioni in Azure e quindi il trigger HTTP specifico che si vuole richiamare.

L'URL della funzione viene copiato negli Appunti, `code` insieme alle chiavi necessarie passate dal parametro di query. Utilizzare uno strumento HTTP per inviare richieste POST o un browser per le richieste GET alla funzione remota.  

## <a name="run-functions-locally"></a>Eseguire funzioni localmente

L'estensione Funzioni di Azure consente di eseguire un progetto Funzioni nel computer di sviluppo locale. The local runtime is the same runtime that hosts your function app in Azure. Le impostazioni locali vengono lette dal [file local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Requisiti aggiuntivi per l'esecuzione di un progetto in locale

Per eseguire il progetto Functions in locale, è necessario soddisfare i requisiti aggiuntivi seguenti:To run your Functions project locally, you must meet these additional requirements:

* Installare la versione 2.x o successiva di [Azure Functions Core Tools](functions-run-local.md#v2). Il pacchetto Strumenti di base viene scaricato e installato automaticamente quando si avvia il progetto in locale. Strumenti di base include l'intero runtime di Funzioni di Azure, pertanto il download e l'installazione potrebbero richiedere del tempo.

* Installare i requisiti specifici per il linguaggio scelto:

    | Linguaggio | Requisito |
    | -------- | --------- |
    | **C #** | [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Strumenti dell'interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debugger per l'estensione JavaDebugger for Java extension](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 o versioni successive](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Estensione di Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) consigliato|

    <sup>*</sup>Versioni LTS attive e LTS di manutenzione (8.11.1 e 10.14.1 consigliate).

### <a name="configure-the-project-to-run-locally"></a>Configurare il progetto per l'esecuzione in locale

Il runtime di Funzioni usa internamente un account di Archiviazione di Azure per tutti i tipi di trigger diversi da HTTP e webhook. È quindi necessario impostare la chiave Values.AzureWebJobsStorage su una stringa di connessione valida dell'account di Archiviazione di Azure.So you need to set the **Values.AzureWebJobsStorage** key to a valid Azure Storage account connection string.

Questa sezione usa [l'estensione Archiviazione di Azure per il codice](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) di Visual Studio con Azure Storage [Explorer](https://storageexplorer.com/) per connettersi e recuperare la stringa di connessione di archiviazione.

Per impostare la stringa di connessione dell'account di archiviazione:

1. In Visual Studio aprire **Cloud Explorer**, espandere **Account** > **di archiviazione dell'account di archiviazione**, quindi selezionare **Proprietà** e copiare il valore Stringa di **connessione primaria.**

2. Nel progetto aprire il file local.settings.json e impostare il valore della chiave **AzureWebJobsStorage** sulla stringa di connessione copiata.

3. Ripetere il passaggio precedente per aggiungere chiavi univoche alla matrice di **Valori** per tutte le altre connessioni richieste dalle funzioni.

Per ulteriori informazioni, consultate [File di impostazioni locali.](#local-settings-file)

### <a name="debugging-functions-locally"></a>Debug delle funzioni in locale  

Per eseguire il debug delle funzioni, selezionare F5. Se non sono già stati scaricati gli strumenti di base di [Microsoft Tools]di[Azure Functions,]viene richiesto di farlo. Quando Core Tools è installato e in esecuzione, l'output viene visualizzato nel terminale. Equivale all'esecuzione del `func host start` comando Strumenti di base dal terminale, ma con attività di compilazione aggiuntive e un debugger collegato.  

When the project is running, you can trigger your functions as you would when the project is deployed to Azure. Quando il progetto è in esecuzione in modalità di debug, i punti di interruzione vengono raggiunti nel codice di Visual Studio, come previsto.

L'URL della richiesta per i trigger HTTP viene visualizzato nell'output nel terminale. I tasti funzione per i trigger HTTP non vengono utilizzati quando un progetto è in esecuzione in locale. Per altre informazioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md).  

Per altre informazioni, vedere Usare gli strumenti di base di [Azure Functions,]strumenti di base di[Azure.]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure.By default, these settings aren't migrated automatically when the project is published to Azure. Al termine della pubblicazione, è possibile pubblicare le impostazioni da local.settings.json all'app per le funzioni in Azure.After publishing finishes, you're a get the option of publishing settings from local.settings.json to your function app in Azure. Per ulteriori informazioni, consultate [Pubblicare le impostazioni dell'applicazione.](#publish-application-settings)

I valori in **ConnectionStrings** non vengono mai pubblicati.

I valori delle impostazioni dell'applicazione della funzione possono anche essere letti nel codice come variabili di ambiente. Per altre informazioni, vedere le sezioni Variabili di ambiente di questi articoli di riferimento specifici del linguaggio:For more information, see the Environment variables sections of these language-specific reference articles:

* [C# precompilato](functions-dotnet-class-library.md#environment-variables)
* [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Impostazioni dell'applicazione in AzureApplication settings in Azure

The settings in the local.settings.json file in your project should be the same as the application settings in the function app in Azure. Tutte le impostazioni aggiunte a local.settings.json devono essere aggiunte anche all'app per le funzioni in Azure.Any settings you add to local.settings.json must also be added to the function app in Azure. Queste impostazioni non vengono caricate automaticamente quando si pubblica il progetto. Analogamente, tutte le impostazioni create nell'app per le funzioni [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) devono essere scaricate nel progetto locale.

### <a name="publish-application-settings"></a>Pubblicare le impostazioni dell'applicazione

Il modo più semplice per pubblicare le impostazioni necessarie nell'app per le funzioni in Azure consiste nell'usare il collegamento Carica impostazioni visualizzato dopo la pubblicazione del progetto:The easiest way to publish the required settings to your function app in Azure is to use the **Upload settings** link that appears after you publish your project:

![Caricare le impostazioni dell'applicazione](./media/functions-develop-vs-code/upload-app-settings.png)

È anche possibile pubblicare le impostazioni usando il comando **Funzioni di Azure: Carica impostazioni locali** nella tavolozza dei comandi. È possibile aggiungere singole impostazioni alle impostazioni dell'applicazione in Azure usando il comando **Funzioni di Azure: Aggiungi nuova impostazione.**

> [!TIP]
> Assicurarsi di salvare il file local.settings.json prima di pubblicarlo.

Se il file locale è crittografato, viene decrittografato, pubblicato e crittografato nuovamente. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene richiesto di scegliere come procedere.

Visualizzare le impostazioni dell'app esistenti nell'area **Azure: Funzioni** espandendo la sottoscrizione, l'app per le funzioni e **le impostazioni dell'applicazione.**

![Visualizzare le impostazioni dell'app per le funzioni nel codice di Visual StudioView function app settings in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Scaricare le impostazioni da AzureDownload settings from Azure

Se sono state create impostazioni dell'applicazione in Azure, è possibile scaricarle nel file local.settings.json usando il comando Funzioni di **Azure: Scarica impostazioni remote.**

Come per il caricamento, se il file locale è crittografato, viene decrittografato, aggiornato e crittografato di nuovo. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene richiesto di scegliere come procedere.

## <a name="monitoring-functions"></a>Monitoraggio delle funzioni

Quando si [eseguono funzioni in locale,](#run-functions-locally)i dati di log vengono trasmessi alla console Terminale. È anche possibile ottenere i dati di log quando il progetto Funzioni è in esecuzione in un'app per le funzioni in Azure.You can also get log data when your Functions project is running in a function app in Azure. È possibile connettersi ai log di streaming in Azure per visualizzare dati di log quasi in tempo reale oppure abilitare Application Insights per una comprensione più completa del funzionamento dell'app per le funzioni.

### <a name="streaming-logs"></a>Streaming dei log

Quando si sviluppa un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. È possibile visualizzare un flusso di file di log generati dalle funzioni. Questo output è un esempio di log di flusso per una richiesta a una funzione attivata da HTTP:This output is an example of streaming logs for a request to an HTTP-triggered function:

![Output dei log di streaming per il trigger HTTPStreaming logs output for HTTP trigger](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Per ulteriori informazioni, vedere [Log di streaming](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> I log di streaming supportano una sola istanza dell'host Funzioni. Quando la funzione viene ridimensionata a più istanze, i dati di altre istanze non vengono visualizzati nel flusso di log. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights supporta più istanze. Anche se in tempo quasi reale, l'analisi dello streaming si basa su [dati campionati.](functions-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

È consigliabile monitorare l'esecuzione delle funzioni integrando l'app per le funzioni con Application Insights. Quando si crea un'app per le funzioni nel portale di Azure, questa integrazione si verifica per impostazione predefinita. Quando si crea l'app per le funzioni durante la pubblicazione di Visual Studio, è necessario integrare Application Insights manualmente.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>Progetti\# di script C

Per impostazione predefinita, tutti i progetti in C, vengono creati come progetti di libreria di [classi compilati in C.](functions-dotnet-class-library.md) Se invece si preferisce usare i progetti di script di C, è necessario selezionare lo script di C'è come linguaggio predefinito nelle impostazioni dell'estensione Funzioni di Azure:If you prefer to work with C's script projects instead, you must select C's script as the default language in the Azure Functions extension settings:

1. Selezionare**Impostazioni****preferenze** >  **file** > .

1. Passare a Funzioni**di Azure****per le estensioni** >  **utente** > .

1. Selezionare **Script c'è** da **Funzione di Azure: linguaggio del progetto**.

Dopo aver completato questi passaggi, le chiamate `--csx` effettuate agli strumenti di base sottostanti includono l'opzione, che genera e pubblica i file di progetto di script di C. Dopo aver specificato questo linguaggio predefinito, tutti i progetti creati per impostazione predefinita per i progetti di script di C. Non viene richiesto di scegliere una lingua del progetto quando viene impostata una lingua predefinita. Per creare progetti in altre lingue, è necessario modificare questa impostazione o rimuoverla dal file settings.json dell'utente. Dopo aver rimosso questa impostazione, viene nuovamente richiesto di scegliere la lingua quando si crea un progetto.

## <a name="command-palette-reference"></a>Informazioni di riferimento sulla tavolozza dei comandi

The Azure Functions extension provides a useful graphical interface in the area for interacting with your function apps in Azure. La stessa funzionalità è disponibile anche come comandi nella tavolozza dei comandi (F1). Sono disponibili i seguenti comandi di Funzioni di Azure:These Azure Functions commands are available:

|Comando Funzioni di AzureAzure Functions command  | Descrizione  |
|---------|---------|
|**Aggiungi nuove impostazioni**  |  Crea una nuova impostazione dell'applicazione in Azure.Creates a new application setting in Azure. Per ulteriori informazioni, consultate [Pubblicare le impostazioni dell'applicazione.](#publish-application-settings) Potrebbe anche essere necessario [scaricare questa impostazione per le impostazioni locali](#download-settings-from-azure). |
| **Configurare l'origine di distribuzione** | Connette l'app per le funzioni in Azure a un repository Git locale. Per altre informazioni, vedere Distribuzione continua per Funzioni di [Azure.To](functions-continuous-deployment.md)learn more, see Continuous deployment for Azure Functions . |
| **Connettersi al repository GitHubConnect to GitHub Repository** | Connette l'app per le funzioni a un repository GitHub. |
| **Copia URL funzione** | Ottiene l'URL remoto di una funzione attivata da HTTP in esecuzione in Azure. Per ulteriori informazioni, vedere [Ottenere l'URL della funzione distribuita.](#get-the-url-of-the-deployed-function) |
| **Creare un'app per le funzioni in AzureCreate function app in Azure** | Crea una nuova app per le funzioni nella sottoscrizione in Azure.Creates a new function app in your subscription in Azure. Per altre informazioni, vedere la sezione su come pubblicare in una nuova app per le funzioni [in Azure.](#publish-to-azure)        |
| **Decrittografa impostazioni** | Decrittografa [le impostazioni locali](#local-settings-file) crittografate da Funzioni di **Azure: Crittografa impostazioni**.  |
| **Elimina App Per le Funzioni** | Rimuove un'app per le funzioni dalla sottoscrizione in Azure.Removes a function app from your subscription in Azure. Quando non sono presenti altre app nel piano di servizio app, è possibile eliminare anche questa opzione. Altre risorse, ad esempio gli account di archiviazione e i gruppi di risorse, non vengono eliminate. Per rimuovere tutte le risorse, è invece necessario [eliminare il gruppo di risorse](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Il progetto locale non è interessato. |
|**Funzione Delete**  | Rimuove una funzione esistente da un'app per le funzioni in Azure.Removes an existing function from a function app in Azure. Poiché questa eliminazione non influisce sul progetto locale, è invece consigliabile rimuovere la funzione localmente e quindi [ripubblicare il progetto.](#republish-project-files) |
| **Elimina proxy** | Rimuove un proxy Funzioni di Azure dall'app per le funzioni in Azure. Per altre informazioni sui proxy, vedere Usare i proxy di Funzioni di Azure.To learn more about proxyies, see [Work with Azure Functions Proxies](functions-proxies.md). |
| **Elimina impostazione** | Elimina l'impostazione di un'app per le funzioni in Azure.Deletes a function app setting in Azure. Questa eliminazione non influisce sulle impostazioni nel file local.settings.json. |
| **Disconnetti da Repo**  | Rimuove la connessione di [distribuzione continua](functions-continuous-deployment.md) tra un'app per le funzioni in Azure e un repository del controllo del codice sorgente. |
| **Scarica impostazioni remote** | Scarica le impostazioni dall'app per le funzioni scelta in Azure nel file local.settings.json. Se il file locale è crittografato, viene decrittografato, aggiornato e crittografato nuovamente. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene richiesto di scegliere come procedere. Assicurarsi di salvare le modifiche apportate al file local.settings.json prima di eseguire questo comando. |
| **Modificare le impostazioni** | Modifica il valore di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel file local.settings.json.  |
| **Crittografare le impostazioni** | Crittografa i singoli `Values` elementi nell'array nelle [impostazioni locali.](#local-settings-file) In questo `IsEncrypted` file è `true`inoltre impostato su , che specifica che il runtime locale decriterà le impostazioni prima di utilizzarle. Crittografare le impostazioni locali per ridurre il rischio di perdita di informazioni preziose. In Azure le impostazioni dell'applicazione vengono sempre archiviate crittografate. |
| **Esegui funzione ora** | Avvia manualmente una funzione attivata dal timer in Azure.Manually starts a [timer-triggered function](functions-bindings-timer.md) in Azure. Questo comando viene utilizzato per il test. Per altre informazioni sull'attivazione di funzioni non HTTP in Azure, vedere Eseguire manualmente una funzione non attivata da [HTTP.](functions-manually-run-non-http.md) |
| **Inizializzare il progetto per l'utilizzo con il codice VSInitialize Project for Use with VS Code** | Aggiunge i file di progetto di codice di Visual Studio necessari a un progetto Functions esistente. Utilizzare questo comando per lavorare con un progetto creato utilizzando gli strumenti di base. |
| **Installare o aggiornare gli strumenti di base di Funzioni di AzureInstall or Update Azure Functions Core Tools** | Installa o aggiorna gli strumenti di base di Funzioni di [Azure,]usati per eseguire le funzioni in locale. |
| **Ripetere la distribuzione**  | Consente di ridistribuire i file di progetto da un repository Git connesso a una distribuzione specifica in Azure.Lets you redeploy project files from a connected Git repository to a specific deployment in Azure. Per ripubblicare gli aggiornamenti locali dal codice di Visual Studio, [ripubblicare il progetto](#republish-project-files). |
| **Rinomina impostazioni** | Modifica il nome della chiave di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel file local.settings.json. Dopo aver rinominato le impostazioni in Azure, è necessario [scaricare le modifiche nel progetto locale.](#download-settings-from-azure) |
| **Riavvia** | Riavvia l'app per le funzioni in Azure.Restarts the function app in Azure. La distribuzione degli aggiornamenti riavvia anche l'app per le funzioni. |
| **Impostare AzureWebJobsStorageSet AzureWebJobsStorage**| Imposta il valore `AzureWebJobsStorage` dell'impostazione dell'applicazione. Questa impostazione è richiesta da Funzioni di Azure.This setting is required by Azure Functions. Viene impostato quando viene creata un'app per le funzioni in Azure.It's set when a function app is created in Azure. |
| **Inizia** | Avvia un'app per le funzioni interrotta in Azure.Starts a stopped function app in Azure. |
| **Avvia log di streaming** | Avvia i log di streaming per l'app per le funzioni in Azure. Usare i log di streaming durante la risoluzione dei problemi remota in Azure se è necessario visualizzare le informazioni di registrazione in tempo quasi reale. Per ulteriori informazioni, vedere [Log di streaming](#streaming-logs). |
| **Arresta** | Arresta un'app per le funzioni in esecuzione in Azure.Stops a function app that's running in Azure. |
| **Interrompi log di streaming** | Arresta i log di streaming per l'app per le funzioni in Azure. |
| **Attiva/disattiva impostazione slot** | Se abilitata, assicura che un'impostazione dell'applicazione venga mantenuta per un determinato slot di distribuzione. |
| **Disinstallare gli strumenti di base di Funzioni di AzureUninstall Azure Functions Core Tools** | Rimuove gli strumenti di base di Funzioni di Azure, richiesti dall'estensione. |
| **Carica impostazioni locali** | Carica le impostazioni dal file local.settings.json nell'app per le funzioni scelta in Azure. Se il file locale è crittografato, viene decrittografato, caricato e crittografato nuovamente. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene richiesto di scegliere come procedere. Assicurarsi di salvare le modifiche apportate al file local.settings.json prima di eseguire questo comando. |
| **Visualizzare il commit in GitHubView Commit in GitHub** | Viene visualizzato il commit più recente in una distribuzione specifica quando l'app per le funzioni è connessa a un repository. |
| **Visualizzare i registri di distribuzione** | Vengono visualizzati i log per una distribuzione specifica dell'app per le funzioni in Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli strumenti di base di Azure Functions, vedere Usare gli strumenti di base di Funzioni di Azure.To learn more about Azure Functions Core Tools, see [Work with Azure Functions Core Tools](functions-run-local.md).

Per altre informazioni sullo sviluppo di funzioni come librerie di classi .NET, vedere [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md). Questo articolo fornisce anche collegamenti a esempi su come usare gli attributi per dichiarare i vari tipi di associazioni supportati da Funzioni di Azure.This article also provides links to examples of how to use attributes to declare the various types of bindings supported by Azure Functions.

[Estensione Funzioni di Azure per Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Strumenti di base di Funzioni di AzureAzure Functions Core Tools]: functions-run-local.md
