---
title: Introduzione alla distribuzione di video on demand tramite il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra il processo di implementazione di un servizio di base per la distribuzione di contenuti video on demand con l'applicazione Servizi multimediali di Azure nel portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: fb981f3240799c924464c828b2c835ac5d9879ed
ms.contentlocale: it-it
ms.lasthandoff: 09/01/2017

---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Introduzione alla distribuzione di contenuto on demand tramite il portale di Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Questa esercitazione illustra il processo di implementazione di un servizio di base per la distribuzione di contenuti video on demand con l'applicazione Servizi multimediali di Azure nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione sono necessari gli elementi seguenti:

* Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Account di Servizi multimediali. Per creare un account Servizi multimediali, vedere [Come creare un account Servizi multimediali](media-services-portal-create-account.md).

Questa esercitazione include le attività seguenti:

1. Avviare l'endpoint di streaming.
2. Caricare un file video.
3. Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
4. Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.  
5. Riprodurre i contenuti.

## <a name="start-the-streaming-endpoint"></a>Avviare l'endpoint di streaming

Uno degli scenari più frequenti dell'uso di Servizi multimediali di Azure riguarda la distribuzione di contenuto video in streaming a bitrate adattivo. Servizi multimediali consente la creazione dinamica dei pacchetti. Con la creazione dinamica dei pacchetti è possibile distribuire contenuti in codifica MP4 a bitrate adattivo in formati di streaming JIT (Just-In-Time) supportati da Servizi multimediali. Alcuni esempi includono Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e DASH (Dynamic Adaptive Streaming over HTTP, denominato anche MPEG-DASH). Con lo streaming a bitrate adattivo di Servizi multimediali è possibile distribuire i video senza dover archiviare le versioni predefinite di ognuno di questi formati di streaming.

> [!NOTE]
> Quando si crea l'account Servizi multimediali, all'account viene aggiunto un endpoint di streaming predefinito con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

Per avviare l'endpoint di streaming:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Impostazioni** > **Endpoint di streaming**. 
3. Selezionare l'endpoint di streaming predefinito. Verrà visualizzata la finestra **DETTAGLI ENDPOINT DI STREAMING PREDEFINITO** .
4. Selezionare l'icona di **avvio**.
5. Fare clic sul pulsante **Salva**.

## <a name="upload-files"></a>Caricare file
Per riprodurre video in streaming con Servizi multimediali è necessario caricare i video di origine, codificarli in bitrate multipli e pubblicare il risultato. Il primo passaggio è illustrato in questa sezione. 

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Asset**. Selezionare quindi il pulsante **Carica**.
   
    ![Caricare file](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Verrà visualizzata la finestra **Carica un asset video** .
   
   > [!NOTE]
   > Servizi multimediali non limita le dimensioni dei file per il caricamento dei video.
   > 
   > 
3. Nel computer, passare al video da caricare. Selezionare il video, quindi **OK**.  
   
    Verrà avviato il caricamento. Lo stato è visibile sotto il nome del file.  

Al termine del caricamento, il nuovo asset viene visualizzato nel riquadro **Asset**. 

## <a name="encode-assets"></a>Codificare gli asset
Per sfruttare i vantaggi della creazione dinamica dei pacchetti è necessario codificare il file di origine in un set di file MP4 a bitrate multipli. I passaggi della codifica vengono illustrati in questa sezione.

### <a name="encode-assets-in-the-portal"></a>Codificare gli asset nel portale
Per codificare il contenuto tramite Media Encoder Standard nel portale di Azure:

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Asset**. Selezionare l'asset da codificare.
3. Selezionare il pulsante **Codifica**.
4. Nel riquadro **Codifica un asset** selezionare il processore **Media Encoder Standard** e un set di impostazioni. Per informazioni sui set di impostazioni, vedere [Generare automaticamente una tabella di coppie bitrate-risoluzione](media-services-autogen-bitrate-ladder-with-mes.md) e [Set di impostazioni delle attività MES (Media Encoder Standard)](media-services-mes-presets-overview.md). È importante scegliere il set di impostazioni più idoneo per i video di input. Se ad esempio è noto che il video di input ha una risoluzione di 1920 × 1080 pixel, è possibile scegliere il set di impostazioni **Codec video H.264 a bitrate multiplo con risoluzione 1080p**. Se il video disponibile è a bassa risoluzione (640 × 360), non usare il set di impostazioni **Codec video H.264 a bitrate multiplo con risoluzione 1080p**.
   
   Per una gestione più semplice delle risorse è possibile modificare il nome dell'asset di output e il nome del processo.
   
   ![Codificare gli asset](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selezionare **Crea**.

### <a name="monitor-encoding-job-progress"></a>Monitorare lo stato del processo di codifica
Per monitorare lo stato del processo di codifica, fare clic su **Impostazioni** nella parte superiore della pagina e selezionare **Processi**.

![Processi](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Pubblicare contenuti
Perché l'utente possa avere a disposizione un URL da usare per scaricare o riprodurre in streaming i contenuti, è prima necessario pubblicare un asset creando un localizzatore. I localizzatori forniscono l'accesso ai file contenuti nell'asset. Servizi multimediali di Azure supporta due tipi di localizzatori: 

* **Localizzatori di streaming (OnDemandOrigin)**. I localizzatori di streaming vengono usati per il flusso adattivo. Sono esempi di flusso adattivo HLS, Smooth Streaming e MPEG-DASH. Per creare un localizzatore di streaming, l'asset deve contenere un file con estensione ism. 
* **Localizzatori progressivi (firma di accesso condiviso)**. I localizzatori progressivi vengono usati per la distribuzione di video tramite download progressivo.

Per creare un URL di streaming HLS, aggiungere *(format=m3u8-aapl)* all'URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Per creare un URL di streaming per la riproduzione di asset Smooth Streaming, usare il formato URL seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Per creare un URL di streaming MPEG DASH, aggiungere *(format=mpd-time-csf)* all'URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Un URL di firma di accesso condiviso ha il formato seguente:

    {blob container name}/{asset name}/{file name}/{shared access signature}

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

## <a name="play-content-from-the-portal"></a>Riprodurre contenuti dal portale
È possibile testare il video in un lettore di contenuti nel portale di Azure.

Selezionare il video, quindi fare clic sul pulsante **Riproduci**.

![Riprodurre il video nel portale di Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

Considerazioni applicabili:

* Per avviare lo streaming, avviare l'esecuzione dell'endpoint di streaming predefinito.
* Verificare che il video sia stato pubblicato.
* Media Player nel portale di Azure esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, selezionare e copiare l'URL e quindi incollarlo in un altro lettore. È ad esempio possibile testare il video in [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

