---
title: Creare una funzione in Azure attivata dall'archiviazione BLOB
description: Usare Funzioni di Azure per creare una funzione serverless che viene richiamata da elementi aggiunti al contenitore di archiviazione BLOB.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123122"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Creare una funzione in Azure attivata dall'archiviazione BLOB

Informazioni su come creare una funzione attivata nel momento in cui vengono caricati o aggiornati i file nel contenitore di archiviazione BLOB.

## <a name="prerequisites"></a>Prerequisiti

+ Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

La creazione della nuova app per le funzioni è stata completata.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="App per le funzioni creata correttamente." border="true":::

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Creare una funzione attivata dall'Archiviazione BLOB di Azure

1. Selezionare **Funzioni** e quindi **+ Aggiungi** per aggiungere una nuova funzione.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Scegliere un modello di funzione nel portale di Azure." border="true":::

1. Scegliere il modello di **attivazione dell'Archiviazione BLOB di Azure**.

1. Usare le impostazioni specificate nella tabella disponibile sotto l'immagine.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Assegnare un nome e configurare la funzione attivata dall'archiviazione BLOB." border="true":::

    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Nuova funzione** | Univoco nell'app per le funzioni | Nome della funzione attivata dal BLOB. |
    | **Percorso**   | samples-workitems/{name}    | Percorso da monitorare nell'archiviazione BLOB. Il nome file del BLOB viene passato nel binding come parametro _name_.  |
    | **Connessione dell'account di archiviazione** | AzureWebJobsStorage | È possibile usare la connessione dell'account di archiviazione già usata dall'app per le funzioni oppure crearne una nuova.  |

1. Selezionare **Crea funzione** per creare la funzione.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Creare una funzione attivata dall'archiviazione BLOB." border="true":::

Successivamente, creare il contenitore **samples-workitems**.

## <a name="create-the-container"></a>Creare il contenitore

1. Nella pagina **Panoramica** della funzione selezionare il gruppo di risorse.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Selezionare il gruppo di risorse del portale di Azure." border="true":::

1. Cercare e selezionare l'account di archiviazione del gruppo di risorse.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Accedere all'account di archiviazione." border="true":::

1. Scegliere **Contenitori** e quindi **+ Contenitore**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Aggiungere il contenitore al proprio account di archiviazione nel portale di Azure." border="true":::

1. Nel campo **Nome** digitare `samples-workitems` e quindi selezionare **Crea**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Assegnare un nome al contenitore di archiviazione." border="true":::

Dopo aver creato un contenitore BLOB, è possibile ora testare la funzione caricando un file nel contenitore.

## <a name="test-the-function"></a>Testare la funzione

1. Tornare al portale di Azure, selezionare la funzione, espandere i **log** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non sia stato interrotto.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Espandere il log nel portale di Azure." border="true":::

1. In una finestra separata del browser passare al gruppo di risorse nel portale di Azure e selezionare l'account di archiviazione.

1. Selezionare **Contenitori** e quindi il contenitore **samples-workitems**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Passare al contenitore samples-workitems nel portale di Azure." border="true":::

1. Selezionare **Carica** e quindi l'icona della cartella per scegliere un file da caricare.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Caricare un file nel contenitore BLOB." border="true":::

1. Identificare un file nel computer locale, ad esempio un file di immagine, e selezionarlo. Selezionare **Apri** e quindi **Carica**.

1. Tornare ai log di funzione e verificare che il BLOB sia stato letto.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Visualizzare il messaggio nei log." border="true":::

    >[!NOTE]
    > Se l'app per le funzioni viene eseguita nel piano a consumo predefinito, è possibile che si verifichi un ritardo di alcuni minuti tra il momento in cui il BLOB viene aggiunto o aggiornato e il momento in cui viene attivata la funzione. Se nelle funzioni attivate dal BLOB è necessaria una bassa latenza, valutare l'opportunità di eseguire l'app per le funzioni in un piano di servizio app.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita nel momento in cui nell'archiviazione BLOB viene aggiunto o aggiornato un BLOB. Per altre informazioni sui trigger dell'archiviazione BLOB, vedere [Binding dell'archiviazione BLOB di Funzioni di Azure](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
