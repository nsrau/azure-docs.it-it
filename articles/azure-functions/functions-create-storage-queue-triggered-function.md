---
title: Creare una funzione in Azure attivata da messaggi di coda
description: Usare Funzioni di Azure per creare una funzione serverless che viene richiamata da un messaggio inviato a una coda di Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: c4c20579f2306b61741f3c6ab1549285271435a3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123343"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Creare una funzione attivata dall'archiviazione code di Azure

Informazioni su come creare una funzione attivata nel momento in cui vengono inviati messaggi a una coda di archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="App per le funzioni creata correttamente." border="true":::

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Creare una funzione attivata da una coda

1. Selezionare **Funzioni** e quindi **+ Aggiungi** per aggiungere una nuova funzione.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Scegliere un modello di funzione nel portale di Azure." border="true":::

1. Scegliere il modello di **attivazione dell'archiviazione code di Azure**.

1. Usare le impostazioni specificate nella tabella disponibile sotto l'immagine.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Assegnare un nome e configurare la funzione attivata dall'archiviazione code." border="true":::


    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Nome** | Univoco nell'app per le funzioni | Nome della funzione attivata dalla coda. |
    | **Nome coda**   | myqueue-items    | Nome della coda a cui connettersi nell'account di archiviazione. |
    | **Connessione dell'account di archiviazione** | AzureWebJobsStorage | È possibile usare la connessione dell'account di archiviazione già usata dall'app per le funzioni oppure crearne una nuova.  |    

1. Selezionare **Crea funzione** per creare la funzione.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Creare una funzione attivata dall'archiviazione code." border="true":::

Connettersi quindi all'account di archiviazione di Azure e creare la coda di archiviazione **myqueue-items**.

## <a name="create-the-queue"></a>Creare la coda

1. Nella pagina **Panoramica** della funzione selezionare il gruppo di risorse.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Selezionare il gruppo di risorse del portale di Azure." border="true":::

1. Cercare e selezionare l'account di archiviazione del gruppo di risorse.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Accedere all'account di archiviazione." border="true":::

1. Scegliere **Code** e quindi **+ Coda**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Aggiungere una coda al proprio account di archiviazione nel portale di Azure." border="true":::

1. Nel campo **Nome** digitare `myqueue-items` e quindi selezionare **Crea**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Assegnare un nome al contenitore di archiviazione." border="true":::

È possibile ora testare la funzione aggiungendo un messaggio alla coda.

## <a name="test-the-function"></a>Testare la funzione

1. Tornare al portale di Azure, selezionare la funzione, espandere i **log** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non sia stato interrotto.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Espandere il log nel portale di Azure." border="true":::

1. In una finestra separata del browser passare al gruppo di risorse nel portale di Azure e selezionare l'account di archiviazione.

1. Selezionare **Code** e quindi il contenitore **myqueue-items**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Passare alla coda myqueue-items nel portale di Azure." border="true":::

1. Selezionare **Aggiungi messaggio** e digitare "Hello World!" in **Testo del messaggio**. Selezionare **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Passare alla coda myqueue-items nel portale di Azure." border="true":::

1. Attendere alcuni secondi, tornare ai log di funzione e verificare che il nuovo messaggio sia stato letto dalla coda.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Visualizzare il messaggio nei log." border="true":::

1. Tornare alla coda di archiviazione, selezionare **Aggiorna** e verificare che il messaggio sia stato elaborato e non sia più in coda.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita nel momento in cui un messaggio viene aggiunto a una coda di archiviazione. Per altre informazioni sui trigger dell'archiviazione code, vedere [Associazioni della coda dell'archiviazione di Funzioni di Azure](functions-bindings-storage-queue.md).

Dopo avere creato la prima funzione, è possibile aggiungere un'associazione di output alla funzione che esegue il writeback del messaggio in un'altra coda.

> [!div class="nextstepaction"]
> [Aggiungere messaggi a una coda di archiviazione di Azure tramite Funzioni](functions-integrate-storage-queue-output-binding.md)
