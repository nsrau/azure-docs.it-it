---
title: Gli errori di creazione di pacchetti e l'origine di Azure Media Services | Microsoft Docs
description: Questo argomento descrive gli errori che potrebbero ricevere dal servizio di Azure Media Services Endpoint di Streaming (Orgin).
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411381"
---
# <a name="streaming-endpoint-origin-errors"></a>Gli errori dell'Endpoint (origine) di streaming 

In questo argomento descrive gli errori che potrebbero ricevere da servizi multimediali di Azure [servizio Endpoint di Streaming](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 - Richiesta non valida

La richiesta contiene informazioni non valide e viene rifiutata con questi codici di errore e a causa di uno dei motivi seguenti:

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Un errore di sintassi o formato di URL. Gli esempi includono le richieste di un tipo non valido, un frammento non è valido o un track non è valido. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|La richiesta contiene tag crittografia nell'URL. Le richieste CMAF richiedono un tag di crittografia nell'URL. Gli altri protocolli che sono configurati con più di un tipo di crittografia richiedono anche il tag di crittografia per la risoluzione dell'ambiguità. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|La richiesta di archiviazione per soddisfare la richiesta non è riuscita con un errore di richiesta non valida. |

## <a name="403-forbidden"></a>403 - Accesso negato

La richiesta non è consentita per uno dei motivi seguenti:

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|La richiesta di archiviazione per soddisfare la richiesta non è riuscita con errore di autenticazione. Questa situazione può verificarsi se sono stati ruotare le chiavi di archiviazione e il servizio non è riuscito sincronizzare le chiavi di archiviazione. <br/><br/>Contatta Azure supportano visitando [Guida e supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Errore di operazione di archiviazione, accesso non è riuscita a causa di sufficienti autorizzazioni dell'Account. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |La richiesta di archiviazione per soddisfare la richiesta non riuscita perché l'account di archiviazione è disabilitata. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Errore di operazione di archiviazione, accesso non è riuscita a causa di errori generici. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Il formato di output è bloccato a causa della configurazione nel StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |La crittografia è necessaria per il contenuto, criteri di distribuzione sono obbligatorio per il formato di output. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Crittografia non è impostata nelle impostazioni di criteri di recapito. |

## <a name="404-not-found"></a>404 - Non trovato

L'operazione sta tentando di eseguire operazioni su una risorsa che non esiste più. Ad esempio, la risorsa potrebbe avere già stata eliminata.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |La traccia richiesta non è stata trovata. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |La risorsa richiesta non viene trovata. |
|MPE_UNAUTHORIZED |0x80890244 |L'accesso non è autorizzato. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Il timestamp richiesto non è stato trovato. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Il filtro manifesto dinamico richiesto non viene trovato. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |L'indice del frammento richiesto non rientra nell'intervallo valido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Live multimediale non è possibile trovare le voci per ottenere il buffer moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Impossibile trovare il frammento nel momento richiesto per un determinato track.<br/><br/>Potrebbe essere che il frammento non è nell'archiviazione. Provare un altro livello della presentazione che potrebbe avere un frammento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Impossibile trovare la voce di supporto per la velocità in bit richiesto nel manifesto. <br/><br/>Potrebbe essere che il lettore richiesto per una traccia video di un determinato a velocità in bit che non era nel manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Impossibile trovare alcuni metadati nel manifesto o non è stato trovato riassegnazione dall'archiviazione. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Errore di operazione di archiviazione, non sono state trovate risorse. |

## <a name="409-conflict"></a>409 - Conflitto

L'ID fornito per una risorsa in un `PUT` o `POST` operazione è stata usata da una risorsa esistente. Per risolvere questo problema, usare un ID diverso per la risorsa.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Errore di operazione di archiviazione, errore di conflitto.  |

## <a name="410"></a>410

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Per lo streaming live, quando il filtro che ha impostato su true, l'inizio o fine timestamp forceEndTimestamp è all'esterno dell'intervallo DVR corrente.|

## <a name="412-precondition-failure"></a>Errore 412-condizione preliminare

L'operazione ha specificato un eTag diverso dalla versione disponibile nel server, vale a dire, un errore di concorrenza ottimistica. Ripetere la richiesta dopo aver letto la versione più recente della risorsa e aggiornare il valore eTag nella richiesta.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Il frammento richiesto non è pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Errore di operazione di archiviazione, un errore di precondizione.|

## <a name="415-unsupported-media-type"></a>415 unsupported Media Type

Il formato di payload inviato dal client è in un formato non supportato.

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Nel contenuto già crittografato non è consigliabile applicare la crittografia.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |La crittografia non è valida per il formato di input.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Tipo di criteri di recapito non è valido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Le impostazioni originali può essere condiviso da più formati di output.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Il tipo o formato multimediale non è supportata. Ad esempio, servizi multimediali non supporta conteggio a livello di qualità che è più di 64. Nel tag video FLV, servizi multimediali non supporta un fotogramma video con Service Pack più e più PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Il formato di input dell'asset richiesto non è supportato. Servizi multimediali supporta (interattiva), Smooth MP4 (VoD) e formati di download progressivo.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Il formato di output richiesto non è supportato. Servizi multimediali supporta Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) e la progressiva Scarica formati.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Rilevato tipo di crittografia non supportato.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Il tipo di supporto richiesto non è supportato per il formato di output. I tipi supportati sono i video, audio o un sottotitolo "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Supporto di asset di origine è stato codificato con un formato multimediale che non è compatibile con il formato di output.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Asset di origine è stato codificato con un formato video non è compatibile con il formato di output. H.264, AVC, H.265 (HEVC, hev1 o hvc1) sono supportati.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Asset di origine è stato codificato con un formato audio che non è compatibile con il formato di output. Formati audio supportati sono AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Asset protetto di origine non può essere convertito nel formato di output.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Il formato di protezione non è supportato per il formato di output.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Il formato di protezione non è supportato per il formato di input.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Non è valido video NAL unità, ad esempio, solo il primo NAL nell'esempio può essere un AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Dimensioni dell'unità NAL non valida.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valore lunghezza unit NAL non valido.|
|MPE_FILTER_INVALID|0x80890236|Filtri manifesto dinamico non validi.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versioni di filtro non valido o non è supportato.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo di filtro non è valido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Intervallo non valido viene specificato dal filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Attributo di traccia non valido specificato dal filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|La lunghezza della finestra di presentazione non valido viene specificato dal filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Non valido in tempo reale Backoff specificato dal filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|È supportato un solo elemento absTimeInHNS nei filtri legacy.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Non è affatto non flussi più dopo aver applicato i filtri.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Il backoff in tempo reale è oltre la finestra DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Il backoff in tempo reale è maggiore della finestra di presentazione.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Superato i filtri predefiniti consentiti massimo dieci (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Più primo operatore di qualità video non è consentito nei filtri richiesta combinato.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Il numero di prima qualità a velocità in bit attributi deve essere uno (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Durata dei segmenti HLS deve essere inferiori a un terzo della finestra DVR e HLS il backoff.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Durate di frammento che devono essere minore o uguale a circa 20 secondi, o i livelli di qualità di input non sono ora allineato.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Errore specifico del DTS, non è possibile trovare il ReservedBox quando devono presentare nel DTSSpecficBox durante la finestra di DTS di analisi.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Errore specifico di DTS, nessun canale trovato nel DTSSpecficBox durante l'analisi di DTS casella.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Errore specifico di DTS, mancata corrispondenza tra tipi di esempio nel DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Errore specifico del DTS, multi-asset è impostata ma mancata corrispondenza tra tipi di esempio DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Errore specifico del DTS, dimensioni del flusso principale non sono valida.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Errore specifico del DTS, la risoluzione di esempio non è valida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Errore specifico del DTS, flusso secondario estensione per indice non valida.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Errore specifico del DTS, numero di blocchi di flusso secondario non è valido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Errore specifico del DTS, frequenza di campionamento non è valida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Errore di DTS specifico, il codice di orologio riferimento nell'estensione flusso secondario non è valido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Errore di DTS specifico, il numero di set di rimappatura dei relatori non è valido.|

Per la crittografia articoli ed esempi, vedere:

- [Concetto: protezione del contenuto](content-protection-overview.md)
- [Concetto: Criteri di chiave del contenuto](content-key-policy-concept.md)
- [Concetto: Criteri di streaming](streaming-policy-concept.md)
- [Esempio: proteggere con la crittografia AES](protect-with-aes128.md)
- [Esempio: proteggere contenuti con DRM](protect-with-drm.md)

Per materiale sussidiario di filtro, vedere:

- [Concetto: grazie ai manifesti dinamici](filters-dynamic-manifest-overview.md)
- [Concetto: i filtri](filters-concept.md)
- [Esempio: creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Esempio: creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempio: creare filtri con CLI](filters-dynamic-manifest-cli-howto.md)

Per articoli in tempo reale ed esempi, vedere:

- [Concetto: Panoramica di live streaming](live-streaming-overview.md)
- [Concetto: Gli eventi in tempo reale e gli output in tempo reale](live-events-outputs-concept.md)
- [: Esempio esercitazione di streaming in tempo reale](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 intervallo non Impossibile attenersi all'

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Errore operazione di archiviazione, ha restituito l'errore http 416, intervallo non valido.|

## <a name="500-internal-server-error"></a>500 - Errore interno del server

Durante l'elaborazione della richiesta, in Servizi multimediali viene generato un errore che impedisce di continuare l'elaborazione.  

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Ricevuto e tradotto da Winhttp codice di errore ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Ricevuto e tradotto da Winhttp codice di errore ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Ricevuto e tradotto da Winhttp codice di errore ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Errore di operazione di archiviazione, InternalError generale di uno degli errori HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Errore di operazione di archiviazione, OperationTimedOut generale di uno degli errori HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Errore di operazione di archiviazione, gli altri errori HTTP 500 InternalError o OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 - Servizio non disponibile

Il server non è attualmente in grado di ricevere richieste. Questo errore può essere causato da un numero eccessivo di richieste al servizio. Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio.

> [!NOTE]
> Controllare il messaggio di errore e la stringa del codice di errore per ottenere informazioni più dettagliate sul motivo per cui è stato generato un errore 503. Questo errore non indica necessariamente la limitazione delle richieste.
> 

|Codice di errore|Valore esadecimale |Descrizione dell'errore|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Errore di operazione di archiviazione, ha ricevuto l'errore sulla disponibilità del server HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

- [Codici di errore di codifica](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Concetti di servizi multimediali di Azure](concepts-overview.md)
- [Quote e limitazioni](limits-quotas-constraints.md)

## <a name="next-steps"></a>Passaggi successivi

[Esempio: accedere a codice di errore e il messaggio da ApiException con .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
