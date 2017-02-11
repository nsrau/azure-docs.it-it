---
title: Distribuzione di contenuti ai clienti | Microsoft Docs
description: Questo argomento fornisce informazioni generali su tutti gli aspetti inerenti la distribuzione di contenuti con Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: 42428d9456c5ea00192a981265bd50263cbf66ba


---
# <a name="deliver-content-to-customers"></a>Distribuire contenuti ai clienti
Quando si distribuiscono contenuti in streaming o video on demand ai clienti, l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete.

Per raggiungere questo obiettivo è possibile:

* Codificare il flusso in un flusso video a bitrate multipli (a bitrate adattivo). In questo modo la qualità e le condizioni di rete saranno gestite adeguatamente.
* Usare la funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md) di Servizi multimediali di Microsoft Azure per riorganizzare dinamicamente il flusso in nuovi pacchetti creati con protocolli diversi. In questo modo si garantisce la trasmissione a diversi tipi di dispositivi. Servizi multimediali supporta le tecnologie di streaming a bitrate adattivo seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

In questo articolo viene fornita una panoramica di importanti concetti di consegna dei contenuti.

Per verificare i problemi noti, vedere [Problemi noti](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>creazione dinamica dei pacchetti
La creazione dinamica dei pacchetti offerta da Servizi multimediali consente di distribuire i contenuti codificati in formato MP4 o Smooth Streaming a bitrate adattivo nei formati di streaming supportati da Servizi multimediali, ovvero MPEG-DASH, HLS, Smooth Streaming, senza dover ricreare i pacchetti in questi formati di streaming. Si consiglia di distribuzione il contenuto con la creazione dinamica dei pacchetti.

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

* Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a bitrate adattivo.
* Ottenere almeno un'unità di streaming on demand per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere l'articolo sulla [procedura per scalare unità riservate di streaming on demand](media-services-portal-manage-streaming-endpoints.md).

La creazione dinamica dei pacchetti consente di archiviare e pagare i file in un solo formato di archiviazione. Servizi multimediali creerà e fornirà la risposta appropriata in base alle richieste.

Oltre a consentire l'accesso alle funzionalità di creazione dinamica dei pacchetti, le unità riservate di streaming on demand offrono capacità in uscita dedicate, acquistabili in incrementi di 200 Mbps. Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo o capacità in uscita) vengono condivise con tutti gli altri utenti. È possibile migliorare la velocità effettiva dello streaming on demand acquistando unità riservate di streaming on demand.

Per altre informazioni, vedere [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtri e manifesti dinamici
Con Servizi multimediali è possibile definire filtri per i propri asset. I filtri sono costituiti da regole lato server che aiutano i clienti a eseguire operazioni come riprodurre una determinata sezione di un video oppure specificare un sottoinsieme di rendering audio e video, in modo che possa essere gestito dal dispositivo del cliente (anziché tutti i rendering associati all'asset). Il filtro viene eseguito attraverso *manifesti dinamici* creati su richiesta del cliente per trasmettere un video in streaming in base a uno o più filtri specificati.

Per altre informazioni, vedere [Filtri e manifesti dinamici](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Localizzatori
Per poter fornire all'utente un URL da usare per scaricare o trasmettere in streaming i contenuti distribuiti, prima è necessario "pubblicare" la risorsa creando un localizzatore. Un localizzatore fornisce un punto di ingresso per accedere ai file contenuti in un asset. Servizi multimediali supporta due tipi di localizzatori:

* Localizzatori OnDemandOrigin. Questi consentono di trasmettere file multimediali in streaming (ad esempio MPEG DASH, HLS o Smooth Streaming) o eseguire il download progressivo dei file.
* localizzatori URL di firma di accesso condiviso. Consentono di scaricare file multimediali nel computer locale.

Per definire le autorizzazioni (ad esempio di lettura, scrittura ed elenco) e il periodo di tempo durante il quale un client può accedere a un determinato asset, viene usato un *criterio di accesso* . Si noti che l'autorizzazione di elenco (AccessPermissions.List) non deve essere usata per la creazione di un localizzatore OnDemandOrigin.

Per i localizzatori vengono definite date di scadenza. Il portale di Azure imposta una scadenza di 100 anni per i localizzatori.

> [!NOTE]
> Se si è usato il portale di Azure per creare i localizzatori prima del mese di marzo 2015, quei localizzatori sono stati impostati con una scadenza di due anni.
> 
> 

Per aggiornare la data di scadenza di un localizzatore, è possibile usare le API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Si noti che quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato.

I localizzatori non sono progettati per gestire il controllo dell'accesso per utente. È possibile assegnare a singoli utenti diritti di accesso diversi usando soluzioni DRM (Digital Rights Management). Per altre informazioni, vedere [Protezione dei file multimediali](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando si crea un localizzatore, è possibile che si verifichi un ritardo di 30 secondi a causa dei processi di archiviazione e propagazione necessari in Archiviazione di Azure.

## <a name="adaptive-streaming"></a>Streaming adattivo
Le tecnologie a bitrate adattivo consentono al lettore video di determinare le condizioni della rete e di effettuare una selezione tra più velocità in bit. Se le prestazioni della comunicazione di rete diminuiscono, il client può selezionare una velocità in bit inferiore in modo che la riproduzione del video possa continuare con una qualità leggermente ridotta. Se invece le prestazioni migliorano, il client può passare a una velocità in bit maggiore e fornire quindi una migliore qualità video. Servizi multimediali di Azure supporta le tecnologie a bitrate adattivo seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

Per poter fornire agli utenti URL di streaming, è prima necessario creare un localizzatore OnDemandOrigin. Creando il localizzatore è possibile ottenere il valore Path di base dell'asset che include i contenuti da trasmettere in streaming. Tuttavia, per trasmettere in streaming questi contenuti, è necessario modificare ulteriormente il percorso. Per creare un URL completo per il file manifesto di streaming, si deve concatenare il valore Path del localizzatore e il nome del file manifesto (nomefile.ism). Quindi aggiungere **/Manifest** e un formato appropriato (se necessario) al percorso di origine del localizzatore.

> [!NOTE]
> Lo streaming dei contenuti può essere eseguito anche tramite una connessione SSL. A questo scopo, verificare che gli URL di streaming inizino con HTTPS.
> 
> 

Lo streaming tramite SSL è possibile solo se l'endpoint di streaming da cui si inviano i contenuti è stato creato dopo il 10 settembre 2014. Se gli URL di streaming si basano sugli endpoint di streaming creati dopo il 10 settembre 2014, l'URL contiene "streaming.mediaservices.windows.net". Gli URL di streaming contenenti "origin.mediaservices.windows.net" (il vecchio formato) non supportano SSL. Se l'URL è nel vecchio formato e si desidera poter eseguire lo streaming tramite SSL, creare un nuovo endpoint di streaming. Usare gli URL basati sul nuovo endpoint di streaming per lo streaming dei contenuti tramite SSL.

## <a name="streaming-url-formats"></a>Formati degli URL di streaming URL:
### <a name="mpeg-dash-format"></a>Formato MPEG-DASH
{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato Apple HTTP Live Streaming (HLS) V4
{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato Apple HTTP Live Streaming (HLS) V3
{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato Apple HTTP Live Streaming (HLS) con il filtro solo audio
Per impostazione predefinita le tracce di solo audio sono incluse nel contenuto HLS del manifesto. È necessario per la certificazione di Apple store per reti cellulari. In questo caso, se un client non dispone di larghezza di banda sufficiente o connessa tramite una connessione 2G, si passa alla sola riproduzione di audio. Ciò aiuta a mantenere il flusso del contenuto senza memorizzarlo nel buffer, ma senza video. In alcuni scenari si potrebbe preferire il buffer del lettore di Windows solamente per l'audio. Se si desidera rimuovere la traccia solo audio, aggiungere **solo audio = false** per l'URL.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Per altre informazioni, vedere l'articolo sul [supporto della composizione di Dynamic Manifest e sulle funzioni aggiuntive di HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato Smooth Streaming
{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest

Esempio:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="a-idfmp4v20asmooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifesto Smooth Streaming 2.0 (manifesto legacy)
Per impostazione predefinita, il manifesto Smooth Streaming contiene il tag di ripetizione (r-tag). Alcuni lettori, tuttavia, non supportano gli r-tag. I client con questi lettori possono usare un formato che disabilita gli r-tag:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Download progressivo
Con il download progressivo è possibile avviare la riproduzione di file multimediali prima che il download dell'intero file sia stato completato. Non è possibile eseguire il download progressivo di file .ism* (ismv, isma, ismt o ismc).

Per eseguire il download progressivo di contenuti, usare un localizzatore di tipo OnDemandOrigin. Il seguente esempio mostra l'URL basato sul tipo di localizzatore OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Per eseguire il download progressivo è necessario decrittografare qualsiasi asset di archiviazione crittografato che si desideri trasmettere in streaming dal servizio di origine.

## <a name="download"></a>Scaricare
Per scaricare i contenuti in un dispositivo client, è necessario creare un localizzatore SAS. Il localizzatore SAS offre l'accesso al contenitore di archiviazione di Azure in cui si trova il file. Per compilare l'URL di download, è necessario incorporare il nome di file tra l'host e la firma SAS.

Il seguente esempio mostra l'URL basato sul localizzatore SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Si applicano le considerazioni seguenti:

* Per eseguire il download progressivo è necessario decrittografare qualsiasi asset di archiviazione crittografato che si desideri trasmettere in streaming dal servizio di origine.
* Un download ha esito negativo se non viene completato entro 12 ore.

## <a name="streaming-endpoints"></a>Endpoint di streaming
Un endpoint di streaming rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per la successiva distribuzione. Il flusso in uscita da un servizio endpoint di streaming può essere costituito da un flusso live o da un asset video on demand associato all'account di Servizi multimediali. È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità riservate di streaming. È consigliabile allocare almeno un'unità riservata per le applicazioni in un ambiente di produzione. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di un servizio multimediale](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problemi noti
### <a name="changes-to-smooth-streaming-manifest-version"></a>Modifiche alla versione del manifesto Smooth Streaming
Prima della versione del servizio di luglio 2016, quando gli asset prodotti da Media Encoder Standard, dal flusso di lavoro Premium del codificatore multimediale o dalla versione precedente di Azure Media Encoder venivano trasmessi con la creazione dinamica dei pacchetti, il manifesto Smooth Streaming restituito era conforme alla versione 2.0. Nella versione 2.0 le durate dei frammenti non usano i cosiddetti tag di ripetizione ("r"). ad esempio:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Nella versione del servizio di luglio 2016, il manifesto Smooth Streaming generato è conforme alla versione 2.2, con durate di frammento che usano i tag di ripetizione, ad esempio:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

È possibile che alcuni client Smooth Streaming legacy non supportino i tag di ripetizione e non carichino il manifesto. Per attenuare il problema è possibile usare il parametro del formato manifesto legacy, **(format=fmp4-v20)** o aggiornare il client alla versione più recente che supporta i tag di ripetizione. Per altre informazioni, vedere [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Argomenti correlati
[Aggiornamento di Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione](media-services-roll-storage-access-keys.md)




<!--HONumber=Dec16_HO2-->


