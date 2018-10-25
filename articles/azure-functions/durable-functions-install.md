---
title: Installare l'estensione Funzioni permanenti e i relativi esempi - Azure
description: Informazioni su come installare l'estensione Funzioni permanenti per Funzioni di Azure, per lo sviluppo nel portale o in Visual Studio.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: 2eb838bcb9d3f64d0bbf4657c516adb50d103223
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585306"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installare l'estensione Funzioni permanenti e i relativi esempi (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) di Funzioni di Azure è inclusa nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Questo articolo illustra come installare il pacchetto e un set di esempi per gli ambienti di sviluppo seguenti:

* Visual Studio 2017 (scelta consigliata per C#) 
* Visual Studio Code (scelta consigliata per JavaScript)
* Portale di Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio offre al momento l'esperienza ottimale per lo sviluppo di applicazioni che usano Funzioni permanenti.  Le funzioni possono essere eseguite in locale ed essere pubblicate anche in Azure. È possibile iniziare con un progetto vuoto o con un set di funzioni di esempio.

### <a name="prerequisites"></a>Prerequisiti

* Installare la versione più recente di [Visual Studio](https://www.visualstudio.com/downloads/) (versione 15.6 o superiore). Includere il carico di lavoro di **sviluppo di Azure** nelle opzioni di installazione.

### <a name="start-with-sample-functions"></a>Iniziare con funzioni di esempio 

1. Scaricare il file [Sample App con estensione zip per Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Non è necessario aggiungere il riferimento di NuGet perché già incluso nel progetto di esempio.
2. Installare ed eseguire [Emulatore di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) versione 5.6 o successive. In alternativa, è possibile aggiornare il file *local.settings.json* con le stringhe di connessione di Archiviazione di Azure autentiche.
3. Aprire il progetto in Visual Studio 2017. 
4. Per istruzioni su come eseguire l'esempio, iniziare con l'esempio relativo al [Concatenamento delle funzioni: Sequenza Hello di esempio](durable-functions-sequence.md). L'esempio può essere eseguito in locale o pubblicato in Azure.

### <a name="start-with-an-empty-project"></a>Iniziare con un progetto vuoto
 
Seguire le stesse istruzioni fornite per l'avvio con l'esempio, ma eseguire i passaggi seguenti invece di scaricare il file con estensione *zip*:

1. Creare un progetto di app per le funzioni.
2. Cercare il riferimento al pacchetto NuGet seguente usando *Gestisci pacchetti NuGet* e aggiungerlo al progetto: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.6.0
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code offre un'esperienza di sviluppo locale che copre tutte le principali piattaforme, Windows, macOS e Linux.  Le funzioni possono essere eseguite in locale ed essere pubblicate anche in Azure. È possibile iniziare con un progetto vuoto o con un set di funzioni di esempio.

### <a name="prerequisites"></a>Prerequisiti

* Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/Download) 

* Seguire le istruzioni in "Installare gli strumenti di base per Funzioni di Azure" in [Scrivere codici per Funzioni di Azure e testarle in locale](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Se si dispone già degli strumenti multipiattaforma di Funzioni di Azure, aggiornarli alla versione più recente disponibile.

    >[!IMPORTANT]
    >L'estensione Funzioni permanenti in JavaScript richiede la versione 2.x degli strumenti di base di Funzioni di Azure.

*  Se si usa un computer Windows, installare ed eseguire [Emulatore di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) versione 5.6 o successiva. In alternativa, è possibile aggiornare il file *local.settings.json* con la connessione di Archiviazione di Azure autentiche. 


### <a name="start-with-sample-functions"></a>Iniziare con funzioni di esempio

#### <a name="c"></a>C#

1. Clonare il [repository delle funzioni permanenti](https://github.com/Azure/azure-functions-durable-extension.git).
2. Spostarsi nel computer nella [cartella degli script di esempio C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Installare l'estensione Funzioni permanenti di Funzioni di Azure eseguendo le operazioni seguenti in un prompt dei comandi/finestra del terminale:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.6.0
    ```
4. Installare l'estensione Twilio di Funzioni di Azure eseguendo le operazioni seguenti in un prompt dei comandi o una finestra del terminale:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta8
    ```
5. In alternativa, è possibile aggiornare il file *local.settings.json* con la stringa di connessione di Archiviazione di Azure autentica.
6. Aprire il progetto in Visual Studio Code. 
7. Per istruzioni su come eseguire l'esempio, iniziare con l'esempio relativo al [Concatenamento delle funzioni: Sequenza Hello di esempio](durable-functions-sequence.md). L'esempio può essere eseguito in locale o pubblicato in Azure.
8. Avviare il progetto eseguendo il comando seguente nel prompt/terminale dei comandi:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (solo funzioni v2)

1. Clonare il [repository delle funzioni permanenti](https://github.com/Azure/azure-functions-durable-extension.git).
2. Spostarsi nella [cartelle di esempi JavaScript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Installare l'estensione Funzioni permanenti di Funzioni di Azure eseguendo le operazioni seguenti in un prompt dei comandi/finestra del terminale

    ```
    func extensions install
    ```
    > [!NOTE] 
    > Questa operazione richiede l'installazione di [.NET Core SDK](https://www.microsoft.com/net/download) nel computer
4. Ripristinare i pacchetti npm eseguendo questo comando in una finestra del prompt dei comandi/terminale:
    
    ```
    npm install
    ``` 
5. Aggiornare il file *local.settings.json* con la stringa di connessione di un account di archiviazione di Azure `AzureWebJobsStorage`.  Questo account di archiviazione verrà usato per lo stato di funzione permanente.
6. Aprire il progetto in un editor come Visual Studio Code. 
7. Per istruzioni su come eseguire l'esempio, iniziare con l'esempio relativo al [Concatenamento delle funzioni: Sequenza Hello di esempio](durable-functions-sequence.md). L'esempio può essere eseguito in locale o pubblicato in Azure.
8. Avviare il progetto eseguendo il comando seguente nel prompt/terminale dei comandi:
    ```
    func start
    ```

### <a name="start-with-an-empty-project"></a>Iniziare con un progetto vuoto
 
1. Nel prompt/terminale dei comandi passare alla cartella che ospiterà l'app per le funzioni.
3. Creare un progetto app per le funzioni con il comando seguente:

    ```
    func init
    ``` 
4. Eseguire l'emulatore di archiviazione di Azure (solo Windows) o aggiornare il file *local.settings.json* con la stringa di connessione di Archiviazione di Azure autentica per `AzureWebJobsStorage`.
5. Successivamente, creare una nuova funzione con il comando seguente e seguire i passaggi della procedura guidata:

    ```
    func new
    ```
    >[!IMPORTANT]
    > Attualmente il modello di funzione permanente non è disponibile, ma è possibile iniziare con una delle opzioni supportate e quindi modificare il codice. Usare come riferimento gli esempi [Client di orchestrazione](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Trigger di orchestrazione](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence) e [Trigger di attività](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).
2. Installare l'estensione Funzioni permanenti di Funzioni di Azure eseguendo le operazioni seguenti in un prompt dei comandi/finestra del terminale nella directory delle app per le funzioni:

    ```
    func extensions install
    ```

6. Aprire la cartella di progetto in Visual Studio Code e continuare modificando il codice del modello. 
7. Avviare il progetto eseguendo il comando seguente nel prompt/terminale dei comandi:
    ```
    func start
    ```

## <a name="azure-portal"></a>Portale di Azure

È possibile usare il [portale di Azure](https://portal.azure.com) per lo sviluppo di Durable Functions.

   > [!NOTE]
   > L'estensione Funzioni permanenti in JavaScript non è ancora disponibile nel portale.

### <a name="create-an-orchestrator-function"></a>Creare una funzione di agente di orchestrazione

1. Creare una nuova app per le funzioni nel portale, come illustrato nell'[articolo introduttivo alle funzioni](functions-create-first-azure-function.md#create-a-function-app).

2. Configurare l'app per le funzioni affinché usi la [versione di runtime 2.0](set-runtime-version.md).

   L'estensione Funzioni permanenti funziona sia nel runtime 1.X che nel runtime 2.0, ma i modelli del portale di Azure sono disponibili solo quando si imposta come destinazione il runtime 2.0.

3. Creare una nuova funzione selezionando **"Creare una funzione personalizzata."**.

4. Modificare il **Linguaggio** in **C#**, lo **Scenario** in **Funzioni permanenti** e selezionare il modello **Durable Functions Http Starter - C#**.

5. In **Estensioni non installate**, fare clic su **Installa** per scaricare l'estensione da NuGet.org. 

6. Al termine dell'installazione, procedere con la creazione di una funzione di client di orchestrazione, **"HttpStart"** che è possibile creare selezionando il modello **Durable Functions Http Starter - C#**.

7. A questo punto, creare una funzione di orchestrazione **"HelloSequence"** dal modello **Agente di orchestrazione di Funzioni permanenti - C#**.

8. E l'ultima funzione verrà chiamata **"Hello"** dal modello **Durable Functions Activity - C#**.

9. Passare alla funzione **"HttpStart"** e copiare il relativo URL.

10. Usare Postman o cURL per richiamare la funzione permanente. Prima di eseguire il test, sostituire nell'URL **{functionName}** con il nome della funzione di agente di orchestrazione **HelloSequence**.  Non sono necessari dati, usare semplicemente il verbo POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Richiamare quindi l'endpoint **"statusQueryGetUri"** e verrà visualizzato lo stato corrente della funzione permanente

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Continuare a richiamare l'endpoint **"statusQueryGetUri"** fino a quando lo stato diventa **"Completato"** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Congratulazioni! La prima funzione permanente è attiva e in esecuzione nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la funzione di concatenamento di esempio](durable-functions-sequence.md)
