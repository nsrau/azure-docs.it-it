---
title: Creare una funzione in Azure attivata dall'archiviazione BLOB| Microsoft Docs
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata da elementi aggiunti all'archiviazione BLOB di Azure.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: a55f28fad4c70e49e417d2856568791b313ad1eb
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Creare una funzione attivata dall'archiviazione BLOB di Azure

Informazioni su come creare una funzione attivata nel momento in cui vengono caricati o aggiornati file nell'archiviazione BLOB di Azure.

![Visualizzare il messaggio nei log.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prerequisiti

+ Scaricare e installare [Microsoft Azure Storage Explorer](http://storageexplorer.com/).
+ Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![App per le funzioni creata correttamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Creare una funzione attivata dall'archiviazione BLOB

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Funzione personalizzata**. Verrà visualizzato il set completo di modelli di funzione.

    ![Pagina della guida introduttiva di Funzioni nel portale di Azure](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. Selezionare il modello **BlobTrigger** per la lingua desiderata e usare le impostazioni specificate nella tabella.

    ![Creare una funzione attivata dall'archiviazione BLOB.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Percorso**   | mycontainer/{name}    | Percorso da monitorare nell'archiviazione BLOB. Il nome file del BLOB viene passato nel binding come parametro _name_.  |
    | **Connessione dell'account di archiviazione** | AzureWebJobStorage | È possibile usare la connessione dell'account di archiviazione già usata dall'app per le funzioni oppure crearne una nuova.  |
    | **Dare un nome alla funzione** | Univoco nell'app per le funzioni | Nome della funzione attivata dal BLOB. |

3. Fare clic su **Crea** per creare la funzione.

Connettersi quindi all'account di archiviazione di Azure e creare il contenitore **mycontainer**.

## <a name="create-the-container"></a>Creare il contenitore

1. Nella funzione fare clic su **Integrazione**, espandere **Documentazione** e copiare sia **Nome account** sia **Chiave account**. Usare queste credenziali per connettersi all'account di archiviazione. Se si è già connessi all'account di archiviazione, andare al passaggio 4.

    ![Ottenere le credenziali per la connessione all'account di archiviazione.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Eseguire lo strumento [Microsoft Azure Storage Explorer](http://storageexplorer.com/), fare clic sull'icona di connessione a sinistra, scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione) e fare clic su **Avanti**.

    ![Eseguire lo strumento di esplorazione dell'account di archiviazione.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Immettere i valori **Nome account** e **Chiave account** definiti nel passaggio 1, fare clic su **Avanti** e quindi su **Connetti**. 

    ![Immettere le credenziali di archiviazione ed eseguire la connessione.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Espandere l'account di archiviazione associato, fare doppio clic su **Contenitori BLOB**, fare clic su **Crea contenitore BLOB**, digitare `mycontainer` e quindi premere INVIO.

    ![Creare una coda di archiviazione.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Dopo aver creato un contenitore BLOB, è possibile ora testare la funzione caricando un file nel contenitore.

## <a name="test-the-function"></a>Testare la funzione

1. Tornare al portale di Azure, selezionare la funzione, espandere i **log** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non sia stato interrotto.

1. In Esplora archivi espandere l'account di archiviazione, **Contenitori BLOB** e **mycontainer**. Fare clic su **Carica** e quindi su **Carica file...**.

    ![Caricare un file nel contenitore BLOB.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Nella finestra di dialogo **Carica file** fare clic sul campo **File**. Identificare un file nel computer locale, ad esempio un file di immagine, selezionarlo e fare clic su **Apri** e quindi su **Carica**.

1. Tornare ai log di funzione e verificare che il BLOB sia stato letto.

   ![Visualizzare il messaggio nei log.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Se l'app per le funzioni viene eseguita nel piano a consumo predefinito, è possibile che si verifichi un ritardo di alcuni minuti tra il momento in cui il BLOB viene aggiunto o aggiornato e il momento in cui viene attivata la funzione. Se nelle funzioni attivate dal BLOB è necessaria una bassa latenza, valutare l'opportunità di eseguire l'app per le funzioni in un piano di servizio app.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita nel momento in cui nell'archiviazione BLOB viene aggiunto o aggiornato un BLOB. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sui trigger dell'archiviazione BLOB, vedere [Binding dell'archiviazione BLOB di Funzioni di Azure](functions-bindings-storage-blob.md).

