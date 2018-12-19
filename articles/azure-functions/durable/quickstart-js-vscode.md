---
title: Creare la prima funzione durevole in Azure usando JavaScript
description: Creare e pubblicare una funzione durevole di Azure con Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: Funzioni di Azure, funzioni, elaborazione eventi, calcolo, architettura senza server
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: ad17b6ef032c7bc25a019d53f12cc33baa3163f3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340898"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Creare la prima funzione durevole in JavaScript

*Durable Functions* è un'estensione di [Funzioni di Azure](../functions-overview.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii.

Questo articolo spiega come l'estensione di Funzioni di Azure in Visual Studio Code per creare e testare una funzione durevole "hello world" in locale.  Questa funzione orchestrerà e concatenerà le chiamate ad altre funzioni. Il codice della funzione verrà quindi pubblicato in Azure.

![Esecuzione di una funzione durevole in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Installare [Visual Studio Code](https://code.visualstudio.com/download).

* Assicurarsi di avere gli [strumenti di Funzioni di Azure più recenti](../functions-develop-vs.md#check-your-tools-version).

* In un computer Windows verificare che [Emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) sia installato e in esecuzione. In un computer Mac o Linux è necessario usare un account di archiviazione Azure effettivo.

* Verificare che sia installata la versione 8.0 o successiva di [Node.js](https://nodejs.org/).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Installare il pacchetto npm di Durable Functions

1. Installare il `durable-functions`pacchetto npm eseguendo`npm install durable-functions` nella directory radice dell'app per le funzioni.

## <a name="create-a-starter-function"></a>Creare una funzione di avvio

Prima di tutto, creare una funzione attivata da HTTP che avvia l'orchestrazione di una funzione durevole.

1. Da **Azure: Funzioni** scegliere l'icona Crea funzione.

    ![Creare una funzione](./media/quickstart-js-vscode/create-function.png)

2. Selezionare la cartella contenente il progetto di app per le funzioni e quindi selezionare il modello di funzione **Trigger HTTP**.

    ![Scegliere il modello Trigger HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Digitare `HttpStart` come nome della funzione, premere INVIO e quindi selezionare **Anonima** come tipo di autenticazione.

    ![Scegliere l'autenticazione anonima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    Viene creata una funzione nel linguaggio prescelto usando il modello per una funzione attivata tramite HTTP.

4. Sostituire index.js con il codice JavaScript seguente:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

5. Sostituire function.json con il codice JSON seguente:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

A questo punto è stato creato un punto di ingresso nella funzione durevole. Verrà ora aggiunto un agente di orchestrazione.

## <a name="create-an-orchestrator-function"></a>Creare una funzione di agente di orchestrazione

Successivamente, si crea un'altra funzione che fungerà da agente di orchestrazione. Per motivi di praticità, viene usato il modello di funzione di trigger HTTP. Il codice della funzione viene sostituito dal codice dell'agente di orchestrazione.

1. Ripetere i passaggi della sezione precedente per creare una seconda funzione usando il modello di trigger HTTP. Questa volta assegnare alla funzione il nome `OrchestratorFunction`.

2. Aprire il file index.js per la nuova funzione e sostituire il contenuto con il codice seguente:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

3. Aprire il file function.json e sostituirlo con il codice JSON seguente:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

È stato aggiunto un agente di orchestrazione per coordinare le funzioni dell'attività. Verrà ora aggiunta la funzione dell'attività di riferimento.

## <a name="create-an-activity-function"></a>Creare una funzione di attività

1. Ripetere i passaggi delle sezioni precedenti per creare una terza funzione usando il modello di trigger HTTP, ma questa volta assegnare alla funzione il nome `SayHello`.

2. Aprire il file index.js per la nuova funzione e sostituire il contenuto con il codice seguente:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

3. Sostituire function.json con il codice JSON seguente:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

A questo punto, sono stati aggiunti tutti i componenti necessari per avviare un'orchestrazione e concatenare le funzioni dell'attività.

## <a name="test-the-function-locally"></a>Testare la funzione in locale

Azure Functions Core Tools consente di eseguire un progetto Funzioni di Azure nel computer di sviluppo locale. Viene richiesto di installare questi strumenti al primo avvio di una funzione da Visual Studio Code.  

1. In un computer Windows avviare l'emulatore di archiviazione di Azure e verificare che la proprietà **AzureWebJobsStorage** di local.settings.json sia impostata su `UseDevelopmentStorage=true`. In un computer Mac o Linux è necessario impostare la proprietà **AzureWebJobsStorage** sulla stringa di connessione di un account di archiviazione di Azure esistente. Sarà possibile creare un account di archiviazione più avanti in questo articolo.

2. Per testare la funzione, impostare un punto di interruzione nel codice della funzione e premere F5 per avviare il progetto di app per le funzioni. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**. Se è la prima volta che si usa Durable Functions, l'estensione Durable Functions viene installata e la compilazione potrebbe richiedere alcuni secondi.

    > [!NOTE]
    > Durable Functions di JavaScript richiedono una versione **1.7.0** o superiore delle estensioni **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Verificare che la versione dell'estensione Durable Functions nel file `extensions.csproj` soddisfi questo requisito. In caso contrario, arrestare l'app per le funzioni, modificare la versione e premere F5 per riavviare l'app per le funzioni.

3. Nel pannello **Terminale** copiare l'endpoint dell'URL della funzione attivata da HTTP.

    ![Output locale di Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser e visualizzare lo stato dell'orchestrazione.

5. Per interrompere il debug, premere MAIUSC+F1.

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testare la funzione in Azure

1. Copiare l'URL del trigger HTTP dal pannello **Output**. Il formato dell'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. Quando si usa l'app pubblicata si dovrebbe ottenere la stessa risposta di stato restituita in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Si è usato Visual Studio Code per creare e pubblicare un'app per le funzioni durevoli in JavaScript.

> [!div class="nextstepaction"]
> [Informazioni sui criteri di funzione permanente comuni](durable-functions-overview.md)