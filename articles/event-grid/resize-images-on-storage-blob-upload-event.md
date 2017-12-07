---
title: Usare Griglia di eventi di Azure per automatizzare il ridimensionamento delle immagini caricate | Microsoft Docs
description: "Griglia di eventi di Azure può attivare i caricamenti BLOB in Archiviazione di Azure. Ciò consente di inviare file di immagini caricati in Archiviazione di Azure ad altri servizi, ad esempio a Funzioni di Azure, perché vengano ridimensionati o migliorati."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 22eafca56eb5677c63a833d298799b725c50f768
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi

[Griglia di eventi di Azure](overview.md) è un servizio di gestione degli eventi per il cloud. Griglia di eventi consente di creare sottoscrizioni a eventi generati dai servizi di Azure o da risorse di terze parti.  

Questa esercitazione è la seconda parte di una serie di esercitazioni sull'archiviazione. Approfondisce l'[esercitazione precedente sull'archiviazione][previous-tutorial] e aggiunge la generazione automatica di anteprima senza server usando Funzioni di Azure e Griglia di eventi di Azure. Griglia di eventi consente a [Funzioni di Azure](..\azure-functions\functions-overview.md) di rispondere agli eventi di [Archiviazione BLOB di Azure](..\storage\blobs\storage-blobs-introduction.md) e generare anteprime delle immagini caricate. Viene creata una sottoscrizione di eventi per l'evento di creazione dell'archivio BLOB. Quando si aggiunge un BLOB a un contenitore di archiviazione BLOB specifico, viene richiamato un endpoint della funzione. I dati passati all'associazione della funzione da Griglia di eventi vengono usati per accedere al BLOB e generare l'immagine di anteprima. 

Usare l'interfaccia della riga di comando di Azure e il portale di Azure per aggiungere le funzionalità di ridimensionamento a un'app esistente di caricamento di immagini.

![App Web pubblicata nel browser Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * creare un account generale di Archiviazione di Azure
> * distribuire il codice senza server mediante Funzioni di Azure
> * creare una sottoscrizione di eventi di archiviazione BLOB in Griglia di eventi

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

+ È necessario aver completato l'esercitazione precedente sull'archiviazione BLOB: [Upload image data in the cloud with Azure Storage][previous-tutorial] (Caricare dati dell'immagine nel cloud con Archiviazione di Azure). 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0.14 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Per Funzioni di Azure è necessario un account di archiviazione generale. Creare un altro account di archiviazione generale nel gruppo di risorse usando il comando [az storage account create](/cli/azure/storage/account#create).

I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. 

Nel comando seguente sostituire il segnaposto `<general_storage_account>` con il nome globalmente univoco dell'account di archiviazione generale. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Creare un'app per le funzioni  

Per ospitare l'esecuzione della funzione è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#create). 

Nel comando seguente sostituire il segnaposto `<function_app>` con il nome univoco dell'app per le funzioni. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<function_app>` sia univoco tra tutte le app in Azure. In questo caso, `<general_storage_account>` è il nome dell'account di archiviazione generale creato.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

È ora necessario configurare l'app per le funzioni per connettersi all'archiviazione BLOB. 

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

La funzione richiede la stringa di connessione per connettersi all'account di archiviazione BLOB. In questo caso, `<blob_storage_account>` è il nome dell'account di archiviazione BLOB creato nell'esercitazione precedente. Ottenere la stringa di connessione con il comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Anche il nome del contenitore dell'immagini di anteprima deve essere impostato su `thumbs`. Aggiungere le impostazioni dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

È ora possibile distribuire un progetto di codice funzione in quest'app per le funzioni.

## <a name="deploy-the-function-code"></a>Distribuire il codice funzione 

La funzione C# che esegue il ridimensionamento delle immagini è disponibile in questa [repository GitHub di esempio](https://github.com/Azure-Samples/function-image-upload-resize). Distribuire il progetto di codice funzione nell'app per le funzioni usando il comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

Nel comando seguente `<function_app>` è la stessa app per le funzioni creata nello script precedente.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

La funzione di ridimensionamento delle immagini viene attivata da una sottoscrizione di eventi per un evento BLOB creato. I dati passati al trigger includono l'URL del BLOB, che a sua volta viene passato all'associazione di input per ottenere l'immagine caricata dall'archivio BLOB. La funzione genera un'immagine di anteprima e scrive il flusso risultante in un contenitore separato nell'archiviazione BLOB. Per altre informazioni su questa funzione, vedere il [file readme nel repository di esempio](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
Il codice del progetto funzione viene distribuito direttamente dal repository pubblico di esempio. Per altre informazioni sulle opzioni di distribuzione per Funzioni di Azure, vedere [Distribuzione continua per Funzioni di Azure](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Creare una sottoscrizione di eventi

Una sottoscrizione di eventi indica quali eventi generati dal provider si desidera inviare a un endpoint specifico. In questo caso l'endpoint viene esposto dalla funzione. Usare la procedura seguente per creare una sottoscrizione di eventi dalla funzione nel portale di Azure: 

1. Nel [portale di Azure](https://portal.azure.com) fare clic sulla freccia in basso a sinistra per espandere tutti i servizi, digitare `functions` nel campo **Filtro** e quindi scegliere **App per le funzioni**. 

    ![Passare ad App per le funzioni nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Espandere l'app per le funzioni, scegliere la funzione **imageresizerfunc** e quindi selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

    ![Passare ad App per le funzioni nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Usare le impostazioni della sottoscrizione di eventi specificate nella tabella.

    ![Creare una sottoscrizione di eventi dalla funzione nel portale di Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome che identifica la nuova sottoscrizione di eventi. | 
    | **Tipo di argomento** |  Account di archiviazione | Scegliere il provider di eventi Account di archiviazione. | 
    | **Sottoscrizione** | Sottoscrizione in uso | Per impostazione predefinita viene selezionata la sottoscrizione corrente.   |
    | **Gruppo di risorse** | myResourceGroup | Selezionare **Usa esistente** e scegliere il gruppo di risorse usato in questo argomento.  |
    | **Istanza** |  `<blob_storage_account>` |  Selezionare l'account di archiviazione BLOB creato. |
    | **Tipi di evento** | Blob created (BLOB creato) | Deselezionare tutti i tipi diversi da **Blob created** (BLOB creato). Solo i tipi di evento `Microsoft.Storage.BlobCreated` vengono passati alla funzione.| 
    | **Endpoint sottoscrittore** | generato automaticamente | Usare l'URL dell'endpoint che viene generato automaticamente. | 
    | **Filtro per prefisso** | /blobServices/predefinito/contenitori/immagini/blob/ | Filtra solo gli eventi di archiviazione nel contenitore **immagini**.| 

4. Fare clic su **Crea** per aggiungere la sottoscrizione di eventi. Questa operazione consente di creare una sottoscrizione di eventi che attiva **imageresizerfunc** quando viene aggiunto un BLOB al contenitore **immagini**. Le immagini ridimensionate vengono aggiunte al contenitore **anteprime**.

Ora che i servizi back-end sono stati configurati, testare la funzione di ridimensionamento delle immagini nell'applicazione Web di esempio. 

## <a name="test-the-sample-app"></a>Testare l'app di esempio

Per testare il ridimensionamento delle immagini nell'app Web, passare all'URL dell'app pubblicata. L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.

Fare clic sull'area **Upload photos** (Carica foto) per selezionare e caricare un file. È anche possibile trascinare una foto in quest'area. 

Si noti che quando l'immagine caricata scompare, nella sequenza video **Generated thumbnails** (Anteprime generate) viene mostrata una copia dell'immagine caricata. Questa immagine è stata ridimensionata dalla funzione, aggiunta al contenitore anteprime e scaricata dal client Web. 

![App Web pubblicata nel browser Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * creare un account generale di Archiviazione di Azure
> * distribuire il codice senza server mediante Funzioni di Azure
> * creare una sottoscrizione di eventi di archiviazione BLOB in Griglia di eventi

Passare alla terza parte della serie di esercitazioni su Archiviazione per apprendere a proteggere l'accesso all'account di archiviazione.

> [!div class="nextstepaction"]
> [Secure access to an applications data in the cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Proteggere l'accesso ai dati di applicazioni nel cloud)


+ Per altre informazioni su Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](overview.md). 
+ Per seguire un'altra esercitazione su Funzioni di Azure, vedere [Creare una funzione che si integra con le app per la logica di Azure](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md