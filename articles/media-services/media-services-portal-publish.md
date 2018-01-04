---
title: Pubblicare contenuti sul portale di Azure | Documentazione Microsoft
description: Questa esercitazione illustra i passaggi necessari per la pubblicazione di contenuti sul portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 6759d3f49e15a3b01022df318a83563ad6bd859f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="publish-content-in-the-azure-portal"></a>Pubblicare contenuti sul portale di Azure
> [!div class="op_single_selector"]
> * [di Microsoft Azure](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Panoramica
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Perché l'utente possa avere a disposizione un URL da usare per scaricare o riprodurre in streaming i contenuti, è prima necessario pubblicare un asset creando un localizzatore. I localizzatori forniscono accesso ai file di risorse. Servizi multimediali di Azure supporta due tipi di localizzatori: 

* **Localizzatori di streaming (OnDemandOrigin)**. I localizzatori di streaming vengono usati per il flusso adattivo. Alcuni esempi di flusso adattivo includono Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e DASH (Dynamic Adaptive Streaming over HTTP, denominato anche MPEG-DASH). Per creare un localizzatore di streaming, l'asset deve contenere un file con estensione ism. 
* **Localizzatori progressivi (firma di accesso condiviso)**. I localizzatori progressivi vengono usati per la distribuzione di video tramite download progressivo.

Per creare un URL di streaming HLS, aggiungere *(format=m3u8-aapl)* all'URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Per creare un URL di streaming per la riproduzione di asset Smooth Streaming, usare il formato URL seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Per creare un URL di streaming MPEG DASH, aggiungere *(format=mpd-time-csf)* all'URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Un URL di firma di accesso condiviso ha il formato seguente:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Per altre informazioni, vedere la [Panoramica della distribuzione di contenuti](media-services-deliver-content-overview.md).

> [!NOTE]
> I localizzatori creati nel portale di Azure prima di marzo 2015 hanno una data di scadenza di due anni.  
> 
> 

Per aggiornare la data di scadenza di un localizzatore, è possibile usare un'[API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Per usare il portale per la pubblicazione di un asset
1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Asset**. Selezionare l'asset da pubblicare.
3. Fare clic sul pulsante **Pubblica**.
4. Selezionare il tipo di localizzatore.
5. Selezionare **Aggiungi**.
   
    ![Pubblicare il video](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L'URL viene aggiunto all'elenco di **URL pubblicati**.

## <a name="play-content-in-the-portal"></a>Gestire contenuti nel portale
È possibile testare il video in un lettore di contenuti nel portale di Azure.

Selezionare il video, quindi fare clic sul pulsante **Riproduci**.

![Riprodurre il video nel portale di Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

Considerazioni applicabili:

* Verificare che il video sia stato pubblicato.
* Media Player nel portale di Azure esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, selezionare e copiare l'URL e quindi incollarlo in un altro lettore. È ad esempio possibile testare il video in [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* L'endpoint di streaming da cui si effettua lo streaming deve essere in esecuzione.  

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

