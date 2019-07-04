---
title: Sviluppare funzioni di Azure usando Visual Studio Code | Microsoft Docs
description: Informazioni su come sviluppare e testare funzioni di Azure usando l'estensione di funzioni di Azure per Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452696"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Sviluppare funzioni di Azure usando Visual Studio Code

Il [Estensione Funzioni di Azure per Visual Studio Code] consente di sviluppare e distribuire funzioni di Azure in locale. Se questa è la prima esperienza con Funzioni di Azure, è consigliabile leggere altre informazioni in [Introduzione a Funzioni di Azure](functions-overview.md).

L'estensione di funzioni di Azure offre i vantaggi seguenti: 

* Modifica, compilazione ed esecuzione di funzioni nel computer di sviluppo locale. 
* Pubblicazione del proprio progetto di Funzioni di Azure direttamente in Azure. 
* Scrittura delle funzioni in diversi linguaggi mantenendo tutti i vantaggi di Visual Studio Code. 

L'estensione può essere usata con le seguenti lingue supportate dal runtime di funzioni di Azure versione 2.x: 

* [C#compilato](functions-dotnet-class-library.md) 
* [C#copione](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>È necessario che si [impostare C# script come il linguaggio di progetto predefinito](#c-script-projects).

In questo articolo, gli esempi sono attualmente disponibili solo per JavaScript (Node. js) e C# classe di funzioni della libreria.  

Questo articolo fornisce informazioni dettagliate su come usare l'estensione di funzioni di Azure per sviluppare le funzioni e li pubblicano in Azure. Prima di leggere questo articolo, dovrebbe [creare la prima funzione usando Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Non combinare lo sviluppo locale con lo sviluppo del portale nella stessa app per le funzioni. Quando si pubblica da un progetto locale a un'app per le funzioni, il processo di distribuzione sovrascrive le funzioni sviluppate nel portale.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare ed eseguire la [estensione di funzioni di Azure][estensione funzioni di azure per visual studio code], è necessario soddisfare i requisiti seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) installato in uno dei [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Altre risorse necessarie, ad esempio un account di archiviazione di Azure, vengono create nella sottoscrizione quando si [esegue la pubblicazione mediante Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> È possibile sviluppare funzioni in locale e pubblicare in Azure senza dover avviare e li Esegui localmente. Esistono requisiti aggiuntivi per l'esecuzione in locale, le funzionalità tra cui un download automatico di Azure Functions Core Tools. Per altre informazioni, vedere [requisiti aggiuntivi per eseguire localmente](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

L'estensione funzioni consente di creare un progetto di app di funzione, con la prima funzione. I passaggi seguenti illustrano come creare una funzione attivata tramite HTTP in un nuovo progetto di funzioni. [Trigger HTTP](functions-bindings-http-webhook.md) è il modello di trigger di funzione più semplice per illustrare.

1. Da **Azure: Funzioni** scegliere l'icona Crea funzione.

    ![Creare una funzione](./media/functions-develop-vs-code/create-function.png)

1. Selezionare la cartella del progetto dell'app di funzione e quindi **selezionare una lingua per il progetto di funzione**. 

1. Selezionare il **trigger HTTP** modello di funzione, oppure è possibile scegliere di **Ignora per ora** per creare un progetto senza una funzione. È sempre possibile [aggiungere una funzione al progetto](#add-a-function-to-your-project) in un secondo momento. 

    ![Scegliere il modello Trigger HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Tipo di `HTTPTrigger` per il nome della funzione e premere INVIO, quindi selezionare **funzione** autorizzazione. Questo livello di autorizzazione richiede di specificare una [tasto funzione](functions-bindings-http-webhook.md#authorization-keys) quando si chiama l'endpoint della funzione.

    ![Scegliere l'autenticazione (funzione)](./media/functions-develop-vs-code/create-function-auth.png)

    Viene creata una funzione nel linguaggio prescelto usando il modello per una funzione attivata tramite HTTP.

    ![Modello di funzione attivata tramite HTTP in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Il modello di progetto crea un progetto nel linguaggio scelto, installa le dipendenze necessarie. Per qualsiasi linguaggio, il nuovo progetto contiene i file seguenti:

* **host.json**: consente di configurare l'host di Funzioni. Queste impostazioni si applicano sia durante l'esecuzione in locale che nell'esecuzione in Azure. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

* **local.settings.json**: mantiene le impostazioni usate quando si esegue Funzioni in locale. Queste impostazioni vengono usate solo durante l'esecuzione locale. Per altre informazioni, vedere [file di impostazioni locali](#local-settings-file).

    >[!IMPORTANT]
    >Poiché il file local.settings.json può contenere segreti, è necessario escluderlo dal controllo del codice sorgente del progetto.

A questo punto, è possibile aggiungere un input e le associazioni di output alla funzione dalla [modificando il file Function. JSON](#javascript-2), o tramite [aggiunta di un parametro a un C# classe funzione della libreria](#c-class-library-2).

È anche possibile [aggiungere una nuova funzione al progetto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installare le estensioni di binding

Fatta eccezione per i trigger di Timer e HTTP, le associazioni vengono implementate in pacchetti di estensione. È necessario installare i pacchetti di estensione per il trigger e associazioni che li richiedono. Il modo in cui installare le estensioni delle associazioni dipende dal linguaggio del progetto.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# libreria di classi

Eseguire la [dotnet aggiungere pacchetto](/dotnet/core/tools/dotnet-add-package) comando nella finestra del terminale per installare i pacchetti di estensione necessari nel progetto. L'esempio seguente installa l'estensione di archiviazione di Azure, che implementa le associazioni per l'archiviazione Blob, coda e tabella.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Aggiungere una funzione al progetto

È possibile aggiungere una nuova funzione a un progetto esistente utilizzando uno dei modelli di trigger di funzioni predefiniti. Per aggiungere un nuovo trigger di funzione, premere il tasto F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando **funzioni di Azure: Crea funzione...** . Seguire le istruzioni per scegliere il tipo di trigger e definire gli attributi obbligatori del trigger. Se il trigger richiede una stringa di connessione o la chiave di accesso per connettersi a un servizio, modo che sia pronta prima di creare il trigger di funzione. 

I risultati di questa operazione dipendono dal linguaggio del progetto:

### <a name="javascript"></a>JavaScript

Viene creata una nuova cartella nel progetto, che contiene un nuovo file Function. JSON e il nuovo file di codice JavaScript.

### <a name="c-class-library"></a>C\# libreria di classi

Un nuovo oggetto C# file (con estensione cs) libreria di classi viene aggiunto al progetto.

## <a name="add-input-and-output-bindings"></a>Aggiungere un input e le associazioni di output

È possibile espandere funzione aggiungendo associazioni di input e output. Il modo con cui si esegue questa operazione dipende dal linguaggio del progetto. Per altre informazioni sulle associazioni, vedere [funzioni di Azure concetti di trigger e associazioni](functions-triggers-bindings.md). 

Negli esempi seguenti la connessione a una coda di archiviazione denominata `outqueue`, in cui la stringa di connessione dell'account di archiviazione è impostata `MyStorageConnection` impostazione dell'applicazione in Local. 

### <a name="javascript"></a>JavaScript

Visual Studio Code è possibile aggiungere binding al file Function. JSON seguendo un utile set di prompt. Per creare un'associazione, pulsante destro del mouse (Ctrl + clic in macOS) il `function.json` del file nella cartella della funzione e scegliere **aggiungere un'associazione...** . 

![Aggiungere un binding a una funzione JavaScript esistente ](media/functions-develop-vs-code/function-add-binding.png)

Di seguito sono riportate istruzioni di esempio per definire una nuova associazione di output dell'archiviazione:

| Prompt | Value | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di associazione** | `out` | Il binding è un binding di output. |
| **Selezionare l'associazione con direzione...** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Il nome utilizzato per identificare questa associazione nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione "local.setting.json"** | `MyStorageConnection` | Il nome di un'impostazione dell'applicazione che contiene la stringa di connessione dell'account di archiviazione. L'impostazione `AzureWebJobsStorage` contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

In questo esempio, il binding seguente viene aggiunto per il `bindings` matrice nel file Function. JSON:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

È anche possibile aggiungere la stessa definizione di associazione direttamente per il file Function. JSON.

Nel codice della funzione, il `msg` associazione avviene dal `context`, come illustrato nell'esempio seguente:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Per altre informazioni, vedere la [associazione di output archiviazione code](functions-bindings-storage-queue.md#output---javascript-example) riferimento.

### <a name="c-class-library"></a>C\# libreria di classi

Aggiornare il metodo di funzione per aggiungere il parametro seguente al `Run` definizione del metodo:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Questo codice è necessario aggiungere il codice seguente `using` istruzione:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Il `msg` parametro è un `ICollector<T>` tipo che rappresenta una raccolta di messaggi che vengono scritti in un output di associazione quando la funzione viene completata. Aggiungere uno o più messaggi alla raccolta, che vengono inviati alla coda quando la funzione viene completata.

Per altre informazioni, vedere la [associazione di output archiviazione code](functions-bindings-storage-queue.md#output---c-example) riferimento.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Visual Studio Code consente di pubblicare il progetto di Funzioni direttamente in Azure. Durante il processo vengono create un'app per le funzioni e le risorse correlate nella sottoscrizione di Azure. L'app per le funzioni fornisce un contesto di esecuzione per le funzioni. Il progetto viene inserito in un pacchetto e distribuito nella nuova app per le funzioni nella sottoscrizione di Azure.

Durante la pubblicazione da Visual Studio Code, uno dei due metodi di distribuzione vengono usati:

* [Distribuisci con abilitato eseguire da pacchetto zip](functions-deployment-technologies.md#zip-deploy): usato per la maggior parte delle distribuzioni di funzioni di Azure.
* [URL del pacchetto esterno](functions-deployment-technologies.md#external-package-url): usato per la distribuzione per le app Linux in un [piano a consumo](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Creazione di app rapido (funzione)

Per impostazione predefinita, Visual Studio Code genera automaticamente valori per le risorse di Azure necessarie per l'app per le funzioni. Questi valori sono basati sul nome dell'applicazione di funzione che scelto. Per un esempio di uso dei valori predefiniti per la pubblicazione del progetto in una nuova app per le funzioni in Azure, vedere la [articolo della Guida rapida di Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se si desidera fornire nomi espliciti per le risorse create, è necessario abilitare la pubblicazione utilizzando le opzioni avanzate.

### <a name="enabled-publishing-with-advanced-create-options"></a>Opzioni di creazione di pubblicazione abilitata con avanzate

Per poter controllare le impostazioni con funzioni di Azure creando App associate, aggiornare l'estensione di funzioni di Azure per abilitare le impostazioni avanzate.

1. Fare clic su **File > Preferenze > Impostazioni**

1. Spostarsi tra **impostazioni utente > estensioni > funzioni di Azure**

1. Selezionare la casella di controllo per **funzioni di Azure: Creazione avanzata**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Pubblicare in una nuova app per le funzioni in Azure con la creazione avanzata

I passaggi seguenti pubblicano il progetto in una nuova app per le funzioni creata utilizzando avanzate opzioni di creazione.

1. Nell'area **Azure: Funzioni** selezionare l'icona per la distribuzione nell'app per le funzioni.

    ![Impostazioni dell'applicazione (funzione)](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se non è stato ancora eseguito l'accesso, viene visualizzata la richiesta per **accedere ad Azure**. È anche possibile **creare un account di Azure gratuito**. Dopo l'accesso dal browser, tornare a Visual Studio Code.

1. Se si hanno più sottoscrizioni, **selezionare una sottoscrizione** per l'app per le funzioni e quindi scegliere l'opzione **+ Create New Function App in Azure** (+ Crea nuova app per le funzioni in Azure).

1. Seguendo le istruzioni, fornire le informazioni seguenti:

    | Prompt | Value | Descrizione |
    | ------ | ----- | ----------- |
    | Selezionare app per le funzioni in Azure | + Crea nuova App per le funzioni in Azure | Nella richiesta successiva, digitare un nome globalmente univoco che identifica la nuova app di funzione e premere INVIO. I caratteri validi per un nome di app per le funzioni sono `a-z`, `0-9` e `-`. |
    | Selezionare un sistema operativo | Windows | App per le funzioni viene eseguita in Windows |
    | Selezionare un piano di hosting | Piano a consumo | Senza server [hosting piano a consumo](functions-scale.md#consumption-plan) viene usato. |
    | Selezionare un runtime per la nuova app | Il linguaggio del progetto | Il runtime deve corrispondere il progetto che si desidera pubblicare. |
    | Selezionare un gruppo di risorse per le nuove risorse | Crea nuovo gruppo di risorse | Nella richiesta successiva, digitare un nome di gruppo di risorse, ad esempio `myResourceGroup`, e premere INVIO. È anche possibile scegliere un gruppo di risorse. |
    | Selezionare un account di archiviazione | Creare un nuovo account di archiviazione | Nella richiesta successiva, digitare un nome univoco globale del nuovo account di archiviazione usati dalle app per le funzioni e premere INVIO. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile scegliere un account esistente. |
    | Selezionare un percorso per le nuove risorse | region | Scegliere una località in un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create.

## <a name="republish-project-files"></a>Ripubblicare i file di progetto

Quando configuri [distribuzione continua](functions-continuous-deployment.md), app per le funzioni in Azure viene aggiornato ogni volta che vengono aggiornati i file di origine nella posizione di origine connessa. Sebbene sia consigliabile questa pratica di sviluppo, è possibile ripubblicare anche gli aggiornamenti di file di progetto da Visual Studio Code. 

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.

1. In Visual Studio Code, premere F1 per aprire il riquadro comandi. Nel riquadro comandi, cercare e selezionare `Azure Functions: Deploy to function app...`.

1. Se non è stato ancora eseguito l'accesso, viene visualizzata la richiesta per **accedere ad Azure**. Dopo l'accesso dal browser, tornare a Visual Studio Code. Se si hanno più sottoscrizioni, **selezionare una sottoscrizione** che contiene l'app per le funzioni.

1. Scegliere l'app per le funzioni esistente in Azure. Quando l'avviso relativo alla sovrascrittura di tutti i file nelle app per le funzioni, scegliere **Distribuisci** per accettare il messaggio di avviso e continuare. 

Il progetto viene ricompilato, riorganizzato e caricato in Azure. Il progetto esistente viene sostituito dal nuovo pacchetto e riavvia l'app per le funzioni.

## <a name="get-deployed-function-url"></a>Recupera URL della funzione distribuito

Per poter chiamare una funzione attivata tramite HTTP, è necessario l'URL della funzione quando viene distribuita in app per le funzioni. Questo URL include le operazioni [tasti funzione](functions-bindings-http-webhook.md#authorization-keys). È possibile usare l'estensione per ottenere questi URL per le funzioni distribuite.

1. Premere il tasto F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando **funzioni di Azure: Copiare l'URL della funzione**.

1. Seguire le istruzioni per scegliere l'app per le funzioni in Azure e quindi il trigger HTTP specifico da richiamare. 

La funzione URL viene copiato negli Appunti, insieme a eventuali chiavi necessarie passato usando il `code` parametro di query. Usare uno strumento HTTP per inviare richieste POST o un browser per le richieste GET per la funzione remota.  

## <a name="run-functions-locally"></a>Eseguire funzioni localmente

L'estensione di funzioni di Azure ti permette di eseguire un progetto di funzioni nel computer di sviluppo locale. Il runtime database locale è lo stesso runtime che ospita l'app per le funzioni in Azure. Le impostazioni locali vengono letti dal [Local file](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Requisiti aggiuntivi per l'esecuzione in locale

Per essere in grado di eseguire il progetto di funzioni in locale, è necessario soddisfare anche questi requisiti aggiuntivi:

* Installare la versione 2.x di [Azure Functions Core Tools](functions-run-local.md#v2). Il pacchetto di strumenti di base viene scaricato e installato per l'automaticamente quando si avvia il progetto in locale. Gli strumenti di base include l'intero runtime di funzioni di Azure, quindi download e l'installazione potrebbe richiedere alcuni minuti.

* Installare i requisiti specifici per il linguaggio scelto:

    | Linguaggio | Requisito |
    | -------- | --------- |
    | **C#** | [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Strumenti CLI di .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debugger per l'estensione di Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node. js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6 +](https://www.python.org/downloads/)|

    <sup>*</sup>LTS e manutenzione LTS le versioni attive (8.11.1 e 10.14.1 consigliato).

### <a name="configure-the-project-to-run-locally"></a>Configurare il progetto da eseguire in locale

Il runtime di funzioni usa un account di archiviazione di Azure internamente per tutti i tipi di trigger diversi da HTTP e webhook. Ciò significa che è necessario impostare il **Values. azurewebjobsstorage** chiave su una stringa di connessione account di archiviazione di Azure valida.

Questa sezione Usa la [estensione di archiviazione di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) con [Microsoft Azure Storage Explorer](https://storageexplorer.com/) per connettersi e recuperare la stringa di connessione di archiviazione.   

Per impostare la stringa di connessione dell'account di archiviazione:

1. In Visual Studio aprire **Cloud Explorer**, espandere **Account di archiviazione** > **Your Storage Account** (Account di archiviazione personale), quindi selezionare **Proprietà** e copiare il valore **Stringa di connessione primaria**.

2. Nel progetto aprire il file local.settings.json e impostare il valore della chiave **AzureWebJobsStorage** sulla stringa di connessione copiata.

3. Ripetere il passaggio precedente per aggiungere chiavi univoche alla matrice di **Valori** per tutte le altre connessioni richieste dalle funzioni.

Per altre informazioni, vedere [file di impostazioni locali](#local-settings-file).

### <a name="debugging-functions-locally"></a>Debug di funzioni in locale  

Per eseguire il debug delle funzioni, premere F5. Se sono stati già scaricati il [strumenti di base][strumenti di base di funzioni di azure], viene chiesto di farlo. Quando viene installato Strumenti di base e in esecuzione, è illustrato un output nel terminale. Questo è lo stesso che `func host start` comandi di strumenti di base dal terminale, ma con ulteriori generare attività e un debugger collegato.  

Con il progetto in esecuzione, è possibile attivare le funzioni come si farebbe quando distribuito in Azure. Durante l'esecuzione in modalità di debug, i punti di interruzione vengono raggiunti in Visual Studio Code, come previsto.

L'URL della richiesta per i trigger HTTP viene visualizzato nell'output nel terminale. Le chiavi di funzione per i trigger HTTP non vengono utilizzate durante l'esecuzione locale. Per altre informazioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md).  

Per altre informazioni, vedere [lavorare con Azure Functions Core Tools][strumenti di base di funzioni di azure].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure. Al termine della pubblicazione, disponibile l'opzione per pubblicare le impostazioni da Local app per le funzioni di Azure. Per altre informazioni, vedere [pubblicare le impostazioni dell'applicazione](#publish-application-settings).

I valori in **ConnectionStrings** non vengono mai pubblicati.

I valori delle impostazioni dell'applicazione di funzione possono essere letti anche nel codice come variabili di ambiente. Per altre informazioni, vedere la sezione di variabili di ambiente di questi articoli di riferimento specifici della lingua:

* [C# precompilato](functions-dotnet-class-library.md#environment-variables)
* [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Impostazioni dell'applicazione in Azure

Le impostazioni nel file local nel progetto devono essere lo stesso come impostazioni dell'applicazione in app per le funzioni in Azure. Tutte le impostazioni che si aggiunge al Local devono essere aggiunte anche per le app per le funzioni in Azure. Queste impostazioni non vengono caricate automaticamente quando si pubblica il progetto. Analogamente, tutte le impostazioni che si crea in app per le funzioni [nel portale di](functions-how-to-use-azure-function-app-settings.md#settings) deve essere scaricato nel progetto locale.

### <a name="publish-application-settings"></a>Pubblicare le impostazioni dell'applicazione

Il modo più semplice per pubblicare le impostazioni necessarie per l'app per le funzioni in Azure è usare il **impostazioni di caricamento** collegamento che viene visualizzato dopo aver pubblicato il progetto.

![Distribuzione di completare le impostazioni dell'applicazione di caricamento](./media/functions-develop-vs-code/upload-app-settings.png)

È anche possibile pubblicare le impostazioni utilizzando il `Azure Functions: Upload Local Setting` comando nel riquadro comandi. Le singole impostazioni vengono aggiunte alle impostazioni dell'applicazione in Azure utilizzando il `Azure Functions: Add New Setting...` comando. 

> [!TIP]
> Assicurarsi di salvare il file local prima della pubblicazione.

Se il file locale è crittografato, viene decrittografato, pubblicato e crittografato nuovamente. In presenza di impostazioni con valori diversi in entrambe le posizioni, viene chiesto di scegliere come procedere.

Visualizzare le impostazioni dell'app esistente nel **Azure: Le funzioni** area, espandere la sottoscrizione, l'app per le funzioni, e **impostazioni applicazione**.

![Impostazione di app di funzione di visualizzazione in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Il download delle impostazioni di Azure

Se sono state create impostazioni dell'applicazione in Azure, è possibile scaricarli nel file local. tramite il `Azure Functions: Download Remote Settings...` comando. 

Come con il caricamento, se il file locale è crittografato, viene decrittografato, aggiornato e crittografato nuovamente. In presenza di impostazioni con valori diversi in entrambe le posizioni, viene chiesto di scegliere come procedere.

## <a name="monitoring-functions"></a>Monitoraggio delle funzioni

Quando si [vengono eseguite localmente](#run-functions-locally), viene inviato nel flusso di dati di log nella console terminale. È anche possibile ottenere i dati di log quando il progetto funzioni è in esecuzione in un'app per le funzioni in Azure. È uno connettersi allo streaming dei log in Azure per visualizzare i dati di log quasi in tempo reale, oppure è possibile abilitare Application Insights per una miglior comprensione del comportamento di app per le funzioni.

### <a name="streaming-logs"></a>Streaming dei log

Durante lo sviluppo di un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. È possibile visualizzare un flusso di file di log generati dalle funzioni. L'output seguente è una funzione attivata da un esempio di streaming dei log per una richiesta a HTTP:

![Streaming dei log di output per il trigger HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Per altre informazioni, vedere [log in Streaming](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> I log in streaming supportano una sola istanza dell'host di funzioni. Quando la funzione viene ridimensionata a più istanze, i dati da altre istanze non vengono visualizzati nel flusso di log. Il [Live Stream metriche](../azure-monitor/app/live-stream.md) in Application Insights supportate più istanze. Sebbene anche in quasi in tempo reale, analitica streaming è inoltre basati sulla [dati campionati](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

È consigliabile monitorare l'esecuzione delle funzioni tramite l'integrazione dell'app per le funzioni con Azure Application Insights. Quando si crea un'app per le funzioni nel portale di Azure, questa integrazione avviene automaticamente per impostazione predefinita. Tuttavia, quando si crea l'app per le funzioni durante la pubblicazione di Visual Studio, l'integrazione nell'app per le funzioni in Azure non viene eseguita.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# progetti script

Per impostazione predefinita, tutti i C# progetti vengono creati come [ C# compilati progetti libreria di classi](functions-dotnet-class-library.md). Se invece si preferisce lavorare con C# progetti di script, è necessario selezionare C# dello script come lingua predefinita nelle impostazioni dell'estensione di funzioni di Azure.

1. Fare clic su **File > Preferenze > Impostazioni**.

1. Spostarsi **delle impostazioni utente > estensioni > funzioni di Azure**.

1. Scegli **C #Script** da **funzioni di Azure: Linguaggio del progetto**.

A questo punto, le chiamate effettuate per gli strumenti di base sottostante includano il `--csx` opzione, che genera e pubblica C# (con estensione csx) i file di progetto di script. Con una lingua predefinita specificata, tutti creati per impostazione predefinita i progetti a C# progetti script. Non viene chiesto di scegliere un linguaggio di progetto quando viene impostato un valore predefinito. Per creare altri progetti della lingua, è necessario modificare questa impostazione o rimuoverlo dal file utente Settings. JSON. Dopo la rimozione di questa impostazione, anche in questo caso viene chiesto di scegliere la lingua quando si crea un progetto.

## <a name="command-palette-reference"></a>Riferimento ai comandi tavolozza

L'estensione di funzioni di Azure fornisce un'interfaccia grafica utile nell'area di Azure per l'interazione con l'App per le funzioni in Azure. La stessa funzionalità è disponibile anche come i comandi nel riquadro comandi (F1). Sono disponibili i comandi di Azure funzioni specifiche seguenti:

|Comando di funzioni di Azure  | Descrizione  |
|---------|---------|
|**Aggiungere le nuove impostazioni...**  |  Crea una nuova impostazione dell'applicazione in Azure. Per altre informazioni, vedere [pubblicare le impostazioni dell'applicazione](#publish-application-settings). Potrebbe anche essere necessario [download di questa impostazione per le impostazioni locali](#download-settings-from-azure). |
| **Configura origine distribuzione...** | Connettere l'app di funzione in Azure a un repository Git locale. Per altre informazioni, vedere [distribuzione continua per funzioni di Azure](functions-continuous-deployment.md). |
| **Connetti al Repository GitHub...** | Connettere l'app di funzione un repository GitHub. |
| **Copiare l'URL della funzione** | Ottiene l'URL remoto di un HTTP attivato funzione in esecuzione in Azure. Per altre informazioni, vedere come [recupera l'URL della funzione distribuito](#get-deployed-function-url). |
| **Creare app per le funzioni in Azure...** | Crea una nuova app per le funzioni nella sottoscrizione di Azure. Per altre informazioni, vedere come [pubblicare in una nuova app per le funzioni in Azure](#publish-to-azure).        |
| **Decrittografare le impostazioni** | Usare per decrittografare [le impostazioni locali](#local-settings-file) che sono stati crittografati mediante `Azure Functions: Encrypt Settings`.  |
| **Elimina App per le funzioni...** | Rimuove un'app per le funzioni esistente dalla sottoscrizione in Azure. Quando non sono presenti altre app nel piano di servizio App, si riceve l'opzione per eliminare tale troppo. Altre risorse, ad esempio gli account di archiviazione e i gruppi di risorse non vengono eliminati. Per rimuovere tutte le risorse, è opportuno [eliminare il gruppo di risorse](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Progetto locale non è interessato. |
|**Eliminare la funzione...**  | Rimuove una funzione esistente da un'app per le funzioni in Azure. Poiché l'eliminazione non influenza del progetto locale, ma provare a rimuovere la funzione in locale e quindi [ripubblicazione progetto](#republish-project-files). |
| **Elimina Proxy...** | Rimuove un proxy di funzioni di Azure da app per le funzioni in Azure. Per altre informazioni sui proxy, vedere [lavorare con il proxy di funzioni di Azure](functions-proxies.md). |
| **Elimina impostazione...** | Elimina un'impostazione dell'applicazione di funzione in Azure. Non influisce sulle impostazioni nel file local. |
| **Disconnettersi dal repository...**  | Rimuovere il [distribuzione continua](functions-continuous-deployment.md) connessione tra un'app per le funzioni in Azure e un repository di controllo del codice sorgente. |
| **Scaricare le impostazioni Remote...** | Scarica le impostazioni dall'app per le funzioni scelto in Azure nel file local. Se il file locale è crittografato, viene decrittografato, aggiornato e crittografato nuovamente. In presenza di impostazioni con valori diversi in entrambe le posizioni, viene chiesto di scegliere come procedere. Assicurarsi di aver salvato le modifiche apportate al file local prima di eseguire questo comando. |
| **Modificare le impostazioni...** | Modifica il valore di un'impostazione dell'applicazione di funzione esistenti in Azure. Non influisce sulle impostazioni nel file local.  |
| **Crittografare le impostazioni** | Crittografa i singoli elementi all'interno di `Values` della matrice nel [le impostazioni locali](#local-settings-file). In questo file `IsEncrypted` viene impostata su `true`, che indica al runtime locale di decrittografare le impostazioni prima di usarli. Crittografare le impostazioni locali per ridurre il rischio della perdita di informazioni utili. In Azure, applicazione delle impostazioni vengono sempre archiviate crittografati. |
| **Eseguire ora (funzione)** | Avvia una [funzione attivata da timer](functions-bindings-timer.md) in Azure manualmente per scopi di test. Per altre informazioni sull'attivazione non HTTP funzioni in Azure, vedere [eseguire manualmente una funzione non attivate da HTTP](functions-manually-run-non-http.md). |
| **Inizializzare progetto per l'uso con Visual Studio Code...** | Aggiunge i file di progetto di Visual Studio Code necessari a un progetto funzioni esistente. Usare questo comando per lavorare con un progetto che è stato creato mediante strumenti di base. |
| **L'installazione dell'aggiornamento Azure Functions Core Tools** | Installa o aggiorna il [Strumenti di base di Funzioni di Azure] che consentono di eseguire in locale. |
| **Ridistribuire**  | È possibile ridistribuire i file di progetto da un repository Git collegato a una distribuzione specifica in Azure. Per ripubblicare aggiornamenti locali da Visual Studio Code [ripubblicare il progetto](#republish-project-files). |
| **Rinomina le impostazioni...** | Modifica il nome della chiave di un'impostazione di applicazione di funzione esistente in Azure. Non influisce sulle impostazioni nel file local. Dopo la rinomina di impostazioni in Azure, dovrebbe [tali modifiche scaricate al progetto locale](#download-settings-from-azure). |
| **Riavvio** | Riavvia l'app per le funzioni in Azure. Inoltre, la distribuzione degli aggiornamenti riavvia l'app per le funzioni. |
| **Imposta AzureWebJobStorage...**| Imposta il valore della `AzureWebJobStorage` impostazione dell'applicazione. Questa impostazione è necessaria per le funzioni di Azure e viene impostata quando viene creato l'app per le funzioni in Azure. |
| **Inizia** | Avvia un'app per le funzioni arrestato in Azure. | 
| **Avviare lo Streaming dei log** | Avvia i log di flusso per l'app per le funzioni in Azure. Usare i log in streaming durante la risoluzione remota dei problemi in Azure se si desidera visualizzare queste informazioni in tempo quasi reale. Per altre informazioni, vedere [log in Streaming](#streaming-logs). |
| **Stop** | Si arresta a discesa un'app per le funzioni in esecuzione in Azure. |
| **Arrestare lo Streaming dei log** | Arresta i log di flusso per l'app per le funzioni in Azure. |
| **Attiva/Disattiva come impostazione Slot** | Quando abilitata, assicura che un'impostazione dell'applicazione viene mantenuta per uno slot di distribuzione specificato. |
| **Disinstallare Azure Functions Core Tools** | Rimuove il Azure Functions Core Tools, che è necessario per l'estensione. |
| **Carica le impostazioni locali...** | Carica le impostazioni nel file local per app per le funzioni scelto in Azure. Se il file locale è crittografato, viene decrittografato, caricato e crittografato nuovamente. In presenza di impostazioni con valori diversi in entrambe le posizioni, viene chiesto di scegliere come procedere. Assicurarsi di aver salvato le modifiche apportate al file local prima di eseguire questo comando. |
| **Visualizza Commit in GitHub** | Illustra l'ultimo commit in una distribuzione specifica quando l'app per le funzioni è connesso a un repository. |
| **Visualizza registri di distribuzione** | Mostra i log per una distribuzione specifica per app per le funzioni in Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'utilizzo degli strumenti di base di Funzioni di Azure, vedere [Come scrivere codice per le funzioni di Azure e testarle in locale](functions-run-local.md).

Per altre informazioni sullo sviluppo di funzioni come librerie di classi .NET, vedere [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md). Questo articolo include anche collegamenti a esempi d'uso degli attributi per dichiarare i vari tipi di associazioni supportati da Funzioni di Azure.    

[Estensione Funzioni di Azure per Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Strumenti di base di Funzioni di Azure]: functions-run-local.md