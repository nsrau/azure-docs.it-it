---
title: Creare una funzione in PowerShell con Visual Studio Code - Funzioni di Azure
description: Informazioni su come creare una funzione in PowerShell e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 5d85c0362daa9dc3b42ed0cc1329329ffa598bee
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637048"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Avvio rapido: Creare una funzione in PowerShell in Azure con Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

In questo articolo si userà Visual Studio Code per creare una funzione PowerShell che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione di questo articolo basata sull'interfaccia della riga di comando](create-first-function-cli-powershell.md).

## <a name="configure-your-environment"></a>Configurare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/), richiesto da Windows per npm. Solo le [versioni Active LTS e Maintenance LT](https://nodejs.org/about/releases/). Usare il comando `node --version` per controllare la versione in uso.
    Non necessario per lo sviluppo locale in macOS e Linux.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Sia il [runtime di .NET Core 3.1](https://www.microsoft.com/net/download) che il [runtime di .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ [Estensione PowerShell per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Creare il progetto locale

In questa sezione si userà Visual Studio Code per creare un progetto di Funzioni di Azure locale in PowerShell. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure.

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `PowerShell`.

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione. Per informazioni sul livello di autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

1. Usando queste informazioni, Visual Studio Code genera un progetto di Funzioni di Azure con un trigger HTTP. È possibile visualizzare i file di progetto locali in Explorer. Per altre informazioni sui file che vengono creati, vedere [File di progetto generati](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile usare Visual Studio Code per pubblicare il progetto direttamente in Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passaggi successivi

È stato usato Visual Studio Code per creare un'app per le funzioni con una semplice funzione attivata tramite HTTP. Il prossimo articolo descrive come espandere questa funzione aggiungendo un binding di output. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. 

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
