---
title: Caricare file in un account Servizi multimediali nel portale di Azure | Microsoft Docs
description: Questa esercitazione illustra la procedura di caricamento di file in un account Servizi multimediali nel portale di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 00035782a17936405b2b042035220dde87da12b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89257059"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Caricare file in un account Servizi multimediali nel portale di Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portale](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. Per informazioni aggiornate sui file di caricamento con il portale, vedere [usare il portale per caricare, codificare e trasmettere in flusso il contenuto](../latest/manage-assets-quickstart.md).<br/>Vedere anche: [servizi multimediali V3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

In Servizi multimediali di Azure è possibile caricare i file digitali in un asset. L'asset può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file. Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

Servizi multimediali prevede dimensioni massime per l'elaborazione dei file. Per informazioni sulle dimensioni massime dei file, vedere [Quote e limitazioni di Servizi multimediali](media-services-quotas-and-limitations.md).

Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Caricare file
1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Asset**. Selezionare quindi il pulsante **Carica**.
   
    ![Caricare file](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Verrà visualizzata la finestra **Carica un asset video** .
   
   > [!NOTE]
   > Servizi multimediali non limita le dimensioni dei file per il caricamento dei video.
 
3. Nel computer, passare al video da caricare. Selezionare il video, quindi **OK**.  
   
    Verrà avviato il caricamento. Lo stato è visibile sotto il nome del file.  

Al termine del caricamento, il nuovo asset viene visualizzato nel riquadro **Asset**. 

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [codificare gli asset caricati](media-services-portal-encode.md).

* È anche possibile usare Funzioni di Azure per attivare un processo di codifica quando un file arriva nel contenitore configurato. Per altre informazioni, vedere l'esempio in [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Servizi multimediali: Integrazione di Servizi multimediali di Azure con Funzioni di Azure e App per la logica).
