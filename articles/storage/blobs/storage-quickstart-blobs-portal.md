---
title: 'Avvio rapido: Creare un BLOB con il portale di Azure'
titleSuffix: Azure Storage
description: In questa guida introduttiva si usa il portale di Azure con l'archivio oggetti (BLOB). Si usa quindi il portale di Azure per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523332"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con il portale di Azure

In questa guida introduttiva si apprenderà come usare il [portale di Azure](https://portal.azure.com/) per creare un contenitore in Archiviazione di Azure e per caricare e scaricare BLOB in blocchi in tale contenitore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore nel portale di Azure, seguire questa procedura:

1. Passare al nuovo account di archiviazione nel portale di Azure.
2. Nel menu a sinistra per l'account di archiviazione scorrere fino alla sezione **Servizio BLOB** e quindi selezionare **Contenitori**.
3. Selezionare il pulsante **+ Contenitore**.
4. Immettere un nome per il nuovo contenitore. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-). Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).
5. Impostare il livello di accesso pubblico del contenitore. Il livello predefinito è **Private (no anonymous access)** (Privato - nessun accesso anonimo).
6. Fare clic su **OK** per creare il contenitore.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Screenshot che mostra come creare un contenitore nel portale di Azure":::

## <a name="upload-a-block-blob"></a>Caricare un BLOB in blocchi

I BLOB in blocchi sono costituiti da blocchi di dati combinati per creare un BLOB. La maggior parte degli scenari di archiviazione BLOB usa BLOB in blocchi. I BLOB in blocchi sono ideali per l'archiviazione di dati di testo e binari nel cloud, ad esempio file, immagini e video. Questa guida introduttiva spiega come usare i BLOB in blocchi.

Per caricare un BLOB in blocchi nel nuovo contenitore nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure passare al contenitore creato nella sezione precedente.
1. Selezionare il contenitore per visualizzare un elenco dei BLOB contenuti. Poiché questo contenitore è nuovo, non conterrà ancora alcun BLOB.
1. Selezionare il pulsante **Carica** per aprire il pannello Carica ed esplorare il file system locale per trovare un file da caricare come BLOB in blocchi. Facoltativamente, è possibile espandere la sezione **Avanzate** per configurare altre impostazioni per l'operazione di caricamento.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Screenshot che mostra come caricare un BLOB dall'unità locale tramite il portale di Azure":::

1. Selezionare il pulsante **Carica** per caricare il BLOB.
1. Caricare tutti i BLOB desiderati in questo modo. Si noterà che i nuovi BLOB sono ora elencati all'interno del contenitore.

## <a name="download-a-block-blob"></a>Scaricare un BLOB in blocchi

È possibile scaricare un BLOB in blocchi per visualizzarlo nel browser o salvarlo nel file system locale. Per scaricare un BLOB in blocchi, seguire questa procedura:

1. Passare all'elenco di BLOB caricati nella sezione precedente.
1. Fare clic con il pulsante destro del mouse sul BLOB che si vuole scaricare e selezionare **Scarica**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Screenshot che mostra come scaricare un BLOB nel portale di Azure":::

## <a name="delete-a-block-blob"></a>Eliminare un BLOB in blocchi

Per eliminare uno o più BLOB nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure passare al contenitore.
1. Visualizzare l'elenco di BLOB nel contenitore.
1. Usare la casella di controllo per selezionare uno o più BLOB nell'elenco.
1. Selezionare il pulsante **Elimina** per eliminare i BLOB selezionati.
1. Nella finestra di dialogo confermare l'eliminazione e indicare se eliminare anche gli snapshot del BLOB.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Screenshot che mostra come eliminare i BLOB dal portale di Azure":::

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa guida di avvio rapido è sufficiente eliminare il contenitore. Verranno eliminati anche tutti i BLOB presenti nel contenitore.

Per eliminare il contenitore:

1. Nel portale di Azure passare all'elenco dei contenitori presenti nell'account di archiviazione.
1. Selezionare il contenitore da eliminare.
1. Selezionare il pulsante **Altro** ( **...** ) e scegliere **Elimina**.
1. Confermare di voler eliminare il contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un contenitore e caricare un BLOB con il portale di Azure. Per informazioni sull'uso di archiviazione BLOB da un'app Web, continuare con un'esercitazione che illustra come caricare immagini in un account di archiviazione.

> [!div class="nextstepaction"]
> [Esercitazione: Caricare i dati immagine nel cloud con Archiviazione di Azure](storage-upload-process-images.md)