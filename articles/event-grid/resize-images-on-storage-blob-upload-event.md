---
title: Usare Griglia di eventi di Azure per automatizzare il ridimensionamento delle immagini caricate | Microsoft Docs
description: Griglia di eventi di Azure può attivare i caricamenti BLOB in Archiviazione di Azure. Ciò consente di inviare file di immagini caricati in Archiviazione di Azure ad altri servizi, ad esempio a Funzioni di Azure, perché vengano ridimensionati o migliorati.
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 652fe182663d37c389658c8fe3b172826168e51f
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617989"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Esercitazione: Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi

[Griglia di eventi di Azure](overview.md) è un servizio di gestione degli eventi per il cloud. Griglia di eventi consente di creare sottoscrizioni a eventi generati dai servizi di Azure o da risorse di terze parti.  

Questa esercitazione è la seconda parte di una serie di esercitazioni sull'archiviazione. Approfondisce l'[esercitazione precedente sull'archiviazione][previous-tutorial] e aggiunge la generazione automatica di anteprima senza server usando Funzioni di Azure e Griglia di eventi di Azure. Griglia di eventi consente a [Funzioni di Azure](../azure-functions/functions-overview.md) di rispondere agli eventi di [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md) e generare anteprime delle immagini caricate. Viene creata una sottoscrizione di eventi per l'evento di creazione dell'archivio BLOB. Quando si aggiunge un BLOB a un contenitore di archiviazione BLOB specifico, viene richiamato un endpoint della funzione. I dati passati all'associazione della funzione da Griglia di eventi vengono usati per accedere al BLOB e generare l'immagine di anteprima.

Usare l'interfaccia della riga di comando di Azure e il portale di Azure per aggiungere le funzionalità di ridimensionamento a un'app esistente di caricamento di immagini.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * creare un account generale di Archiviazione di Azure
> * distribuire il codice senza server mediante Funzioni di Azure
> * creare una sottoscrizione di eventi di archiviazione BLOB in Griglia di eventi

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare questa esercitazione:

È necessario aver completato l'esercitazione precedente sull'archiviazione BLOB: [Caricare i dati immagine nel cloud con Archiviazione di Azure][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Se il provider di risorse di Griglia di eventi non è stato registrato in precedenza nella sottoscrizione, assicurarsi che sia registrato.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa esercitazione richiede l'interfaccia della riga di comando di Azure versione 2.0.14 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Per Funzioni di Azure è necessario un account di archiviazione generale. Oltre all'account di archiviazione BLOB creato nell'esercitazione precedente, creare un account di archiviazione generale separato nel gruppo di risorse usando il comando [az storage account create](/cli/azure/storage/account). I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. 

1. Impostare una variabile per includere il nome del gruppo di risorse creato nell'esercitazione precedente. 

    ```azurecli-interactive
    resourceGroupName=myResourceGroup
    ```
2. Impostare una variabile per il nome del nuovo account di archiviazione richiesto da Funzioni di Azure. 

    ```azurecli-interactive
    functionstorage=<name of the storage account to be used by the function>
    ```
3. Creare l'account di archiviazione per la funzione di Azure. 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind storage
    ```

## <a name="create-a-function-app"></a>Creare un'app per le funzioni  

Per ospitare l'esecuzione della funzione è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp). 

Nel comando seguente specificare il proprio nome di app per le funzioni univoco. Poiché verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che il app per le funzioni sia univoco tra tutte le app in Azure. 

1. Specificare un nome per l'app per le funzioni da creare. 

    ```azurecli-interactive
    functionapp=<name of the function app>
    ```
2. Creare la funzione di Azure. 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
    --resource-group $resourceGroupName --consumption-plan-location southeastasia
    ```

È ora necessario configurare l'app per le funzioni per la connessione all'account di archiviazione BLOB creato nell'[esercitazione precedente][previous-tutorial].

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

La funzione necessita di credenziali per l'account di archiviazione BLOB, che vengono aggiunte alle impostazioni dell'app per le funzioni usando il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
-n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Con l'impostazione `FUNCTIONS_EXTENSION_VERSION=~2` l'app per le funzioni viene eseguita nella versione 2.x del runtime di Funzioni di Azure.

È ora possibile distribuire un progetto di codice funzione in quest'app per le funzioni.

## <a name="deploy-the-function-code"></a>Distribuire il codice funzione 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

La funzione di ridimensionamento C# di esempio è disponibile in [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Distribuire il progetto di codice nell'app per le funzioni usando il comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source). 

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

La funzione di ridimensionamento di esempio Node.js è disponibile in [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node). Distribuire il progetto di codice funzione nell'app per le funzioni usando il comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

La funzione di ridimensionamento di esempio Node.js è disponibile in [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Distribuire il progetto di codice funzione nell'app per le funzioni usando il comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

La funzione di ridimensionamento delle immagini viene attivata dalle richieste HTTP inviate all'app dal servizio Griglia di eventi. È possibile segnalare al servizio Griglia di eventi che si vogliono ricevere queste notifiche nell'URL della funzione creando una sottoscrizione di eventi. Per questa esercitazione vengono sottoscritti eventi creati da BLOB.

I dati passati alla funzione dalla notifica del servizio Griglia di eventi includono l'URL del BLOB. Tale URL viene a sua volta passato all'associazione di input per ottenere l'immagine caricata dall'archivio BLOB. La funzione genera un'immagine di anteprima e scrive il flusso risultante in un contenitore separato nell'archiviazione BLOB. 

Questo progetto usa `EventGridTrigger` per il tipo di trigger. L'uso del trigger della Griglia di eventi è consigliato rispetto ai trigger HTTP generici. Griglia di eventi convalida automaticamente i trigger di funzioni della Griglia di eventi. Con i trigger HTTP generici è necessario implementare la [risposta di convalida](security-authentication.md).

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Per altre informazioni su questa funzione, vedere i [file function.json e run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Per altre informazioni su questa funzione, vedere i [file function.json e index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node/tree/master/Thumbnail).

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Per altre informazioni su questa funzione, vedere i [file function.json e index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Il codice del progetto funzione viene distribuito direttamente dal repository pubblico di esempio. Per altre informazioni sulle opzioni di distribuzione per Funzioni di Azure, vedere [Distribuzione continua per Funzioni di Azure](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Creare una sottoscrizione di eventi

Una sottoscrizione di eventi indica quali eventi generati dal provider si desidera inviare a un endpoint specifico. In questo caso l'endpoint viene esposto dalla funzione. Seguire questa procedura per creare una sottoscrizione di eventi che invia notifiche alla funzione nel portale di Azure: 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi** nel menu a sinistra e quindi selezionare **App per le funzioni**. 

    ![Passare ad App per le funzioni nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Espandere l'app per le funzioni, scegliere la funzione **Anteprima** e quindi selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

    ![Passare ad App per le funzioni nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Usare le impostazioni della sottoscrizione di eventi specificate nella tabella.
    
    ![Creare una sottoscrizione di eventi dalla funzione nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome che identifica la nuova sottoscrizione di eventi. | 
    | **Tipo di argomento** |  Account di archiviazione | Scegliere il provider di eventi Account di archiviazione. | 
    | **Sottoscrizione** | Sottoscrizione di Azure | Per impostazione predefinita viene selezionata la sottoscrizione di Azure corrente.   |
    | **Gruppo di risorse** | myResourceGroup | Selezionare **Usa esistente** e scegliere il gruppo di risorse usato in questa esercitazione.  |
    | **Risorsa** |  Account di archiviazione BLOB |  Selezionare l'account di archiviazione BLOB creato. |
    | **Tipi di evento** | Blob created (BLOB creato) | Deselezionare tutti i tipi diversi da **Blob created** (BLOB creato). Solo i tipi di evento `Microsoft.Storage.BlobCreated` vengono passati alla funzione.| 
    | **Tipo di sottoscrittore** |  generato automaticamente |  Predefinito come webhook. |
    | **Endpoint sottoscrittore** | generato automaticamente | Usare l'URL dell'endpoint che viene generato automaticamente. | 
4. Passare alla scheda **Filtro** ed eseguire le azioni seguenti:     
    1. Selezionare l'opzione **Abilita filtro per l'oggetto**.
    2. Per **L'oggetto inizia con** immettere il valore seguente: **/blobServices/default/containers/images/blobs/**.

        ![Specificare un filtro per la sottoscrizione di eventi](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png) 
2. Selezionare **Crea** per aggiungere la sottoscrizione di eventi. Questa operazione consente di creare una sottoscrizione di eventi che attiva la funzione `Thumbnail` quando viene aggiunto un BLOB al contenitore `images`. La funzione ridimensiona le immagini e le aggiunge al contenitore `thumbnails`.

Ora che i servizi back-end sono stati configurati, testare la funzione di ridimensionamento delle immagini nell'applicazione Web di esempio. 

## <a name="test-the-sample-app"></a>Testare l'app di esempio

Per testare il ridimensionamento delle immagini nell'app Web, passare all'URL dell'app pubblicata. L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Fare clic sull'area **Upload photos** (Carica foto) per selezionare e caricare un file. È anche possibile trascinare una foto in quest'area. 

Si noti che quando l'immagine caricata scompare, nella sequenza video **Generated thumbnails** (Anteprime generate) viene mostrata una copia dell'immagine caricata. Questa immagine è stata ridimensionata dalla funzione, aggiunta al contenitore *anteprime* e scaricata dal client Web.

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Fare clic su **Choose File** (Scegli file) per selezionare un file, quindi fare clic su **Upload Image** (Carica l'immagine). Quando il caricamento ha esito positivo, il browser passa a una pagina di operazione riuscita. Fare clic sul collegamento per tornare alla home page. Una copia dell'immagine caricata viene visualizzata nell'area **Generated thumbnails** (Anteprime generate). Se l'immagine non viene visualizzata, provare a ricaricare la pagina. Questa immagine è stata ridimensionata dalla funzione, aggiunta al contenitore *anteprime* e scaricata dal client Web.

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Fare clic su **Choose File** (Scegli file) per selezionare un file, quindi fare clic su **Upload Image** (Carica l'immagine). Quando il caricamento ha esito positivo, il browser passa a una pagina di operazione riuscita. Fare clic sul collegamento per tornare alla home page. Una copia dell'immagine caricata viene visualizzata nell'area **Generated thumbnails** (Anteprime generate). Se l'immagine non viene visualizzata, provare a ricaricare la pagina. Questa immagine è stata ridimensionata dalla funzione, aggiunta al contenitore *anteprime* e scaricata dal client Web.

![App Web pubblicata nel browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * creare un account generale di Archiviazione di Azure
> * distribuire il codice senza server mediante Funzioni di Azure
> * creare una sottoscrizione di eventi di archiviazione BLOB in Griglia di eventi

Passare alla terza parte della serie di esercitazioni su Archiviazione per apprendere a proteggere l'accesso all'account di archiviazione.

> [!div class="nextstepaction"]
> [Secure access to an applications data in the cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Proteggere l'accesso ai dati di applicazioni nel cloud)

+ Per altre informazioni su Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](overview.md). 
+ Per seguire un'altra esercitazione su Funzioni di Azure, vedere [Creare una funzione che si integra con le app per la logica di Azure](../azure-functions/functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
