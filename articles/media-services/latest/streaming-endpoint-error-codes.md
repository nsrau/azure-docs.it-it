---
title: Errori di creazione e creazione di pacchetti di servizi multimediali di Azure | Microsoft Docs
description: Questo argomento descrive gli errori che possono essere ricevuti dal servizio Azure Media Services streaming endpoint (Orgin).
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8442c52052a2016da002d392c6fe0834b3813229
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295326"
---
# <a name="streaming-endpoint-origin-errors"></a>Errori dell'endpoint di streaming (Origine)

Questo argomento descrive gli errori che possono essere ricevuti dal [servizio di endpoint di streaming](streaming-endpoint-concept.md)di servizi multimediali di Azure.

## <a name="400-bad-request"></a>400 - Richiesta non valida

La richiesta contiene informazioni non valide e viene rifiutata con questi codici di errore e a causa di uno dei motivi seguenti:

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Sintassi o errore di formato dell'URL. Gli esempi includono le richieste per un tipo non valido, un frammento non valido o una traccia non valida. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|La richiesta non contiene tag di crittografia nell'URL. Le richieste CMAF richiedono un tag di crittografia nell'URL. Altri protocolli configurati con più di un tipo di crittografia richiedono anche il tag di crittografia per la risoluzione dell'ambiguità. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|La richiesta di archiviazione per completare la richiesta non è riuscita con un errore di richiesta non valida. |

## <a name="403-forbidden"></a>403 - Accesso negato

La richiesta non è consentita per uno dei motivi seguenti:

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|La richiesta di archiviazione per completare la richiesta non è riuscita con un errore di autenticazione. Questo problema può verificarsi se le chiavi di archiviazione sono ruotate e il servizio non è stato in grado di sincronizzare le chiavi di archiviazione. <br/><br/>Per contattare il supporto tecnico di Azure, passare a [Guida e supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nella portale di Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Errore dell'operazione di archiviazione. accesso non riuscito a causa di autorizzazioni di account insufficienti. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |La richiesta di archiviazione per completare la richiesta non è riuscita perché l'account di archiviazione è disabilitato. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Errore dell'operazione di archiviazione. accesso non riuscito a causa di errori generici. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Il formato di output è bloccato a causa della configurazione nella StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |La crittografia è necessaria per il contenuto. i criteri di recapito sono necessari per il formato di output. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |La crittografia non è impostata nelle impostazioni dei criteri di recapito. |

## <a name="404-not-found"></a>404 - Non trovato

L'operazione tenta di agire su una risorsa che non esiste più. Ad esempio, è possibile che la risorsa sia stata già eliminata.

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |La traccia richiesta non è stata trovata. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |La risorsa richiesta non è stata trovata. |
|MPE_UNAUTHORIZED |0x80890244 |L'accesso non è autorizzato. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Il timestamp richiesto non è stato trovato. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Il filtro manifesto dinamico richiesto non è stato trovato. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |L'indice del frammento richiesto è oltre l'intervallo valido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Non è possibile trovare voci multimediali attive per ottenere il buffer Moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Impossibile trovare il frammento al momento richiesto per una particolare traccia.<br/><br/>Potrebbe essere che il frammento non si trova nell'archivio. Provare un livello diverso della presentazione che potrebbe avere un frammento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Impossibile trovare l'elemento multimediale per la velocità in bit richiesta nel manifesto. <br/><br/>Potrebbe essere il giocatore a richiedere una traccia video di una determinata velocità in bit non presente nel manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Non è possibile trovare determinati metadati nel manifesto oppure non è possibile trovare la riassegnazione dalla risorsa di archiviazione. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Errore dell'operazione di archiviazione. la risorsa non è stata trovata. |

## <a name="409-conflict"></a>409 - Conflitto

L'ID fornito per una risorsa in un' `PUT` `POST` operazione o è stato accettato da una risorsa esistente. Per risolvere il problema, usare un altro ID per la risorsa.

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Errore dell'operazione di archiviazione, errore di conflitto.  |

## <a name="410"></a>410

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Per lo streaming live, quando il filtro con forceEndTimestamp è impostato su true, il timestamp di inizio o di fine è esterno alla finestra DVR corrente.|

## <a name="412-precondition-failure"></a>412 Precondition Failure

L'operazione ha specificato un valore eTag diverso dalla versione disponibile sul server, ovvero un errore di concorrenza ottimistica. Riprovare a eseguire la richiesta dopo aver letto la versione più recente della risorsa e aver aggiornato l'ETag sulla richiesta.

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Il frammento richiesto non è pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Errore dell'operazione di archiviazione, un errore di condizione preliminare.|

## <a name="415-unsupported-media-type"></a>415 tipo di supporto non supportato

Il formato del payload inviato dal client è in un formato non supportato.

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Non applicare la crittografia al contenuto già crittografato.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |La crittografia non è valida per il formato di input.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Il tipo di criteri di recapito non è valido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Le impostazioni originali possono essere condivise da più formati di output.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Il formato o il tipo di supporto non è supportato. Ad esempio, servizi multimediali non supporta il conteggio del livello di qualità superiore a 64. Nel tag video FLV, servizi multimediali non supporta un frame video con più SPS e più PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Il formato di input dell'asset richiesto non è supportato. Servizi multimediali supporta formati Smooth (Live), MP4 (VoD) e di download progressivo.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Il formato di output richiesto non è supportato. Servizi multimediali supporta i formati Smooth, DASH (CSF, CMAF), HLS (v3, V4, CMAF) e di download progressivo.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|È stato rilevato un tipo di crittografia non supportato.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Il tipo di supporto richiesto non è supportato dal formato di output. I tipi supportati sono il sottotitolo video, audio o "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Il supporto dell'asset di origine è stato codificato con un formato multimediale non compatibile con il formato di output.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|L'asset di origine è stato codificato con un formato video non compatibile con il formato di output. Sono supportati h. 264, AVC, H. 265 (HEVC, hev1 o hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|L'asset di origine è stato codificato con un formato audio non compatibile con il formato di output. I formati audio supportati sono AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|L'asset protetto di origine non può essere convertito nel formato di output.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Il formato di protezione non è supportato dal formato di output.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Il formato di protezione non è supportato dal formato di input.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unità NAL video non valida. ad esempio, solo il primo NAL nell'esempio può essere un AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Dimensioni unità NAL non valide.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valore di lunghezza unità NAL non valido.|
|MPE_FILTER_INVALID|0x80890236|Filtri manifesto dinamici non validi.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versioni di filtro non valide o non supportate.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo di filtro non valido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|L'intervallo specificato dal filtro non è valido.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Attributo Track non valido specificato dal filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Lunghezza della finestra di presentazione non valida specificata dal filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Il filtro consente di specificare un backup in tempo reale non valido.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Nei filtri legacy è supportato un solo elemento absTimeInHNS.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Non sono disponibili altri flussi dopo l'applicazione dei filtri.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Il backup in tempo reale si trova oltre la finestra DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Il backup in tempo reale è maggiore della finestra di presentazione.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Sono stati superati 10 (10) i filtri predefiniti massimi consentiti.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Non sono consentiti più operatori di qualità video prima nei filtri di richiesta combinati.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Il numero di attributi di prima velocità in bit di qualità deve essere uno (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|La durata del segmento HLS deve essere inferiore a un terzo della finestra DVR e HLS.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Le durate del frammento devono essere minori o uguali a circa 20 secondi oppure i livelli di qualità di input non sono allineati.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Errore specifico di DTS. Impossibile trovare il ReservedBox quando deve essere presente in DTSSpecficBox durante l'analisi della casella DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Errore specifico di DTS, nessun canale trovato in DTSSpecficBox durante l'analisi della casella DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Errore specifico di DTS, tipo di esempio non corrispondente in DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Errore specifico di DTS. la funzionalità multiasset è impostata, ma il tipo di esempio DTSH non corrisponde.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Errore specifico di DTS. le dimensioni del flusso di base non sono valide.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Errore specifico di DTS. la risoluzione di esempio non è valida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Errore specifico di DTS. l'indice dell'estensione del flusso secondario non è valido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Errore specifico di DTS. il numero del blocco del flusso secondario non è valido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Errore specifico di DTS. la frequenza di campionamento non è valida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Errore specifico di DTS. il codice dell'orologio di riferimento nell'estensione del flusso secondario non è valido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Errore specifico di DTS. il numero di set di riassociazione di altoparlanti non è valido.|

Per gli articoli e gli esempi di crittografia, vedere:

- [Concetto: protezione del contenuto](content-protection-overview.md)
- [Concetto: criteri chiave simmetrica](content-key-policy-concept.md)
- [Concetto: criteri di streaming](streaming-policy-concept.md)
- [Esempio: proteggere con la crittografia AES](protect-with-aes128.md)
- [Esempio: proteggere con DRM](protect-with-drm.md)

Per indicazioni sul filtro, vedere:

- [Concetto: manifesti dinamici](filters-dynamic-manifest-overview.md)
- [Concetto: filtri](filters-concept.md)
- [Esempio: creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Esempio: creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempio: creare filtri con l'interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

Per gli articoli e gli esempi Live, vedere:

- [Concetto: Panoramica dello streaming live](live-streaming-overview.md)
- [Concetto: eventi live e output Live](live-events-outputs-concept.md)
- [Esempio: esercitazione per lo streaming live](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Impossibile attenersi all'intervallo richiesto

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Errore dell'operazione di archiviazione. errore HTTP 416 restituito, intervallo non valido.|

## <a name="500-internal-server-error"></a>500 - Errore interno del server

Durante l'elaborazione della richiesta, in Servizi multimediali viene generato un errore che impedisce di continuare l'elaborazione.  

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Ricevuto e tradotto dal codice di errore WinHTTP di ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Ricevuto e tradotto dal codice di errore WinHTTP di ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Ricevuto e tradotto dal codice di errore WinHTTP di ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Errore dell'operazione di archiviazione, InternalError generale di uno degli errori HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Errore dell'operazione di archiviazione, OperationTimedOut generale di uno degli errori HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Errore dell'operazione di archiviazione, altri errori HTTP 500 rispetto a InternalError o OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 - Servizio non disponibile

Il server non è attualmente in grado di ricevere richieste. Questo errore può essere causato da un numero eccessivo di richieste al servizio. Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio.

> [!NOTE]
> Controllare il messaggio di errore e la stringa del codice di errore per ottenere informazioni più dettagliate sul motivo per cui è stato generato un errore 503. Questo errore non indica necessariamente la limitazione delle richieste.
> 

|Codice errore|Valore esadecimale |Descrizione errore|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Errore dell'operazione di archiviazione. errore del server HTTP occupato 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

- [Codici di errore di codifica](/rest/api/media/jobs/get#joberrorcode)
- [Concetti relativi ai Servizi multimediali di Azure](concepts-overview.md)
- [Quote e limiti](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passaggi successivi

[Esempio: accedere al codice ErrorCode e al messaggio da ApiException con .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
