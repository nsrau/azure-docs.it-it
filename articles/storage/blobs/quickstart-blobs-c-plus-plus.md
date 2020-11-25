---
title: 'Avvio rapido: Libreria di Archiviazione BLOB di Azure v12 - C++'
description: In questo argomento di avvio rapido viene illustrato come usare la libreria client di Archiviazione BLOB di Azure versione 12 per C++ per creare un contenitore e un BLOB nell'archiviazione BLOB (oggetto). Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 246d2677c784daee4cc6b083cff04b6993ab3818
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006490"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Avvio rapido: Libreria client di Archiviazione BLOB di Azure v12 per C++

Questo argomento di avvio rapido illustra come iniziare a usare la libreria client di Archiviazione BLOB di Azure v12 per C++. Archiviazione BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati.

Usare la libreria client di Archiviazione BLOB di Azure v12 per C++ per:

- Creare un contenitore
- Caricare un oggetto BLOB in Archiviazione di Azure
- Elencare tutti i BLOB in un contenitore
- Scaricare il BLOB nel computer locale
- Eliminare un contenitore

Risorse:

- [Documentazione di riferimento delle API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Esempi](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Account di archiviazione di Azure](../common/storage-account-create.md)
- [Compilatore C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - Gestione pacchetti C e C++](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione BLOB di Azure v12 per C++.

### <a name="install-the-packages"></a>Installare i pacchetti

Se non lo si è ancora fatto, installare i pacchetti LibCurl e LibXML2 con il comando `vcpkg install`.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Seguire le istruzioni su GitHub per acquisire e compilare [Azure SDK per C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Creare il progetto

In Visual Studio creare una nuova applicazione console C++ per Windows denominata *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Finestra di dialogo di Visual Studio per la configurazione di una nuova app console C++ per Windows":::

Aggiungere le librerie seguenti al progetto:

- libcurl.lib
- libxml2.lib
- bcrypt.lib
- Crypt32.Lib
- WS2_32.Lib
- azure-core.lib
- azure-storage-common.lib
- azure-storage-blobs.lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Il servizio Archiviazione BLOB di Azure è ottimizzato per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. Archiviazione BLOB offre tre tipi di risorse:

- L'account di archiviazione
- Un contenitore nell'account di archiviazione
- Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura di Archiviazione BLOB](./media/storage-blobs-introduction/blob1.png)

Per interagire con queste risorse, usare le classi C++ seguenti:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): la classe `BlobContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure. È la classe di base per tutte le classi BLOB specializzate.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): la classe `BlockBlobClient` consente di modificare i BLOB in blocchi di Archiviazione di Azure.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le attività seguenti con la libreria client di Archiviazione BLOB di Azure per C++:

- [Aggiungere file di inclusione](#add-include-files)
- [Ottenere la stringa di connessione](#get-the-connection-string)
- [Creare un contenitore](#create-a-container)
- [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
- [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
- [Scaricare BLOB](#download-blobs)
- [Eliminare un contenitore](#delete-a-container)

### <a name="add-include-files"></a>Aggiungere file di inclusione

Dalla directory del progetto:

1. Aprire il file della soluzione *BlobQuickstartV12.sln* in Visual Studio
1. In Visual Studio aprire il file di origine *BlobQuickstartV12.cpp*
1. Rimuovere eventuale codice all'interno di `main` che è stato generato automaticamente
1. Aggiungere le istruzioni `#include`

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata in [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Creare un contenitore

Creare un'istanza della classe [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) chiamando la funzione [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe). Chiamare quindi [Create](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) per creare il contenitore effettivo nell'account di archiviazione.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Aggiungere questo codice alla fine di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Dichiarare una stringa che contiene "Hello Azure!".
1. Ottenere un riferimento a un oggetto [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) chiamando il metodo [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) sul contenitore dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare la stringa nel BLOB chiamando la funzione [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d). Questa funzione crea il BLOB se non esiste oppure lo aggiorna se esiste già.

Aggiungere questo codice alla fine di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB presenti nel contenitore chiamando la funzione [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c). Al contenitore è stato aggiunto un unico BLOB, quindi l'operazione restituisce solo tale BLOB.

Aggiungere questo codice alla fine di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Scaricare BLOB

Ottenere le proprietà del BLOB caricato, quindi dichiarare e ridimensionare un nuovo oggetto `std::string` usando le proprietà del BLOB caricato. Scaricare il BLOB creato in precedenza nel nuovo oggetto `std::string` chiamando la funzione [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) nella classe di base [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html). Infine, visualizzare i dati del BLOB scaricato.

Aggiungere questo codice alla fine di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Eliminare un BLOB

Il codice seguente elimina il BLOB dal contenitore di Archiviazione BLOB di Azure chiamando la funzione [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615).

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente pulisce le risorse create dall'app eliminando l'intero contenitore tramite [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Aggiungere questo codice alla fine di `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea un contenitore e carica un file di testo in Archiviazione BLOB di Azure. L'esempio elenca quindi i BLOB nel contenitore, scarica il file e visualizza il contenuto del file. Infine, l'app elimina il BLOB e il contenitore.

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come caricare, scaricare ed elencare i BLOB con C++. È stato anche illustrato come creare ed eliminare un contenitore di Archiviazione BLOB di Azure.

Per visualizzare un esempio di Archiviazione BLOB per C++, procedere con:

> [!div class="nextstepaction"]
> [Esempio di Archiviazione BLOB di Azure SDK v12 per C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)