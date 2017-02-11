---
title: " Introduzione alla distribuzione di contenuto su richiesta con il portale di Azure | Documentazione Microsofts"
description: Questa esercitazione illustra il processo di implementazione di un servizio per la distribuzione di contenuto video on demand (VoD) di base con l&quot;applicazione Servizi multimediali di Azure (AMS) usando il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: b433c35817a0ba36003e8d506db9d2d6d97f9ff7


---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introduzione alla distribuzione di contenuto su richiesta tramite il portale di Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Questa esercitazione illustra il processo di implementazione di un servizio per la distribuzione di contenuto video on demand (VoD) di base con l'applicazione Servizi multimediali di Azure (AMS) usando il portale di Azure.

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Questa esercitazione include le attività seguenti:

1. Creare un account di Servizi multimediali di Azure.
2. Avviare l'endpoint di streaming.
3. Caricare un file video.
4. Codificare il file di origine in un set di file MP4 a velocità in bit adattiva.
5. Pubblicare l'asset e ottenere gli URL di streaming e di download progressivo.  
6. Riprodurre i contenuti.

## <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure
I passaggi descritti in questa sezione illustrano come creare un account Servizi multimediali di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+Nuovo** > **Web e dispositivi mobili** > **Servizi multimediali**.
   
    ![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. In **CREARE UN ACCOUNT DEL SERVIZIO MULTIMEDIALE** immettere i valori richiesti.
   
    ![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. In **Nome account**immettere il nome del nuovo account di AMS. Un nome di account di Servizi multimediali deve essere composto da tutte lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.
   2. In Sottoscrizione selezionare una delle diverse sottoscrizioni di Azure per le quali è disponibile l'accesso.
   3. In **Gruppo di risorse**selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.
   4. In **Località** selezionare l'area geografica usata per archiviare i contenuti multimediali e i record di metadati per l'account Servizi multimediali. Questa area viene usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. 
   5. In **Account di archiviazione**selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account Servizi multimediali oppure è possibile crearne uno. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.
      
       Altre informazioni sull'archiviazione sono disponibili [qui](../storage/storage-introduction.md).
   6. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
4. Fare clic su **Crea** nella parte inferiore del form.
   
    Dopo che l'account è stato creato, viene caricata la pagina della panoramica. Nella tabella dell'endpoint di streaming l'account avrà un endpoint di streaming predefinito con stato **Arrestato**. L'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 
   
    ![Impostazioni di Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Per gestire l'account AMS, ad esempio per caricare video, codificare asset, monitorare lo stato dei processi, usare la finestra **Impostazioni** .

## <a name="manage-keys"></a>Gestione delle chiavi
Per accedere a livello di codice all'account Servizi multimediali, sono necessarie le informazioni relative al nome dell'account e alla chiave primaria.

1. Nel portale di Azure selezionare l'account. 
   
    Su lato destro verrà visualizzata la finestra **Impostazioni** . 
2. Nella finestra **Impostazioni** selezionare **Chiavi**. 
   
    Nella finestra **Gestisci chiavi** sono visualizzati il nome dell'account e le chiavi primaria e secondaria. 
3. Per copiare i valori, scegliere il pulsante Copia.
   
    ![Chiavi di Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="start-streaming-endpoints"></a>Avviare gli endpoint di streaming 

Uno degli scenari più frequenti dell'uso di Servizi multimediali di Azure riguarda la distribuzione di contenuto video in streaming a bitrate adattivo. Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, come MPEG DASH, HLS e Smooth Streaming in modalità JIT, senza dover archiviare le versioni predefinite di ognuno di questi formati di streaming.

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

Per avviare l'endpoint di streaming, eseguire queste operazioni:

1. Nella finestra Impostazioni fare clic su Endpoint di streaming. 
2. Fare clic sull'endpoint di streaming predefinito. 

    Verrà visualizzata la finestra DETTAGLI ENDPOINT DI STREAMING PREDEFINITO.

3. Fare clic sull'icona di avvio.
4. Fare clic sul pulsante Salva per salvare le modifiche apportate.

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
4. Nella finestra **Codifica un asset** selezionare il processore "Media Encoder Standard" e un set di impostazioni. Ad esempio, se è noto che il video di input ha una risoluzione di 1920x1080 pixel, è possibile usare il set di impostazioni "Codec video H.264 a bitrate multiplo con risoluzione 1080p". Per altre informazioni sui set di impostazioni, vedere [questo](https://msdn.microsoft.com/library/azure/mt269960.aspx) articolo. È importante selezionare il set di impostazioni più appropriato per il video di input. Se il video disponibile è a bassa risoluzione (640x360), non usare il set di impostazioni "Codec video H.264 a bitrate multiplo con risoluzione 1080p".
   
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

Per aggiornare la data di scadenza di un localizzatore, è possibile usare le API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato.

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

* Verificare che il video sia stato pubblicato.
* **Media Player** esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, fare clic per copiare l'URL e usare un altro lettore, ad esempio [Lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


