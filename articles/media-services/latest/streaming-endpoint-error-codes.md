---
title: Errori di creazione di pacchetti e di origine di Servizi multimediali di Azure Documenti Microsoft
description: Questo argomento descrive gli errori che è possibile ricevere dal servizio Azure Media Services Streaming Endpoint (Orgin).
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
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65411381"
---
# <a name="streaming-endpoint-origin-errors"></a>Errori dell'endpoint di streaming (origine)Streaming Endpoint (Origin) errors 

In questo argomento vengono descritti gli errori che è possibile ricevere dal [servizio Endpoint](streaming-endpoint-concept.md)di flusso di Servizi multimediali di Azure.

## <a name="400-bad-request"></a>400 - Richiesta non valida

La richiesta contiene informazioni non valide e viene rifiutata con questi codici di errore e per uno dei seguenti motivi:

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Sintassi dell'URL o errore di formato. Gli esempi includono le richieste per un tipo non valido, un frammento non valido o una traccia non valida. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|La richiesta non ha tag di crittografia nell'URL. Le richieste CMAF richiedono un tag di crittografia nell'URL. Anche altri protocolli configurati con più di un tipo di crittografia richiedono il tag di crittografia per la disambiguazione. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|La richiesta di archiviazione per soddisfare la richiesta non è riuscita con un errore di richiesta non valida. |

## <a name="403-forbidden"></a>403 - Accesso negato

La richiesta non è consentita per uno dei motivi seguenti:

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|La richiesta di archiviazione per soddisfare la richiesta non è riuscita con un errore di autenticazione. Ciò può verificarsi se le chiavi di archiviazione sono state ruotate e il servizio non è stato in grado di sincronizzare le chiavi di archiviazione. <br/><br/>Contattare il supporto di Azure accedendo a Guida e supporto nel portale di Azure.Contact Azure support by going to [Help - support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in the Azure portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Errore di funzionamento di archiviazione, l'accesso non è riuscito a causa di autorizzazioni dell'account insufficienti. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |La richiesta di archiviazione per soddisfare la richiesta non è riuscita perché l'account di archiviazione è disabilitato. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Errore dell'operazione di archiviazione, accesso non riuscito a causa di errori generici. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Il formato di output è bloccato a causa della configurazione in StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |La crittografia è necessaria per il contenuto, i criteri di recapito sono necessari per il formato di output. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |La crittografia non è impostata nelle impostazioni dei criteri di recapito. |

## <a name="404-not-found"></a>404 - Non trovato

L'operazione tenta di agire su una risorsa che non esiste più. È ad esempio possibile che la risorsa sia già stata eliminata.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |La traccia richiesta non è stata trovata. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |La risorsa richiesta non è stata trovata. |
|MPE_UNAUTHORIZED |0x80890244 |L'accesso non è autorizzato. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Il timestamp richiesto non è stato trovato. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Il filtro del manifesto dinamico richiesto non è stato trovato. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |L'indice del frammento richiesto non rientra nell'intervallo valido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Non è possibile trovare voci multimediali in tempo reale per ottenere il buffer di moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Impossibile trovare il frammento all'ora richiesta per una traccia specifica.<br/><br/>Potrebbe essere che il frammento non è in deposito. Provare un livello diverso della presentazione che potrebbe avere un frammento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Impossibile trovare la voce multimediale per la velocità di bit richiesta nel manifesto. <br/><br/>Potrebbe essere che il giocatore ha chiesto una traccia video di un certo bitrate che non era nel manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Impossibile trovare determinati metadati nel manifesto o non è possibile trovare rebase dall'archivio. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Errore dell'operazione di archiviazione, risorsa non trovata. |

## <a name="409-conflict"></a>409 - Conflitto

L'ID fornito per `PUT` una `POST` risorsa in un'operazione o è stato preso da una risorsa esistente. Utilizzare un altro ID per la risorsa per risolvere il problema.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EEE  |Errore dell'operazione di archiviazione, errore di conflitto.  |

## <a name="410"></a>410

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Per il live streaming, quando il filtro con forceEndTimestamp impostato su true, il timestamp iniziale o finale è esterno alla finestra DVR corrente.|

## <a name="412-precondition-failure"></a>412 Precondition Failure

L'operazione ha specificato un eTag diverso da quello disponibile nel server, ovvero un errore di concorrenza ottimistica. Riprovare la richiesta dopo aver letto la versione più recente della risorsa e aver aggiornato l'eTag sulla richiesta.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Il frammento richiesto non è pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Errore di operazione di archiviazione, un errore di precondizione.|

## <a name="415-unsupported-media-type"></a>415 Tipo di supporto non supportato

Il formato di payload inviato dal client è in un formato non supportato.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Non deve applicare la crittografia sul contenuto già crittografato.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |La crittografia non è valida per il formato di input.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Il tipo di criterio di recapito non è valido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Le impostazioni originali potrebbero essere condivise da più formati di output.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Il formato o il tipo di supporto non è supportato. Ad esempio, Servizi multimediali non supporta il numero di livelli di qualità superiore a 64. Nel tag video FLV, Servizi multimediali non supporta un fotogramma video con più SPS e più PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Il formato di input dell'asset richiesto non è supportato. Media Services supporta i formati Smooth (live), MP4 (VoD) e Progressive.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Il formato di output richiesto non è supportato. Servizi multimediali supporta i formati Smooth, DASH(CSF, CMAF), HLS (v3, v4, CMAF) e Progressive.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Tipo di crittografia non supportato rilevato.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Il tipo di supporto richiesto non è supportato dal formato di output. I tipi supportati sono video, audio o sottotitolo "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Il supporto dell'asset di origine è stato codificato con un formato multimediale non compatibile con il formato di output.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|L'asset sorgente è stato codificato con un formato video non compatibile con il formato di output. Sono supportati H.264, AVC, H.265 (HEVC, hev1 o hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|L'asset sorgente è stato codificato con un formato audio non compatibile con il formato di output. I formati audio supportati sono AAC, E-AC3 (DD) , Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|L'asset protetto di origine non può essere convertito nel formato di output.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Il formato di protezione non è supportato dal formato di output.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Il formato di protezione non è supportato dal formato di input.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unità NAL video non valida, ad esempio, solo il primo NAL nel campione può essere un AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Dimensione unità NAL non valida.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valore di lunghezza dell'unità NAL non valido.|
|MPE_FILTER_INVALID|0x80890236|Filtri di manifesto dinamici non validi.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versioni di filtro non valide o non supportate.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo di filtro non valido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|L'intervallo non valido è specificato dal filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Attributo track non valido specificato dal filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|La lunghezza della finestra di presentazione non valida viene specificata dal filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Il valore in tempo reale non valido viene specificato dal filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Nei filtri legacy è supportato un solo elemento absTimeInHNS.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Non ci sono più flussi dopo l'applicazione dei filtri.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Il live back off è oltre la finestra DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Il live back off è maggiore della finestra di presentazione.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Superato dieci (10) massimo consentito filtri predefiniti.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Più operatori di qualità video non sono consentiti nei filtri di richiesta combinati.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Il numero di attributi bitrate di qualità di prima qualità deve essere uno (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|La durata del segmento HLS deve essere inferiore a un terzo della finestra DVR e HLS si distoglie.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Le durate dei frammenti devono essere inferiori o uguali a circa 20 secondi oppure i livelli di qualità di input non sono allineati nel tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Errore specifico DTS, impossibile trovare il ReservedBox quando deve essere presente nel DTSSpecficBox durante l'analisi della casella DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Errore specifico dTS, nessun canale trovato nel DTSSpecficBox durante l'analisi della casella DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Errore specifico di DTS, tipo di esempio non corrispondente nel DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Errore specifico DTS, multi-asset è impostato ma DTSH tipo di esempio non corrispondente.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Errore specifico DTS, la dimensione del flusso principale non è valida.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Errore specifico dTS, risoluzione del campione non valida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Errore specifico DTS, l'indice di estensione del flusso secondario non è valido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Errore specifico DTS, il numero di blocco del flusso secondario non è valido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Errore specifico dTS, frequenza di campionamento non valida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifica errore, il codice orologio di riferimento nell'estensione del flusso secondario non è valido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifica errore, il numero di altoparlanti remap impostato non è valido.|

Per articoli ed esempi di crittografia, vedere:For encryption articles and examples, see:

- [Concetto: protezione dei contenuti](content-protection-overview.md)
- [Concetto: Criteri chiave del contenuto](content-key-policy-concept.md)
- [Concetto: Criteri di streaming](streaming-policy-concept.md)
- [Esempio: protezione con crittografia AES](protect-with-aes128.md)
- [Esempio: protezione con DRM](protect-with-drm.md)

Per indicazioni sui filtri, vedere:For filter guidance, see:

- [Concetto: manifesti dinamici](filters-dynamic-manifest-overview.md)
- [Concetto: filtri](filters-concept.md)
- [Esempio: creare filtri con LE API REST](filters-dynamic-manifest-rest-howto.md)
- [Esempio: creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempio: creare filtri con CLI](filters-dynamic-manifest-cli-howto.md)

Per articoli ed esempi in tempo reale, vedere:

- [Concetto: panoramica del live streaming](live-streaming-overview.md)
- [Concetto: eventi dal vivo e uscite dal vivo](live-events-outputs-concept.md)
- [Esempio: esercitazione in live streaming](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Gamma Non soddisfacente

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Errore dell'operazione di archiviazione, restituito errore http 416, intervallo non valido.|

## <a name="500-internal-server-error"></a>500 - Errore interno del server

Durante l'elaborazione della richiesta, in Servizi multimediali viene generato un errore che impedisce di continuare l'elaborazione.  

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Ricevuto e tradotto da Winhttp codice di errore di ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Ricevuto e tradotto da Winhttp codice di errore di ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Ricevuto e tradotto da Winhttp codice di errore di ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Errore dell'operazione di archiviazione, InternalError generale di uno degli errori HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Errore dell'operazione di archiviazione, generale OperationTimedOut di uno degli errori HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Errore dell'operazione di archiviazione, altri errori HTTP 500 oltre a InternalError o OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 - Servizio non disponibile

Il server non è attualmente in grado di ricevere richieste. Questo errore può essere causato da un numero eccessivo di richieste al servizio. Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio.

> [!NOTE]
> Controllare il messaggio di errore e la stringa del codice di errore per ottenere informazioni più dettagliate sul motivo per cui è stato generato un errore 503. Questo errore non indica necessariamente la limitazione delle richieste.
> 

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Errore dell'operazione di archiviazione, ricevuto l'errore di occupato del server HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

- [Codici di errore di codifica](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Concetti relativi ai Servizi multimediali di Azure](concepts-overview.md)
- [Quote e limitazioni](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passaggi successivi

[Esempio: accesso ErrorCode e Message from ApiException con .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
