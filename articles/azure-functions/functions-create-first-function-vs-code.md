---
title: Creare la prima funzione in Azure con Visual Studio Code
description: Creare e pubblicare in Azure una semplice funzione attivata tramite HTTP usando l'estensione Funzioni di Azure in Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Funzioni di Azure, funzioni, elaborazione eventi, calcolo, architettura senza server
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fbde3c939a23e4023c0403c27433717a11762b08
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622063"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Creare la prima funzione con Visual Studio Code

Funzioni di Azure consente di eseguire il codice in un ambiente [senza server](https://azure.microsoft.com/solutions/serverless/), senza dover prima creare una macchina virtuale o pubblicare un'applicazione Web.

In questo articolo verrà illustrato come usare l'[estensione Funzioni di Azure per Visual Studio Code] per creare e testare una funzione "hello world" nel computer locale mediante Microsoft Visual Studio Code. Il codice della funzione verrà quindi pubblicato in Azure da Visual Studio Code.

![Codice di Funzioni di Azure in un progetto di Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

L'estensione attualmente supporta funzioni C#, JavaScript, Java e Python. I passaggi descritti in questo articolo e in quello seguente supportano solo le funzioni JavaScript e C#. Per informazioni su come usare Visual Studio Code per creare e pubblicare funzioni di Python, vedere [Creare e distribuire Funzioni di Azure serverless in Python con Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). Per informazioni su come usare Visual Studio Code per creare e pubblicare funzioni di PowerShell, consultare [Creare la prima funzione di PowerShell in Azure](functions-create-first-function-powershell.md). 

L'estensione è attualmente disponibile anteprima. Per altre informazioni, vedere la pagina relativa all'[estensione Funzioni di Azure per Visual Studio Code].

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* Installare [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Installare la versione 2.x di [Azure Functions Core Tools](functions-run-local.md#v2).

* Installare i requisiti specifici per il linguaggio scelto:

    | Linguaggio | Requisito |
    | -------- | --------- |
    | **C#** | [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Versioni di Active LTS e Maintenance LTS (consigliate 8.11.1 e 10.14.1).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Copiare l'URL del trigger HTTP dal pannello **Output**. Questo URL include la chiave di funzione, che viene passata per il `code` parametro di query. Come prima, assicurarsi di aggiungere la stringa di query `?name=<yourname>` alla fine dell'URL ed eseguire la richiesta.

    Il formato dell'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. Di seguito è illustrata la risposta nel browser alla richiesta GET remota restituita dalla funzione: 

    ![Risposta della funzione nel browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Passaggi successivi

È stato usato Visual Studio Code per creare un'app per le funzioni con una semplice funzione attivata tramite HTTP. Il prossimo articolo descrive come espandere questa funzione aggiungendo un binding di output. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. L'articolo successivo illustra anche come eseguire la pulizia di queste nuove risorse di Azure rimuovendo il gruppo di risorse creato.

> [!div class="nextstepaction"]
> [Aggiungere un binding della coda di archiviazione di Azure alla funzione](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Estensione Funzioni di Azure per Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
