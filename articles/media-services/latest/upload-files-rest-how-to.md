---
title: Caricare file in un account di Servizi multimediali di Azure con REST Documenti Microsoft
description: Informazioni su come ottenere contenuti multimediali in Servizi multimediali creando e caricando asset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705769"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Caricare file in un account di Servizi multimediali v3 usando RESTUpload files into a Media Services v3 account using REST

In Servizi multimediali è possibile caricare i file digitali in un contenitore BLOB associato a un asset. L'entità [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) può contenere video, audio, immagini, raccolte di miniature, tracce di testo e file di sottotitoli codificati (e i metadati relativi a questi file). Dopo il caricamento dei file nel contenitore dell'asset, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

Questo articolo illustra come caricare un file locale usando REST.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- Consultare nuovamente il concetto di [asset](assets-concept.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).
    
    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Creare un asset

Questa sezione illustra come creare un nuovo asset.

1. Selezionare **Risorse** -> **Creare o aggiornare un asset**.
2. Fare clic su **Invia**.

    ![Creare un asset](./media/upload-files/postman-create-asset.png)

Viene visualizzata la **risposta** con le informazioni sull'asset appena creato.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Ottenere un URL di firma di accesso condiviso con autorizzazioni di lettura/scrittura 

Questa sezione illustra come ottenere un URL di firma di accesso condiviso generato per l'asset creato. L'URL di firma di accesso condiviso viene creato con autorizzazioni di lettura/scrittura e si può usare per caricare i file digitali nel contenitore di asset.

1. Selezionare **Risorse** -> **Elenca gli URL delle risorse**.
2. Fare clic su **Invia**.

    ![Caricare un file](./media/upload-files/postman-create-sas-locator.png)

Viene visualizzata la **risposta** con le informazioni sugli URL dell'asset. Copiare il primo URL e usarlo per caricare il file.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Caricare un file nell'archiviazione BLOB usando l'URL di caricamento

Usare le API di Archiviazione di Azure o gli SDK, ad esempio [l'API REST](../../storage/common/storage-rest-api-auth.md) di archiviazione o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video - RESTTutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md)
