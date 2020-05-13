---
title: Creare la prima funzione nel portale di Azure
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite il portale di Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 030af8a289daaf03d17f8402e8d603e893657853
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123617"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creare la prima funzione nel portale di Azure

Funzioni di Azure consente di eseguire il codice in un ambiente serverless senza dover prima creare una macchina virtuale (VM) o pubblicare un'applicazione Web. Questo articolo illustra come usare funzioni di Azure per creare una funzione trigger HTTP "Hello World" nel portale di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Per gli sviluppatori in C# potrebbe essere opportuno [creare la prima funzione in Visual Studio 2019](functions-create-your-first-function-visual-studio.md) anziché nel portale. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Creare quindi una funzione nella nuova app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione trigger HTTP

1. Dal menu a sinistra della finestra **funzioni** selezionare **funzioni**, quindi scegliere **Aggiungi** dal menu in alto. 
 
1. Dalla finestra **nuova funzione** selezionare **trigger http**.

    ![Scegliere la funzione trigger HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Nella finestra **nuova funzione** accettare il nome predefinito per la **nuova funzione**oppure immettere un nuovo nome. 

1. Scegliere **Anonimo** dall'elenco a discesa **livello di autorizzazione** e quindi selezionare **Crea funzione**.

    Azure crea la funzione trigger HTTP. Ora è possibile eseguire la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione trigger HTTP selezionare **codice + test** dal menu a sinistra, quindi selezionare **Ottieni URL funzione** dal menu in alto.

    ![Selezionare Ottieni URL funzione](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Nella finestra di dialogo **Ottieni URL funzione** selezionare **predefinito** dall'elenco a discesa, quindi selezionare l'icona **copia negli Appunti** . 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa `?name=<your_name>` di query alla fine dell'URL e premere INVIO per eseguire la richiesta. 

    L'esempio seguente mostra la risposta nel browser:

    ![Risposta della funzione nel browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    L'URL della richiesta include una chiave necessaria per impostazione predefinita per accedere a una funzione tramite HTTP.

1. Quando viene eseguita la funzione, vengono scritte nei log informazioni di traccia. Per visualizzare l'output di traccia, tornare alla pagina **codice + test** nel portale ed espandere la freccia **log** nella parte inferiore della pagina.

   ![Visualizzatore log di Funzioni nel portale di Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

