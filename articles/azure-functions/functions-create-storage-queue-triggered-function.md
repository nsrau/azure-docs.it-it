---
title: Creare una funzione in Azure attivata da messaggi di coda | Microsoft Docs
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata da un messaggio inviato a una coda di archiviazione di Azure.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: ba8db575c8731e4f9067a6635e745da12c8667dd
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017

---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Creare una funzione attivata dall'archiviazione code di Azure

Informazioni su come creare una funzione attivata nel momento in cui vengono inviati messaggi a una coda di archiviazione di Azure.

![Visualizzare il messaggio nei log.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prerequisiti

- Scaricare e installare [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![App per le funzioni creata correttamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Creare una funzione attivata da una coda

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Funzione personalizzata**. Verrà visualizzato il set completo di modelli di funzione.

    ![Pagina della guida introduttiva di Funzioni nel portale di Azure](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

2. Selezionare il modello **QueueTrigger** per la lingua desiderata e usare le impostazioni specificate nella tabella.

    ![Creare una funzione attivata da una coda di archiviazione.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Nome coda**   | myqueue-items    | Nome della coda a cui connettersi nell'account di archiviazione. |
    | **Connessione dell'account di archiviazione** | AzureWebJobStorage | È possibile usare la connessione dell'account di archiviazione già usata dall'app per le funzioni oppure crearne una nuova.  |
    | **Dare un nome alla funzione** | Univoco nell'app per le funzioni | Nome della funzione attivata dalla coda. |

3. Fare clic su **Crea** per creare la funzione.

Connettersi quindi all'account di archiviazione di Azure e creare la coda di archiviazione **myqueue-items**.

## <a name="create-the-queue"></a>Creare la coda

1. Nella funzione fare clic su **Integrazione**, espandere **Documentazione** e copiare sia **Nome account** sia **Chiave account**. Usare queste credenziali per connettersi all'account di archiviazione. Se si è già connessi all'account di archiviazione, andare al passaggio 4.

    ![Ottenere le credenziali per la connessione all'account di archiviazione.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

1. Eseguire lo strumento [Microsoft Azure Storage Explorer](http://storageexplorer.com/), fare clic sull'icona di connessione a sinistra, scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e fare clic su **Avanti**.

    ![Eseguire lo strumento di esplorazione dell'account di archiviazione.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Immettere i valori **Nome account** e **Chiave account** definiti nel passaggio 1, fare clic su **Avanti** e quindi su **Connetti**.

    ![Immettere le credenziali di archiviazione ed eseguire la connessione.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Espandere l'account di archiviazione associato, fare doppio clic su **Code**, fare clic su **Crea coda**, digitare `myqueue-items` e quindi premere INVIO.

    ![Creare una coda di archiviazione.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

È possibile ora testare la funzione aggiungendo un messaggio alla coda.

## <a name="test-the-function"></a>Testare la funzione

1. Tornare al portale di Azure, selezionare la funzione, espandere i **log** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non sia stato interrotto.

1. In Esplora archivi espandere l'account di archiviazione, **Code** e **myqueue-items** e quindi fare clic su **Aggiungi messaggio**.

    ![Aggiungere un messaggio alla coda.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Digitare il proprio messaggio di benvenuto nel campo **Testo del messaggio** e fare clic su **OK**.

1. Attendere alcuni secondi, tornare ai log di funzione e verificare che il nuovo messaggio sia stato letto dalla coda.

    ![Visualizzare il messaggio nei log.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Tornare a Esplora archivi, fare clic su **Aggiorna** e verificare che il messaggio sia stato elaborato e non sia più in coda.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita nel momento in cui un messaggio viene aggiunto a una coda di archiviazione.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sui trigger dell'archiviazione code, vedere [Associazioni della coda dell'archiviazione di Funzioni di Azure](functions-bindings-storage-queue.md).
