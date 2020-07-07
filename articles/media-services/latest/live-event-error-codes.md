---
title: Codici di errore dell'evento live di servizi multimediali di Azure | Microsoft Docs
description: Questo articolo elenca i codici di errore dell'evento Live.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/4/2020
ms.author: inhenkel
ms.openlocfilehash: e039a240465478e86ce816890219e5dfaa07dfbf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82791052"
---
# <a name="media-services-live-event-error-codes"></a>Codici di errore dell'evento live di servizi multimediali

Nelle tabelle di questa sezione sono elencati i codici di errore dell' [evento Live](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Quando si esegue la sottoscrizione agli eventi di [griglia di eventi](https://docs.microsoft.com/azure/event-grid/) per un evento Live, è possibile che venga visualizzato uno degli errori seguenti dell'evento [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Codice di risultato | Descrizione |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | L'URL di inserimento non è corretto. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | L'indirizzo IP del codificatore non è presente nell'elenco di indirizzi IP consentiti configurato. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Il codificatore RTMP non ha inviato il comando setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Il codec specificato non è supportato. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Le informazioni sulla descrizione dei supporti non sono state ricevute prima del recapito dei dati multimediali effettivi.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Il numero di qualità per il tipo audio o video supera il limite massimo consentito.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |La velocità in bit totale in ingresso in un evento Live o un servizio canale ha superato il limite massimo consentito.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Il timestamp dal codificatore RTMP per FLVTag audio o video non è valido. |
| MPE_INGEST_FRAMERATE_EXCEEDED | I flussi inseriti dal codificatore in ingresso con framerate hanno superato il numero massimo di 30 fps per la codifica di eventi/canali live.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | I flussi inseriti dal codificatore in ingresso hanno superato le risoluzioni consentite seguenti: 1920X1088 per la codifica di eventi/canali live e 4096 x 2160 per gli eventi/canali live pass-through.|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | L'evento Live ha ricevuto una grande quantità di dati audio contemporaneamente o una grande quantità di dati video senza fotogrammi chiave. Il codificatore è stato disconnesso per offrire la possibilità di riprovare con i dati corretti. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

È possibile che venga visualizzato uno degli errori seguenti dell'evento [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) .

|Codice di risultato|Descrizione|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|La sessione RTMP è scaduta dopo un periodo di inattività per il limite di tempo consentito.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Il timestamp dal codificatore RTMP per FLVTag audio o video non è valido.|
|MPE_CAPACITY_LIMIT_REACHED|Il codificatore invia i dati troppo velocemente.|
|Codici di errore sconosciuti|Questi codici di errore possono variare da errori di memoria a voci duplicate nella mappa di hash.|


## <a name="see-also"></a>Vedere anche

[Codici di errore dell'endpoint di streaming (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: flusso live con servizi multimediali](stream-live-tutorial-with-api.md)
