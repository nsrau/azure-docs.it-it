---
title: Panoramica della creazione dinamica dei pacchetti di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento offre una panoramica della creazione dinamica dei pacchetti in Servizi multimediali.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: a907e35e8e39b9dadd9106e7fd99063db28647a5
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869664"
---
# <a name="dynamic-packaging"></a>Creazione dinamica dei pacchetti

Servizi multimediali di Microsoft Azure può essere usato per distribuire molti formati di file di origine multimediali, formati di streaming multimediali e formati di protezione del contenuto a un'ampia gamma di tecnologie client, ad esempio iOS e Xbox. Questi client supportano tuttavia protocolli diversi. iOS, ad esempio, richiede un formato HTTP Live Streaming (HLS), mentre Xbox richiede Smooth Streaming. Se si dispone di un set di velocità in bit adattiva (più velocità in bit) MP4 file (ISO Base Media 14496-12) o un set di file Smooth Streaming a bitrate adattivo che si vuole renderli disponibili ai client che supportano HLS, MPEG DASH o Smooth Streaming, è possibile sfruttare  **Creazione dinamica dei pacchetti**. La creazione di pacchetti è indipendente dalla risoluzione video, sono supportati SD/HD/UHD - 4K.

In servizi multimediali, una [Endpoint di Streaming](streaming-endpoint-concept.md) rappresenta un dinamico (just-in-time) creazione di pacchetti e l'origine del servizio che può distribuire contenuti live e on demand direttamente a un'applicazione di lettore client, usando uno dei più comuni di streaming protocolli di supporti (HLS o DASH). Creazione dinamica dei pacchetti è una funzionalità che include standard su tutti **gli endpoint di Streaming** (Standard o Premium). 

Per poter sfruttare **creazione dinamica dei pacchetti**, è necessario avere un' **Asset** con un set di file MP4 a bitrate adattivo e streaming i file di configurazione necessari da Media Services creazione dinamica dei pacchetti. Un modo per ottenere i file consiste nel codificare il file mezzanine (di origine) con Servizi multimediali. Per rendere i video nell'Asset codificato disponibile ai client per la riproduzione, è necessario creare un **localizzatore di Streaming** e creare URL di streaming. Quindi, in base al formato specificato nel manifesto client di streaming (HLS, DASH o Smooth), il flusso sia ricevuto nel protocollo scelto.

Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client. 

In servizi multimediali di creazione dinamica dei pacchetti viene usato se si utilizza il flusso live o on demand. 

## <a name="common-on-demand-workflow"></a>Flusso di lavoro on demand comune

Di seguito è un comune del flusso di lavoro di streaming in cui viene usato creazione dinamica dei pacchetti di servizi multimediali.

1. Caricare un file di input (detto file in formato intermedio). Ad esempio, MP4, MOV o un file MXF (per l'elenco dei formati supportati, vedere [formati supportati da Media Encoder Standard](media-encoder-standard-formats.md).
2. Codificare il file in formato intermedio in set MP4 a velocità in bit adattiva H.264.
3. Pubblicare l'asset contenente il set MP4 a bitrate adattivo. Pubblicazione tramite la creazione di un **localizzatore di Streaming**.
4. Creare URL che usano formati diversi (HLS, Dash e Smooth Streaming). Il **Endpoint di Streaming** occuparsi di servire il manifesto corretto e le richieste per tutti questi formati diversi.

Il diagramma seguente illustra lo streaming on demand con flusso di lavoro di creazione dinamica dei pacchetti.

![Creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codifica con velocità in bit adattiva MP4s

Per informazioni sulle [come codificare un video con servizi multimediali](encoding-concept.md), vedere gli esempi seguenti:

* [Eseguire la codifica da un URL HTTPS usando i set di impostazioni predefinite](job-input-from-http-how-to.md)
* [Codificare un file locale usando i set di impostazioni predefinite](job-input-from-local-file-how-to.md)
* [Creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato](customize-encoder-presets-how-to.md)

Per un elenco dei codec e formati di Media Encoder Standard, vedere [codec e formati](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Lavoro di streaming live comuni

Ecco i passaggi del flusso di lavoro di uno streaming live:

1. Creare un [Evento live](live-events-outputs-concept.md).
1. Ottenere gli URL di inserimento e configurare un codificatore locale per usare l'URL per inviare il feed di contributo.
1. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
1. Creare una nuova **Asset**.
1. Creare un **LiveOutput** e usare il nome dell'asset creato.<br/>L'**output live** archivierà il flusso nell'**asset**.
1. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.<br/>Se si prevede di crittografare il contenuto, rivedere la [Panoramica della protezione del contenuto](content-protection-overview.md).
1. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare.
1. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
1. Creare URL che usano formati diversi (HLS, Dash e Smooth Streaming). Il **Endpoint di Streaming** occuparsi di servire il manifesto corretto e le richieste per tutti questi formati diversi.

Un evento Live può essere uno dei due tipi: codifica pass-through e in tempo reale. Per informazioni dettagliate sullo streaming live in servizi multimediali v3, vedere [panoramica dello streaming Live](live-streaming-overview.md).

Il diagramma seguente illustra lo streaming live con flusso di lavoro di creazione dinamica dei pacchetti.

![pass-through](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Protocolli di distribuzione

|Protocol|Esempio|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codec video supportati dalla creazione dinamica dei pacchetti

Creazione dinamica dei pacchetti supporta file MP4 contenenti video codificati [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC o AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 o hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codec audio supportati dalla creazione dinamica dei pacchetti

Creazione dinamica dei pacchetti supporta i file MP4, che contengono audio codificato con [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 o E-AC3), Atmos Dolby, o [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR DTS, DTS HD, HD DTS senza perdita di dati). Streaming di contenuti Atmos Dolby è supportato per gli standard come MP4 frammentato protocollo MPEG-DASH con formato di Streaming comuni (CSF) o formato di applicazione comuni Media (CMAF) e tramite HTTP Live Streaming (HLS) con CMAF.

> [!NOTE]
> La creazione dinamica dei pacchetti non supporta file contenenti audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) perché si tratta di un codec legacy codec.

## <a name="dynamic-encryption"></a>Crittografia dinamica

**Crittografia dinamica** consente di crittografare dinamicamente i contenuti live o on demand tramite AES-128 o uno qualsiasi dei sistemi tre principali diritti digitali (DRM) management: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. Per altre informazioni, vedere [crittografia dinamica](content-protection-overview.md).

## <a name="manifests"></a>Manifesti 
 
Servizi multimediali supporta i protocolli Smooth Streaming, HLS, MPEG DASH. Come parte della **creazione dinamica dei pacchetti**, i manifesti client streaming (Master HLS Playlist, DASH Media Presentation Description (MPD) e Smooth Streaming) vengono generati dinamicamente in base il selettore di formato nell'URL. Vedere i protocolli di recapito [in questa sezione](#delivery-protocols). 

Un file manifesto include metadati, ad esempio di streaming: tenere traccia di tipo (audio, video o testo), Tieni traccia del nome, ora di inizio e fine, a velocità in bit (qualità), lingue della traccia, la finestra di presentazione (finestra temporale scorrevole di durata fissa) e un codec video (FourCC). Indica inoltre al lettore come recuperare il frammento successivo fornendo informazioni sui successivi frammenti video riproducibili disponibili e il relativo percorso. I frammenti (o segmenti) sono i "blocchi" effettivi di un contenuto video.

### <a name="hls-master-playlist"></a>Playlist HLS Master

Di seguito è riportato un esempio di file manifesto HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>DASH Media Presentation Description (MPD)

Di seguito è riportato un esempio di un manifesto DASH:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Di seguito è riportato un esempio di un manifesto Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Manifesto dinamico

Applicazione di filtri dinamici viene utilizzato per controllare il numero di intervalli di tempo di presentazione che vengono inviati ai giocatori, i formati, velocità in bit e tiene traccia. Per altre informazioni, vedere [filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](developers-guide.md) supportati.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Caricare, codificare ed eseguire lo streaming dei video](stream-files-tutorial-with-api.md)

