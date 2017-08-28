---
title: Introduzione alla distribuzione di VOD tramite il portale di Azure | Documentazione Microsoft
description: Questa esercitazione illustra il processo di implementazione di un servizio per la distribuzione di contenuto video on demand (VoD) di base con l'applicazione Servizi multimediali di Azure (AMS) usando il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
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
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: cbb67ef92386a6288b3317bf77ebb67f15ce7fb2
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introduzione alla distribuzione di contenuto su richiesta tramite il portale di Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Questa esercitazione illustra il processo di implementazione di un servizio per la distribuzione di contenuto video on demand (VoD) di base con l'applicazione Servizi multimediali di Azure (AMS) usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione è necessario quanto segue:

* Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Account di Servizi multimediali. Per creare un account Servizi multimediali, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).

Questa esercitazione include le attività seguenti:

1. Avviare l'endpoint di streaming.
2. Caricare un file video.
3. Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
4. Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.  
5. Riprodurre i contenuti.

## <a name="start-streaming-endpoints"></a>Avviare gli endpoint di streaming 

Uno degli scenari più frequenti dell'uso di Servizi multimediali di Azure riguarda la distribuzione di contenuto video in streaming a bitrate adattivo. Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, come MPEG DASH, HLS e Smooth Streaming in modalità JIT, senza dover archiviare le versioni predefinite di ognuno di questi formati di streaming.

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

Per avviare l'endpoint di streaming, eseguire queste operazioni:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella finestra Impostazioni fare clic su Endpoint di streaming. 
3. Fare clic sull'endpoint di streaming predefinito. 

    Verrà visualizzata la finestra DETTAGLI ENDPOINT DI STREAMING PREDEFINITO.

4. Fare clic sull'icona di avvio.
5. Fare clic sul pulsante Salva per salvare le modifiche apportate.

## <a name="upload-files"></a>Caricare file
Per riprodurre video in streaming con Servizi multimediali di Azure, è necessario caricare i video di origine, codificarli in bitrate multipli e pubblicare il risultato. Il primo passaggio è illustrato in questa sezione. 

1. Nella finestra **Impostazione** fare clic su **Asset**.
   
    ![Caricare file](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Fare clic sul pulsante **Upload** .
   
    Verrà visualizzata la finestra **Carica un asset video** .
   
   > [!NOTE]
   > Non esistono limiti alle dimensioni dei file.
   > 
   > 
3. Passare al video desiderato nel computer locale, selezionarlo e fare clic su OK.  
   
    Il caricamento viene avviato ed è possibile visualizzare l'avanzamento sotto il nome del file.  

Al termine del caricamento, il nuovo asset viene visualizzato nella finestra **Asset** . 

## <a name="encode-assets"></a>Codificare gli asset

Quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti consiste nella distribuzione di contenuti in streaming a velocità in bit adattiva ai client. Servizi multimediali supporta le tecnologie di streaming a bitrate adattivo seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH. Per preparare i video per lo streaming a bitrate adattivo, è necessario codificare il video di origine in file a più bitrate. Per codificare i video, è consigliabile usare il codificatore **Media Encoder Standard** .  

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire file MP4 a bitrate multipli nei formati MPEG DASH, HLS e Smooth Streaming, senza dover ricreare i pacchetti con questi formati di streaming. Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un singolo formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Per sfruttare i vantaggi della creazione dinamica dei pacchetti, è necessario codificare il file di origine in un set di file MP4 a bitrate multipli. La procedura per la codifica è descritta più avanti in questa esercitazione.

### <a name="to-use-the-portal-to-encode"></a>Per usare il portale per la codifica
Questa sezione descrive la procedura per la codifica di contenuti con Media Encoder Standard.

1. Nella finestra **Impostazioni** selezionare **Asset**.  
2. Nella finestra **Asset** selezionare la risorsa che si vuole codificare.
3. Fare clic sul pulsante **Codifica** .
4. Nella finestra **Codifica un asset** selezionare il processore "Media Encoder Standard" e un set di impostazioni. Per informazioni sui set di impostazioni, vedere [Generare automaticamente un bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) e [Set di impostazioni delle attività MES](media-services-mes-presets-overview.md). Se si prevede di controllare il set di impostazioni di codifica usato, tenere presente che è importante selezionare il set di impostazioni più appropriato per il video di input. Ad esempio, se è noto che il video di input ha una risoluzione di 1920x1080 pixel, è possibile usare il set di impostazioni "Codec video H.264 a bitrate multiplo con risoluzione 1080p". Se il video disponibile è a bassa risoluzione (640x360), non usare il set di impostazioni "Codec video H.264 a bitrate multiplo con risoluzione 1080p".
   
   Per una gestione più semplice, è possibile modificare il nome dell'asset di output e il nome del processo.
   
   ![Codificare gli asset](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Fare clic su **Crea**.

### <a name="monitor-encoding-job-progress"></a>Monitorare lo stato del processo di codifica
Per monitorare l'avanzamento del processo di codifica, fare clic su **Impostazioni** nella parte superiore della pagina e selezionare **Processi**.

![Processi](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Pubblicare contenuti
Perché l'utente possa avere a disposizione un URL da usare per scaricare o riprodurre in streaming i contenuti, è prima necessario "pubblicare" un asset creando un localizzatore. I localizzatori forniscono l'accesso ai file contenuti nell'asset. Servizi multimediali supporta due tipi di localizzatori: 

* Localizzatori di streaming (OnDemandOrigin) usati per lo streaming adattivo, ad esempio per riprodurre in streaming file MPEG DASH, HLS o Smooth Streaming. Per creare un localizzatore di streaming, l'asset deve contenere un file con estensione ISM. 
* Localizzatori progressivi (SAS) usati per la distribuzione di video tramite download progressivo.

Un URL di streaming presenta il formato seguente e può essere usato per riprodurre asset Smooth Streaming.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Per creare un URL di streaming HLS, aggiungere (format=m3u8-aapl) all'URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Per creare un URL di streaming MPEG DASH, aggiungere (format=mpd-time-csf) all'URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Un URL di firma di accesso condiviso ha il formato seguente.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> I localizzatori creati attraverso il portale prima del mese di marzo 2015 hanno una validità di due anni.  
> 
> 

Per aggiornare la data di scadenza di un localizzatore, è possibile usare le API [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Per usare il portale per la pubblicazione di un asset
Per pubblicare un asset tramite il portale, seguire questa procedura:

1. Selezionare **Impostazioni** > **Asset**.
2. Selezionare l'asset da pubblicare.
3. Fare clic sul pulsante **Pubblica** .
4. Selezionare il tipo di localizzatore.
5. Fare clic su **Aggiungi**.
   
    ![Pubblica](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L'URL viene aggiunto all'elenco di **URL pubblicati**.

## <a name="play-content-from-the-portal"></a>Riprodurre contenuti dal portale
Il portale di Azure fornisce un lettore di contenuti che può essere usato per testare il proprio video.

Fare clic sul video richiesto e quindi sul pulsante **Riproduci** .

![Pubblica](./media/media-services-portal-vod-get-started/media-services-play.png)

Considerazioni applicabili:

* Per avviare lo streaming, avviare l'esecuzione dell'endpoint di streaming **predefinito**.
* Verificare che il video sia stato pubblicato.
* **Media Player** esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, fare clic per copiare l'URL e usare un altro lettore, ad esempio [Lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


