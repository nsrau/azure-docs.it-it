---
title: Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali
description: Descrive le proprietà disponibili per gli eventi di Servizi multimediali con Griglia di eventi di Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146255"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali

Questo articolo illustra gli schemi e le proprietà degli eventi di Servizi multimediali.

Per un elenco di esercitazioni e script di esempio, vedere [Origine evento di Servizi multimediali](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Tipi di evento disponibili

Servizi multimediali genera i tipi di evento seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Stato delle modifiche del processo. |
| Microsoft.Media.LiveEventConnectionRejected | Il tentativo di connessione del codificatore viene rifiutato. |
| Microsoft.Media.LiveEventEncoderConnected | Il codificatore stabilisce una connessione con l'evento live. |
| Microsoft.Media.LiveEventEncoderDisconnected | Il codificatore si disconnette. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Il server dei contenuti multimediali elimina il blocco di dati perché è troppo tardi o presenta un timestamp sovrapposto. Il timestamp del nuovo blocco di dati è minore rispetto all'ora di fine del blocco di dati precedente. |
| Microsoft.Media.LiveEventIncomingStreamReceived | Il server dei contenuti multimediali riceve il primo blocco di dati per ogni traccia nel flusso o nella connessione. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Il server dei contenuti multimediali rileva che i flussi audio e video non sono sincronizzati. Usare come avviso per evitare che ciò abbia impatto sull'esperienza dell'utente. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Il server dei contenuti multimediali rileva che uno dei due flussi video provenienti dal codificatore esterno non è sincronizzato. Usare come avviso per evitare che ciò abbia impatto sull'esperienza dell'utente. |
| Microsoft.Media.LiveEventIngestHeartbeat | Pubblicato ogni 20 secondi per ogni traccia quando l'evento live è in esecuzione. Presenta un riepilogo dell'integrità dell'inserimento. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Il server dei contenuti multimediali rileva discontinuità nella traccia in ingresso. |

Esistono due categorie per gli eventi **live**: eventi a livello di flusso e a livello di traccia. 

Gli eventi a livello di flusso vengono generati per singolo flusso o connessione. Ogni evento ha un parametro `StreamId` che identifica la connessione o il flusso. Ogni flusso o connessione dispone di una o più tracce di tipi diversi. Ad esempio, una connessione da un codificatore può avere una traccia audio e quattro tracce video. I tipi di evento di flusso sono i seguenti:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Gli eventi a livello di traccia vengono generati per singola traccia. I tipi di evento di traccia sono i seguenti:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

L'esempio seguente illustra lo schema dell'evento **JobStateChange**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| previousState | stringa | Stato del processo prima dell'evento. |
| state | stringa | Nuovo stato del processo notificato in questo evento. Ad esempio, "Queued: il processo è in attesa di risorse" o "Scheduled: il processo sta per iniziare".|

Dove lo stato del processo può essere uno dei valori: *Queued*, *Scheduled*, *Processing*, *Finished*, *Error*, *Canceled*, *Canceling*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

L'esempio seguente illustra lo schema dell'evento **LiveEventConnectionRejected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| StreamId | stringa | Identificatore del flusso o della connessione. Il codificatore o il cliente è responsabile dell'aggiunta di questo ID nell'URL di inserimento. |  
| IngestUrl | stringa | URL di inserimento fornito dall'evento live. |  
| EncoderIp | stringa | Indirizzo IP del codificatore. |
| EncoderPort | stringa | Porta del codificatore da cui proviene il flusso. |
| ResultCode | stringa | Motivo per cui la connessione è stata rifiutata. I codici di risultato sono elencati nella tabella seguente. |

I codici di risultato sono:

| Codice di risultato | Descrizione |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | L'URL di inserimento non è corretto. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | L'indirizzo IP del codificatore non è presente nell'elenco di indirizzi IP consentiti configurato. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Il codificatore non invia i metadati relativi al flusso. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Il codec specificato non è supportato. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Il frammento è stato ricevuto prima della ricezione e dell'intestazione per il flusso. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Il numero di qualità specificato supera il limite massimo consentito. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | La velocità in bit aggregata supera il limite massimo consentito. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Il timestamp dal codificatore RTMP per FLVTag audio o video non è valido. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

L'esempio seguente illustra lo schema dell'evento **LiveEventEncoderConnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| StreamId | stringa | Identificatore del flusso o della connessione. Il codificatore o il cliente è responsabile della specifica di questo ID nell'URL di inserimento. |
| IngestUrl | stringa | URL di inserimento fornito dall'evento live. |
| EncoderIp | stringa | Indirizzo IP del codificatore. |
| EncoderPort | stringa | Porta del codificatore da cui proviene il flusso. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

L'esempio seguente illustra lo schema dell'evento **LiveEventEncoderDisconnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| StreamId | stringa | Identificatore del flusso o della connessione. Il codificatore o il cliente è responsabile dell'aggiunta di questo ID nell'URL di inserimento. |  
| IngestUrl | stringa | URL di inserimento fornito dall'evento live. |  
| EncoderIp | stringa | Indirizzo IP del codificatore. |
| EncoderPort | stringa | Porta del codificatore da cui proviene il flusso. |
| ResultCode | stringa | Motivo di disconnessione del codificatore. La causa può essere una disconnessione normale o causata da un errore. I codici di risultato sono elencati nella tabella seguente. |

I codici di risultato sono:

| Codice di risultato | Descrizione |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | La sessione RTMP è scaduta dopo un periodo di inattività per il limite di tempo consentito. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Il timestamp dal codificatore RTMP per FLVTag audio o video non è valido. |
| MPE_CAPACITY_LIMIT_REACHED | Il codificatore invia i dati troppo velocemente. |
| Codici di errore sconosciuti | Questi codici di errore possono variare da errori di memoria a voci duplicate nella mappa di hash. |

I codici di risultato della disconnessione normale sono:

| Codice di risultato | Descrizione |
| ----------- | ----------- |
| S_OK | Il codificatore si è disconnesso correttamente. |
| MPE_CLIENT_TERMINATED_SESSION | Codificatore disconnesso (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificatore disconnesso (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Il comando di ripristino del canale è stato ricevuto. |
| MPI_REST_API_CHANNEL_STOP | Il comando di arresto del canale è stato ricevuto. |
| MPI_REST_API_CHANNEL_STOP | Canale in fase di manutenzione. |
| MPI_STREAM_HIT_EOF | Flusso EOF inviato dal codificatore. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

L'esempio seguente illustra lo schema dell'evento **LiveEventIncomingDataChunkDropped**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| TrackType | stringa | Tipo di traccia (audio/video). |
| TrackName | stringa | Nome della traccia. |
| Bitrate | numero intero | Velocità in bit della traccia. |
| Timestamp | stringa | Timestamp del blocco di dati eliminato. |
| Timescale | stringa | Scala cronologica del timestamp. |
| ResultCode | stringa | Motivo dell'eliminazione del blocco di dati. **FragmentDrop_OverlapTimestamp** o **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

L'esempio seguente illustra lo schema dell'evento **LiveEventIncomingStreamReceived**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| TrackType | stringa | Tipo di traccia (audio/video). |
| TrackName | stringa | Nome della traccia (fornito dal codificatore o, in caso di RTMP, generato dal server nel formato *TrackType_Bitrate*). |
| Bitrate | numero intero | Velocità in bit della traccia. |
| IngestUrl | stringa | URL di inserimento fornito dall'evento live. |
| EncoderIp | stringa  | Indirizzo IP del codificatore. |
| EncoderPort | stringa | Porta del codificatore da cui proviene il flusso. |
| Timestamp | stringa | Primo timestamp del blocco di dati ricevuto. |
| Timescale | stringa | Scala cronologica in cui è rappresentato il timestamp. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

L'esempio seguente illustra lo schema dell'evento **LiveEventIncomingStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| MinLastTimestamp | stringa | Numero minimo dei timestamp più recenti tra le tracce (audio o video). |
| TypeOfTrackWithMinLastTimestamp | stringa | Tipo di traccia (audio o video) con numero minimo di timestamp più recenti. |
| MaxLastTimestamp | stringa | Numero massimo di tutti i timestamp tra le tracce (audio o video). |
| TypeOfTrackWithMaxLastTimestamp | stringa | Tipo di traccia (audio o video) con numero massimo di timestamp più recenti. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

L'esempio seguente illustra lo schema dell'evento **LiveEventIncomingVideoStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| FirstTimestamp | stringa | Timestamp ricevuto per uno dei livelli di traccia/qualità del tipo di video. |
| FirstDuration | stringa | Durata del blocco di dati con il primo timestamp. |
| SecondTimestamp | stringa  | Timestamp ricevuto per altri livelli di traccia/qualità del tipo di video. |
| SecondDuration | stringa | Durata del blocco di dati con il secondo timestamp. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

L'esempio seguente illustra lo schema dell'evento **LiveEventIngestHeartbeat**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| TrackType | stringa | Tipo di traccia (audio/video). |
| TrackName | stringa | Nome della traccia (fornito dal codificatore o, in caso di RTMP, generato dal server nel formato *TrackType_Bitrate*). |
| Bitrate | numero intero | Velocità in bit della traccia. |
| IncomingBitrate | numero intero | Velocità in bit calcolata in base ai blocchi di dati provenienti dal codificatore. |
| LastTimestamp | stringa | Timestamp più recente ricevuto per una traccia negli ultimi 20 secondi. |
| Timescale | stringa | Scala cronologica in cui sono espressi i timestamp. |
| OverlapCount | numero intero | Numero di blocchi di dati sovrapposti al timestamp negli ultimi 20 secondi. |
| DiscontinuityCount | numero intero | Numero di discontinuità osservate negli ultimi 20 secondi. |
| NonIncreasingCount | numero intero | Numero di blocchi di dati con timestamp ricevuti in passato negli ultimi 20 secondi. |
| UnexpectedBitrate | bool | Se la velocità in bit prevista e quella effettiva superano il limite consentito negli ultimi 20 secondi. È true se e solo se i valori sono: IncomingBitrate >= 2* bitrate O IncomingBitrate <= bitrate/2 O IncomingBitrate = 0. |
| State | stringa | Stato dell'evento live. |
| Healthy | bool | Indica se l'inserimento è integro in base ai conteggi e ai flag. Healthy è true se i valori sono: OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

L'esempio seguente illustra lo schema dell'evento **LiveEventTrackDiscontinuityDetected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| TrackType | stringa | Tipo di traccia (audio/video). |
| TrackName | stringa | Nome della traccia (fornito dal codificatore o, in caso di RTMP, generato dal server nel formato *TrackType_Bitrate*). |
| Bitrate | numero intero | Velocità in bit della traccia. |
| PreviousTimestamp | stringa | Timestamp del frammento precedente. |
| NewTimestamp | stringa | Timestamp del frammento corrente. |
| DiscontinuityGap | stringa | Spazio tra i due timestamp precedenti. |
| Timescale | stringa | Scala cronologica in cui sono rappresentati i timestamp e lo spazio di discontinuità. |

## <a name="common-event-properties"></a>Proprietà dell'evento comune

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| topic | stringa | Argomento EventGrid. Questa proprietà include l'ID risorsa per l'account di Servizi multimediali. |
| subject | stringa | Il percorso della risorsa per il canale di Servizi multimediali nell'account di Servizi multimediali. La concatenazione dell'argomento e del soggetto fornisce l'ID risorsa per il processo. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. Ad esempio, "Microsoft.Media.JobStateChange". |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento di Servizi multimediali. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

[Eseguire la registrazione agli eventi di modifica dello stato del processo](job-state-events-cli-how-to.md)