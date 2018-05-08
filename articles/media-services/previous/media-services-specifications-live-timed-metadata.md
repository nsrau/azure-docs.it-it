---
title: Servizi multimediali di Azure - Segnalazione dei metadati programmati in streaming live | Microsoft Docs
description: Questa specifica descrive due modalità supportate da Servizi multimediali per la segnalazione dei metadati programmati in streaming live. Sono inclusi il supporto di segnali di metadati programmati generici e la segnalazione SCTE-35 per l'inserimento di giunzioni annuncio.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 2e736872dc3e471af7c5b3f758516910a02067fe
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Segnalazione dei metadati programmati in streaming live


## <a name="1-introduction"></a>1 Introduzione 
Per facilitare l'inserimento di annunci o eventi personalizzati in un lettore client, spesso le emittenti usano i metadati programmati incorporati nel video. Per consentire questi scenari, Servizi multimediali supporta il trasporto dei metadati programmati insieme ai file multimediali, dal punto di inserimento del canale di streaming live all'applicazione client.
Questa specifica descrive due modalità supportate da Servizi multimediali per i segnali dei metadati programmati in streaming live:

1. Segnalazione [SCTE-35] che segue le procedure consigliate descritte da [SCTE-67]

2. Una modalità di segnalazione dei metadati generica, per messaggi che non sono [SCTE-35]

### <a name="12-conformance-notation"></a>1.2 Nota di conformità
Le parole chiave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" e "OPTIONAL" in questo documento vanno interpretate come descritto nella specifica RFC 2119

### <a name="13-terms-used"></a>1.3 Termini usati

| Termine              | Definizione                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ora di presentazione | L'ora in cui un evento viene presentato a un visualizzatore. L'ora rappresenta il momento nella scala cronologica dei supporti in cui un visualizzatore vedrà l'evento. Ad esempio, l'ora per la presentazione di un messaggio di comando SCTE-35 splice_info() è splice_time(). |
| Ora di arrivo      | L'ora di arrivo di un messaggio evento. L'ora è in genere diversa dall'ora di presentazione dell'evento, poiché i messaggi evento vengono inviati prima dell'ora di presentazione dell'evento.                                     |
| Traccia di tipo sparse      | traccia multimediale che non è continua ed è sincronizzata temporalmente con una traccia padre o di controllo.                                                                                                                                    |
| Origine            | Il servizio di streaming multimediale di Azure                                                                                                                                                                                                |
| Sink di canale      | Il servizio di streaming live multimediale di Azure                                                                                                                                                                                           |
| HLS               | Protocollo Apple HTTP Live Streaming                                                                                                                                                                                               |
| DASH              | Dynamic Adaptive Streaming Over HTTP                                                                                                                                                                                             |
| Smooth            | Smooth Streaming Protocol                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG-2 Transport Stream                                                                                                                                                                                                         |
| RTMP              | Real-Time Multimedia Protocol                                                                                                                                                                                                    |
| uimsbf            | Intero senza segno, a partire dal numero di bit più significativo.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 Inserimento di metadati programmati
## <a name="21-rtmp-ingest"></a>2.1 Inserimento in RTMP
RTMP supporta l'invio di segnali di metadati programmati come messaggi di segnale AMF incorporati nel flusso RTMP. I messaggi di segnale possono essere inviati alcuni minuti prima dell'evento effettivo oppure deve essere eseguito l'inserimento della giunzione annuncio. Per supportare questo scenario, il tempo effettivo della giunzione o del segmento viene inviato nel messaggio di segnale. Per altre informazioni, vedere [AMF0].

La tabella seguente illustra il formato del payload dei messaggi AMF che verranno inseriti da Servizi multimediali.

Il nome del messaggio AMF può essere utilizzato per distinguere più flussi di eventi dello stesso tipo.  Per i messaggi [SCTE-35], il nome del messaggio AMF deve (MUST) essere "onAdCue" come consigliato in [SCTE-67].  Tutti i campi non elencati di seguito devono (MUST) essere ignorati, in modo che l'innovazione di questa progettazione non venga bloccata in futuro.

### <a name="signal-syntax"></a>Sintassi del segnale
Per la modalità semplice RTMP, Servizi multimediali supporta un singolo messaggio di segnale AMF chiamato "onAdCue" con il formato seguente:

### <a name="simple-mode"></a>Modalità semplice

| Nome campo | Tipo di campo | Obbligatorio? | Descrizioni                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Obbligatoria | Il messaggio evento.  Deve essere "SpliceOut" per designare una giunzione modalità semplice.                                              |
| id         | string     | Obbligatoria | Un identificatore univoco che descrive la giunzione o il segmento. Identifica questa istanza del messaggio                            |
| duration   | Number     | Obbligatoria | La durata della giunzione. Le unità sono espresse in secondi frazionari.                                                                |
| elapsed    | Number     | Facoltativo | Se il segnale dell'annuncio viene ripetuto per supportare l'ottimizzazione, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. Quando si usa la modalità semplice, questo valore non deve superare la durata originale della giunzione.                                                  |
| time       | Number     | Obbligatoria | Deve essere l'ora della giunzione, nell'ora di presentazione. Le unità sono espresse in secondi frazionari.                                     |

---------------------------

### <a name="scte-35-mode"></a>Modalità SCTE-35

| Nome campo | Tipo di campo | Obbligatorio? | Descrizioni                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Obbligatoria | Il messaggio evento.  Per i messaggi [SCTE-35], deve (MUST) essere base64 (IETF RFC 4648) binario codificato affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-67].                                              |
| type       | string     | Obbligatoria | Un URN o URL che identifica lo schema del messaggio; ad esempio, "urn:example:signaling:1.0".  Per i messaggi [SCTE-35], deve (MUST) essere "urn:scte:scte35:2013a:bin" affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-67].  |
| id         | string     | Obbligatoria | Un identificatore univoco che descrive la giunzione o il segmento. Identifica l'istanza del messaggio.  I messaggi semanticamente equivalenti devono avere lo stesso valore.|
| duration   | Number     | Obbligatoria | La durata dell'evento o del segmento di giunzione, se conosciuto. Se sconosciuto, il valore dovrebbe essere 0.                                                                 |
| elapsed    | Number     | Facoltativo | Se il segnale dell'annuncio [SCTE-35] viene ripetuto per ottimizzarlo, questo campo deve indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. In modalità [SCTE-35], questo valore può superare la durata originale specificata della giunzione o del segmento.                                                  |
| time       | Number     | Obbligatoria | L'ora di presentazione dell'evento o della giunzione annuncio.  L'ora di presentazione e la durata dovrebbero (SHOULD) essere allineati ai punti di accesso al flusso (SAP) di tipo 1 o 2, come definito nell'Allegato I di [ISO-14496-12]. Per i dati in uscita HLS, l'ora e la durata dovrebbero (SHOULD) essere allineati con i limiti del segmento. L'ora di presentazione e la durata di diversi messaggi di eventi all'interno dello stesso flusso di eventi non devono (MUST NOT) sovrapporsi. Le unità sono espresse in secondi frazionari.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 Annullamento e aggiornamenti

I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali. L'ora di presentazione e l'ID identificano in modo univoco l'evento e l'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. L'evento aggiornato sostituisce eventuali messaggi ricevuti in precedenza. Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Inserimento MP4 frammentato (Smooth Streaming)
Fare riferimento a [LIVE-FMP4] per i requisiti per l'inserimento live di un flusso. Le sezioni seguenti descrivono i dettagli relativi all'inserimento dei metadati di presentazione programmati.  I metadati di presentazione programmati vengono inseriti come traccia di tipo sparse, che viene definita nelle caselle Live Server Manifest Box (vedere MS-SSTR) e Movie Box ('moov').  Ogni frammento di tipo sparse è costituito dalle caselle Movie Fragment Box ('moof') e Media Data Box ('mdat'), in cui la casella 'mdat' è il messaggio binario.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Casella Live Server Manifest Box
La traccia di tipo sparse deve essere dichiarata nella casella Live Server Manifest con la voce \<textstream\> e devono (MUST) essere impostati gli attributi seguenti:

| **Nome attributo** | **Tipo di campo** | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Obbligatoria      | DEVE essere "0", che indica una traccia con bitrate variabile, sconosciuto.                                                                                                                                                                                                 |
| parentTrackName    | string         | Obbligatoria      | Deve (MUST) essere il nome della traccia padre, alla cui scala temporale sono allineati i codici temporali della traccia di tipo sparse. La traccia padre non può essere una traccia di tipo sparse.                                                                                                                    |
| manifestOutput     | boolean        | Obbligatoria      | Deve (MUST) essere "true", per indicare che la traccia di tipo sparse verrà incorporata nel manifesto client Smooth.                                                                                                                                                               |
| Subtype            | string         | Obbligatoria      | Deve (MUST) essere il codice di quattro caratteri "DATA".                                                                                                                                                                                                                         |
| Schema             | string         | Obbligatoria      | Un URN o URL che identifica lo schema del messaggio; ad esempio, "urn:example:signaling:1.0". Per i messaggi [SCTE-35], deve (MUST) essere "urn:scte:scte35:2013a:bin" affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-67]. |
| trackName          | string         | Obbligatoria      | Deve (MUST) essere il nome della traccia di tipo sparse. Il trackName può essere usato per distinguere più flussi di eventi con lo stesso schema. Ogni flusso di eventi univoco deve avere un nome traccia univoco.                                                                           |
| timescale          | Number         | Facoltativo      | Deve (MUST) essere la scala cronologica della traccia padre.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Casella Movie Box

La casella Movie Box ('moov') segue la casella Live Server Manifest Box come parte dell'intestazione del flusso per una traccia di tipo sparse.

La casella 'moov' dovrebbe (SHOULD) contenere una casella **TrackHeaderBox ('tkhd')** come definito in [ISO-14496-12] con i vincoli seguenti:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | Intero senza segno a 64 bit | Obbligatoria      | Dovrebbe (SHOULD ) essere 0, poiché la casella della traccia contiene zero campioni e la durata totale dei campioni nella casella della traccia è 0. |
-------------------------------------

La casella 'moov' dovrebbe (SHOULD) contenere un **HandlerBox ('hdlr')** come definito in [ISO-14496-12] con i vincoli seguenti:

| **Nome campo** | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | Intero senza segno a 32 bit | Obbligatoria      | Dovrebbe (SHOULD) essere 'meta'. |
-------------------------------------

La casella 'stsd' dovrebbe (SHOULD) contenere una casella MetaDataSampleEntry con un nome di codifica, come definito in [ISO-14496-12].  Ad esempio, per i messaggi SCTE-35 il nome di codifica dovrebbe (SHOULD) essere 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caselle Movie Fragment Box e Media Data Box

I frammenti di tracce di tipo sparse sono costituiti dalle caselle Movie Fragment Box ('moof') e Media Data Box ('mdat').

La casella MovieFragmentBox ('moof') deve (MUST) contenere una casella **TrackFragmentExtendedHeaderBox (‘uuid’)** come definito in [MS-SSTR] con i campi seguenti:

| **Nome campo**         | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | Intero senza segno a 64 bit | Obbligatoria      | Deve (MUST) essere l'ora di arrivo dell'evento. Questo valore consente di allineare il messaggio con la traccia padre.   |
| fragment_duration      | Intero senza segno a 64 bit | Obbligatoria      | Deve (MUST) essere la durata dell'evento. La durata può essere zero per indicare che la durata è sconosciuta. |

------------------------------------


La casella MediaDataBox ('mdat') deve (MUST) avere il formato seguente:

| **Nome campo**          | **Tipo di campo**                   | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Determina il formato dei contenuti della casella 'mdat'. Le versioni non riconosciute verranno ignorate. Al momento, l'unica versione supportata è 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore, vale a dire che è sufficiente l'elaborazione di una qualsiasi casella di messaggio evento con lo stesso ID.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Intero senza segno a 32 bit (uimsbf) | Obbligatoria      | La somma di fragment_absolute_time, specificata nella casella TrackFragmentExtendedHeaderBox e di presentation_time_delta deve (MUST) essere l'ora di presentazione dell'evento. L'ora di presentazione e la durata dovrebbero (SHOULD) essere allineati ai punti di accesso al flusso (SAP) di tipo 1 o 2, come definito nell'Allegato I di [ISO-14496-12]. Per i dati in uscita HLS, l'ora e la durata dovrebbero (SHOULD) essere allineati con i limiti del segmento. L'ora di presentazione e la durata di diversi messaggi di eventi all'interno dello stesso flusso di eventi non devono (MUST NOT) sovrapporsi. |
| Message                 | matrice di byte                       | Obbligatoria      | Il messaggio evento. Per i messaggi [SCTE-35], il messaggio è splice_info_section() binario, sebbene [SCTE-67] consigli diversamente. Per i messaggi [SCTE-35], deve (MUST) essere splice_info_section() affinché i messaggi vengano inviati ai client HLS, Smooth e Dash in conformità con [SCTE-67]. Per i messaggi [SCTE-35], splice_info_section() binario è il payload della casella 'mdat' e NON è codificato base64.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 Annullamento e aggiornamenti
I messaggi possono essere annullati o aggiornati inviando più messaggi con ora di presentazione e ID uguali.  Il tempo di presentazione e l'ID identificano in modo univoco l'evento. L'ultimo messaggio ricevuto per un'ora di presentazione specifica che soddisfa i vincoli preroll è il messaggio su cui viene eseguita un'operazione. Il messaggio aggiornato sostituisce eventuali messaggi ricevuti in precedenza.  Il vincolo di preroll è 4 secondi. Sui messaggi ricevuti almeno quattro secondi prima dell'ora di presentazione verranno eseguite le operazioni. 


## <a name="3-timed-metadata-delivery"></a>3 Recapito di metadati programmati

I dati del flusso di eventi sono opachi per Servizi multimediali. Servizi multimediali si limita a passare tre tipi di informazioni tra l'endpoint di inserimento e l'endpoint client. Le proprietà seguenti vengono recapitate al client, in conformità con [SCTE-67] e/o protocollo di streaming del client:

1.  Schema: un URN o URL che identifica lo schema del messaggio.

2.  Ora di presentazione: l'ora di presentazione dell'evento sulla scala cronologica dei file multimediali.

3.  Durata: la durata dell'evento.

4.  ID: un identificatore univoco facoltativo per l'evento.

5.  Messaggio: i dati dell'evento.


## <a name="31-smooth-streaming-delivery"></a>3.1 Recapito Smooth Streaming

Fare riferimento ai dettagli della gestione delle tracce di tipo sparse in [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Esempio di manifesto client Smooth
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Recapito Apple HLS
I metadati programmati per Apple HTTP Live Streaming (HLS) possono essere incorporati nella playlist del segmento in un tag M3U personalizzato.  Il livello dell'applicazione può analizzare la playlist M3U ed elaborare i tag M3U. Servizi multimediali di Azure incorporerà i metadati programmati nel tag EXT-X-CUE definito in [HLS].  Il tag EXT-X-CUE è attualmente usato da DynaMux per i messaggi di tipo ADI3.  Per supportare i messaggi SCTE-35 e non SCTE-35, impostare gli attributi del tag EXT-X-CUE come definito di seguito:

| **Nome attributo** | **Tipo**                      | **Obbligatorio?**                             | **Descrizione**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | stringa tra virgolette                 | Obbligatoria                                  | Il messaggio evento codificato come stringa base64 come descritto in [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). Per i messaggi [i segnali SCTE-35], è splice_info_section() codificata base64.                                                                                                |
| TIPO               | stringa tra virgolette                 | Obbligatoria                                  | Un URN o URL che identifica lo schema del messaggio; ad esempio, "urn:example:signaling:1.0". Per i messaggi [i segnali SCTE-35], il tipo assume il valore speciale "scte35".                                                                                                                                |
| ID                 | stringa tra virgolette                 | Obbligatoria                                  | Un identificatore univoco dell'evento. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                                                                          |
| DURATION           | numero a virgola mobile decimale | Obbligatoria                                  | Durata dell'evento. Se sconosciuto, il valore dovrebbe essere 0. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                           |
| ELAPSED            | numero a virgola mobile decimale | Facoltativo, ma necessari per la finestra temporale scorrevole | Se il segnale dell'annuncio viene ripetuto per supportare una finestra temporale scorrevole di presentazione, questo campo deve (MUST) indicare il tempo di presentazione trascorso dall'inizio della giunzione. Le unità sono espresse in secondi frazionari. Questo valore può superare la durata originale specificata della giunzione o del segmento. |
| TIME               | numero a virgola mobile decimale | Obbligatoria                                  | L'ora di presentazione dell'evento. Le unità sono espresse in secondi frazionari.                                                                                                                                                                                                                    |


Il livello dell'applicazione del lettore HLS userà l'attributo TYPE per identificare il formato del messaggio, decodificare il messaggio, applicare le conversioni temporali necessarie ed elaborare l'evento.  Gli eventi sono sincronizzati temporalmente nella playlist del segmento della traccia padre, in base al timestamp dell'evento.  Essi vengono inserite prima del segmento più vicino (tag #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Esempi di playlist del segmento HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>Gestione dei messaggi HLS

Gli eventi vengono segnalati nella playlist del segmento di ogni traccia video e audio. La posizione del tag EXT-X-CUE deve (MUST) sempre essere immediatamente prima del primo segmento HLS (per l'uscita giunzione o l'inizio segmento) o subito dopo l'ultimo segmento HLS (per l'ingresso giunzione o la fine segmento) a cui fanno riferimento i relativi attributi ORA e DURATA, come richiesto da [ HLS].

Se una finestra temporale scorrevole di presentazione è abilitata, il tag EXT-X-CUE deve (MUST) essere ripetuto con una frequenza tale che la giunzione o il segmento sia sempre completamente descritto nella playlist del segmento e deve (MUST) essere usato l'attributo ELAPSED per indicare il tempo di attività della giunzione o del segmento, come richiesto da [HLS].

Se una finestra temporale scorrevole di presentazione è abilitata, i tag EXT-X-CUE vengono rimossi dalla playlist del segmento quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="33--dash-delivery"></a>3.3 Recapito DASH
[DASH] offre tre modi per segnalare gli eventi:

1.  Eventi segnalati in MPD
2.  Gli eventi segnalati in banda nella rappresentazione (tramite la finestra di messaggio evento ('emsg')
3.  Una combinazione di 1 e 2

Gli eventi segnalati in MPD sono utili per lo streaming VOD perché i client hanno l'accesso immediato a tutti gli eventi subito dopo aver scaricato MPD. La soluzione in banda è utile per lo streaming live perché i client non devono scaricare nuovamente MPD. Per la segmentazione basata sul tempo, il client determina l'URL per il segmento successivo aggiungendo la durata e il timestamp del segmento corrente. Se la richiesta ha esito negativo, il client presuppone una discontinuità e scarica MPD ma, in caso contrario, continua lo streaming senza scaricare MPD.

Servizi multimediali di Azure eseguirà le segnalazioni in MPD e in banda con la casella messaggio evento.

#### <a name="mpd-signaling"></a>Segnalazione MPD

Gli eventi verranno segnalati in MPD usando l'elemento EventStream, che viene visualizzato nell'elemento Periodo.

L'elemento EventStream ha gli attributi seguenti:

| **Nome attributo** | **Tipo**                | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | stringa                  | Obbligatoria      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. I valori dovrebbero (SHOULD) essere un URN o URL che identifica lo schema del messaggio; ad esempio, "urn:example:signaling:1.0".                                                                |
| value              | stringa                  | Facoltativo      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per poter distinguere più flussi di eventi con lo stesso schema, il valore deve (MUST) essere impostato sul nome del flusso di eventi (trackName per l'inserimento Smooth o nome del messaggio AMF per l' inserimento RTMP). |
| Scala cronologica          | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, in tick al secondo, per i campi ore e durata nella casella 'emsg'.                                                                                                                                                                                                       |


Zero o più elementi evento sono contenuti nell'elemento EventStream e hanno gli attributi seguenti:

| **Nome attributo**  | **Tipo**                | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | Intero senza segno a 64 bit | Facoltativo      | Deve (MUST) essere l'ora di presentazione dell'evento relativo all'inizio del periodo. L'ora e la durata della presentazione dovrebbero (SHOULD) essere allineate ai punti di accesso al flusso (SAP, Stream Access Points) di tipo 1 o 2,come definito nell'Allegato I di [ISO-14496-12]. |
| duration            | Intero senza segno a 32 bit | Facoltativo      | Durata dell'evento. Deve (MUST) essere omessa se la durata è sconosciuta.                                                                                                                                                 |
| id                  | Intero senza segno a 32 bit | Facoltativo      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.             |
| Valore elemento evento | stringa                  | Obbligatoria      | Il messaggio evento come stringa base64 come descritto in [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Sintassi XML ed esempio di segnalazione (MPD) manifesto DASH

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>presentationTime è l'ora di presentazione dell'evento, non l'ora di arrivo del messaggio.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 Segnalazione casella messaggio evento in banda
Un flusso di eventi in banda richiede che MPD abbia un elemento InbandEventStream a livello del set di adattamento.  Questo elemento ha un attributo schemeIdUri obbligatorio e un attributo scala temporale facoltativo, che compare anche nella casella messaggio evento ('emsg').  Le caselle messaggio evento con identificatori di schema non definite in MPD non devono essere presenti. Se un client DASH rileva una casella messaggio evento con uno schema non definito in MPD, il client deve ignorarlo.
La casella messaggio evento ('emsg') fornisce la segnalazione per eventi generici correlati all'ora di presentazione dei file multimediali. Se presente, un'eventuale casella 'emsg' dovrà essere posizionata prima di un'eventuale 'moof'.

### <a name="dash-event-message-box-emsg"></a>Casella DASH Event Message Box 'emsg'
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

I campi della casella DASHEventMessageBox sono definiti di seguito:

| **Nome campo**          | **Tipo di campo**          | **Obbligatorio?** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | stringa                  | Obbligatoria      | Identifica lo schema del messaggio. Lo schema è impostato sul valore dell'attributo Schema nella casella Live Server Manifest. I valori dovrebbero (SHOULD) essere un URN o URL che identifica lo schema del messaggio; ad esempio, "urn:example:signaling:1.0". Per i messaggi [SCTE-35], assume il valore speciale "urn:scte:scte35:2013a:bin", sebbene [SCTE-67] consigli diversamente. |
| Valore                   | stringa                  | Obbligatoria      | Un valore di stringa aggiuntivo usato dai proprietari dello schema per personalizzare la semantica del messaggio. Per poter distinguere più flussi di eventi con lo stesso schema, il valore verrà impostato sul nome del flusso di eventi (trackName per l'inserimento Smooth o nome del messaggio AMF per l' inserimento RTMP).                                                                  |
| Scala cronologica               | Intero senza segno a 32 bit | Obbligatoria      | La scala cronologica, in tick al secondo, per i campi ore e durata nella casella 'emsg'.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | Intero senza segno a 32 bit | Obbligatoria      | Delta del tempo della presentazione multimediale dell'evento è l'ora di presentazione meno recente in questo segmento. L'ora e la durata della presentazione dovrebbero (SHOULD) essere allineate ai punti di accesso al flusso (SAP, Stream Access Points) di tipo 1 o 2,come definito nell'Allegato I di [ISO-14496-12].                                                                                            |
| event_duration          | Intero senza segno a 32 bit | Obbligatoria      | La durata dell'evento o 0xFFFFFFFF per indicare una durata sconosciuta.                                                                                                                                                                                                                                                                                          |
| ID                      | Intero senza segno a 32 bit | Obbligatoria      | Identifica l'istanza del messaggio. I messaggi semanticamente equivalenti devono avere lo stesso valore. Se al momento dell'inserimento del messaggio non viene specificato l'ID, Servizi multimediali di Azure genererà un ID univoco.                                                                                                                                                    |
| Message_data            | matrice di byte              | Obbligatoria      | Il messaggio evento. Per i messaggi [SCTE-35], i dati del messaggio sono splice_info_section() binario, sebbene [SCTE-67] consigli diversamente.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 Gestione dei messaggi DASH

Gli eventi vengono segnalati in banda, nella casella 'emsg' per le tracce video e audio.  La segnalazione si verifica per tutte le richieste di segmenti per cui presentation_time_delta è 15 secondi o inferiore. Se una finestra temporale scorrevole di presentazione è abilitata, i messaggi eventi vengono rimossi da MPD quando la somma di ora e durata del messaggio evento è inferiore alla durata dei dati dei file multimediali nel manifesto.  In altre parole, i messaggi eventi vengono rimossi dal manifesto quando il tempo file multimediali a cui fanno riferimento non rientra nella finestra temporale scorrevole di presentazione.

## <a name="4-scte-35-ingest"></a>4 Inserimento SCTE-35

I messaggi [SCTE-35] vengono inseriti in formato binario usando lo schema **“urn:scte:scte35:2013a:bin”** per l'inserimento Smooth e il tipo **“scte35”** per l'inserimento RTMP. Per semplificare la conversione dell'intervallo [SCTE-35], che si basa sui timestamp ora di presentazione (PTS) del flusso di trasporto MPEG-2, il mapping tra PTS (pts_time + pts_adjustment of the splice_time()) e la scala cronologica dei file multimediali viene fornito dall'ora di presentazione dell'evento (il campo fragment_absolute_time per l'inserimento Smooth e il campo ora per l'inserimento RTMP). Il mapping è necessario perché il valore PTS a 33 bit viene aggiornato ogni 26,5 ore circa.

Per l'inserimento Smooth Streaming, la casella Media Data Box ('mdat') deve (MUST) contenere **splice_info_section()** definito nella Tabella 8-1 di [SCTE-35]. Per l'inserimento RTMP, l'attributo segnale del messaggio AMF è impostato su base64encoded **splice_info_section()**. Quando i messaggi presentano il formato descritto in precedenza, vengono inviati ai client Dash, HLS e Smooth in conformità con [SCTE-67].


## <a name="5-references"></a>5 Riferimenti

**[SCTE-35]** ANSI/SCTE 35 2013a – Digital Program Insertion Cueing Message for Cable, 2013a

**[SCTE-67]** ANSI/SCTE 67 2014 –Recommended Practice for SCTE 35: Digital Program Insertion Cueing Message for Cable

**[DASH]** ISO/IEC 23009-1 2014 – Information technology – Dynamic adaptive streaming over HTTP (DASH) – Part 1: Media Presentation description and segment formats, 2nd edition

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, October 14, 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [“Microsoft Smooth Streaming Protocol”, May 15, 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** ["Action Message Format AMF0"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]** [Azure Media Services Fragmented MP4 Live Ingest Specification](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Part 12 ISO base media file format, Fourth Edition 2012-07-15.

**[RTMP]** [“Adobe’s Real-Time Messaging Protocol”, December 21, 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Passaggi successivi
Visualizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
