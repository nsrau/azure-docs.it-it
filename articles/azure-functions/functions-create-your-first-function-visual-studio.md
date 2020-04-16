---
title: 'Guida introduttiva: Creare la prima funzione in Azure con Visual Studio'
description: Questa guida di avvio rapido illustra come creare e pubblicare una funzione di Azure attivata tramite HTTP usando Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308925"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Guida introduttiva: Creare la prima funzione in Azure con Visual Studio

Funzioni di Azure consente di eseguire il codice in un ambiente serverless senza dover prima creare una VM o pubblicare un'applicazione Web.

Questa guida di avvio rapido illustra come usare Visual Studio 2019 per creare e testare in locale un'app per le funzioni C# "hello world" attivata tramite HTTP e quindi pubblicarla in Azure. 

![Risposta localhost della funzione nel browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Questo argomento di avvio rapido è destinato a Visual Studio 2019. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, per prima cosa installare [Visual Studio 2019](https://azure.microsoft.com/downloads/). Durante l'installazione assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**. Se si preferisce creare un progetto Funzioni di Azure con Visual Studio 2017, è prima necessario installare gli [strumenti più recenti per Funzioni di Azure](functions-develop-vs.md#check-your-tools-version).

![Installare Visual Studio con il carico di lavoro Sviluppo di Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/dotnet/) prima di iniziare.

## <a name="create-a-function-app-project"></a>Creare un progetto di app per le funzioni

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio crea un progetto e una classe che contiene il codice boilerplate per il tipo di funzione trigger HTTP. L'attributo `FunctionName` del metodo imposta il nome della funzione, che per impostazione predefinita è `Function1`. L'attributo `HttpTrigger` specifica che la funzione è attivata da una richiesta HTTP. Il codice boilerplate invia una risposta HTTP che include un valore presente nel corpo della richiesta o nella stringa di query.

Espandere le funzionalità della funzione con associazioni di input e di output applicando gli attributi appropriati al metodo. Per altre informazioni, vedere la sezione [Trigger e associazioni](functions-dotnet-class-library.md#triggers-and-bindings) di [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md).

Ora che è stato creato il progetto di funzione con una funzione attivata tramite HTTP, è possibile testare la funzione nel computer locale.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Dato che Visual Studio si integra con Azure Functions Core Tools, è possibile testare le funzioni in locale usando il runtime completo di Funzioni di Azure.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

Per poter pubblicare il progetto, è prima necessario che la sottoscrizione di Azure includa un'app per le funzioni. La pubblicazione di Visual Studio crea un'app per le funzioni la prima volta che si pubblica il progetto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testare la funzione in Azure

1. Copiare l'URL di base dell'app per le funzioni dalla pagina del profilo **Pubblica**. Sostituire la parte `localhost:port` dell'URL usato per testare la funzione in locale con il nuovo URL di base. Aggiungere la stringa di query `?name=<YOUR_NAME>` a questo URL ed eseguire la richiesta.

    L'URL che chiama la funzione attivata tramite HTTP ha il formato seguente:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. L'immagine seguente mostra la risposta nel browser alla richiesta GET remota restituita dalla funzione:

    ![Risposta della funzione nel browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è usato Visual Studio per creare e pubblicare un'app per le funzioni C# in Azure con una semplice funzione attivata tramite HTTP. 

Passare all'articolo successivo per informazioni su come aggiungere un'associazione alla coda di archiviazione di Azure alla funzione:
> [!div class="nextstepaction"]
> [Aggiungere un binding della coda di archiviazione di Azure alla funzione](functions-add-output-binding-storage-queue-vs.md)

