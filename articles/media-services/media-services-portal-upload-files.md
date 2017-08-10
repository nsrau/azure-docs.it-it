---
title: " Caricare file in un account Servizi multimediali usando il portale di Azure | Documentazione Microsoft"
description: Questa esercitazione illustra la procedura di caricamento di file in un account Servizi multimediali con il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8b9a7a266259077d49a38d51d7f801f420cc6315
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Caricare file in un account Servizi multimediali usando il portale di Azure
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


In Servizi multimediali è possibile caricare i file digitali in un asset. L'asset può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file. Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.


## <a name="upload-files"></a>Caricare file

>[!NOTE]
>È previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. Vedere [questo](media-services-quotas-and-limitations.md) argomento per informazioni dettagliate sulla limitazione per le dimensioni dei file.
>

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Nel pannello **Impostazioni** fare clic su **Asset**.
   
    ![Caricare file](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Fare clic sul pulsante **Upload** .
   
    Verrà visualizzata la finestra **Carica un asset video** .
   
   > [!NOTE]
   > Non esistono limiti alle dimensioni dei file.
   > 
   > 
4. Passare al video desiderato nel computer locale, selezionarlo e fare clic su OK.  
   
    Il caricamento viene avviato ed è possibile visualizzare l'avanzamento sotto il nome del file.  

Al termine del caricamento, il nuovo asset verrà visualizzato nella finestra **Asset** . 

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile codificare gli asset caricati. Per altre informazioni, vedere [Encode assets](media-services-portal-encode.md)(Codificare gli asset).

È anche possibile usare Funzioni di Azure per attivare un processo di codifica basato su un file che arriva nel contenitore configurato. Per altre informazioni, vedere [questo esempio](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


