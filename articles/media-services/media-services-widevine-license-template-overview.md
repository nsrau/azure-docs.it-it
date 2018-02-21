---
title: Panoramica del modello di licenza Widevine | Microsoft Docs
description: Questo argomento offre una panoramica di un modello di licenza Widevine usato per configurare le licenze Widevine.
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 85de5765975b0c55fafe9bb4c14a1c1f435a6d5c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="widevine-license-template-overview"></a>Panoramica del modello di licenza Widevine
Servizi multimediali di Azure consente di configurare e richiedere licenze Google Widevine. Quando il lettore tenta di riprodurre contenuto protetto da Widevine, viene inviata una richiesta al servizio di distribuzione di licenze per ottenere una licenza. Se il servizio di licenza approva la richiesta, genera la licenza. Viene inviata al client e usata per decrittografare e riprodurre il contenuto specificato.

Una richiesta per la licenza Widevine è formattata come messaggio JSON.  

>[!NOTE]
> È possibile creare un messaggio vuoto senza alcun valore, semplicemente "{}". Quindi viene creato un modello di licenza con le impostazioni predefinite. Il valore predefinito funziona nella maggior parte dei casi. Gli scenari di distribuzione di licenze basati su Microsoft devono sempre usare i valori predefiniti. Se è necessario impostare i valori di "provider" e "content_id", un provider deve corrispondere alle credenziali di Widevine.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Messaggio JSON
| NOME | Valore | DESCRIZIONE |
| --- | --- | --- |
| payload |Stringa con codifica Base64 |Richiesta di licenza inviata da un client. |
| content_id |Stringa con codifica Base64 |Identificatore usato per derivare l'ID chiave e la chiave simmetrica per ogni content_key_specs.track_type. |
| provider |stringa |Consente di cercare criteri e chiavi simmetriche. Se viene usata la distribuzione delle chiavi Microsoft per la distribuzione di licenze Widevine, questo parametro viene ignorato. |
| policy_name |stringa |Nome di un criterio precedentemente registrato. facoltativo. |
| allowed_track_types |enum |SD_ONLY o SD_HD. Consente di controllare le chiavi simmetriche incluse in una licenza. |
| content_key_specs |Matrice di strutture JSON. Vedere la sezione "Specifiche della chiave simmetrica".  |Controllo più granulare delle chiavi simmetriche da restituire. Per altre informazioni, vedere la sezione "Specifiche della chiave simmetrica". È possibile specificare solo uno dei valori allowed_track_types e content_key_specs. |
| use_policy_overrides_exclusively |Booleano: true o false |Usare gli attributi di criteri specificati in policy_overrides e omettere tutti i criteri memorizzati in precedenza. |
| policy_overrides |Struttura JSON, vedere la sezione "Override dei criteri". |Impostazioni di criteri per questa licenza.  Nel caso in cui per questo asset esistano già criteri predefiniti, vengono usati questi valori specificati. |
| session_init |Struttura JSON, vedere la sezione "Inizializzazione della sessione". |Dati facoltativi vengono passati alla licenza. |
| parse_only |Booleano: true o false |La richiesta di licenza viene analizzata, ma la licenza non viene generata. Nella risposta, tuttavia, vengono restituiti i valori componenti la richiesta di licenza. |

## <a name="content-key-specs"></a>Specifiche della chiave simmetrica
In presenza di criteri pre-esistenti, non è necessario specificare alcun valore nella specifica della chiave simmetrica. Il criterio pre-esistente associato a questo contenuto viene usato per determinare la protezione dell'output, ad esempio il sistema HDCP e il sistema CGMS. Se i criteri pre-esistenti non sono registrati con il server di licenze Widevine, il provider di contenuti può inserire i valori nella richiesta di licenza.   

Ogni valore content_key_specs deve essere specificato per tutte le singole tracce, indipendentemente dall'opzione use_policy_overrides_exclusively. 

| NOME | Valore | DESCRIZIONE |
| --- | --- | --- |
| content_key_specs track_type |stringa |Nome di un tipo di traccia. Se nella richiesta di licenza è specificato un valore content_key_specs, assicurarsi di specificare esplicitamente tutti i tipi di traccia. In caso contrario, non possono essere riprodotti gli ultimi 10 secondi. |
| content_key_specs  <br/> security_level |Valore UInt32 |Definisce i requisiti di affidabilità client per la riproduzione. <br/> - È richiesta una soluzione di crittografia white box basata su software. <br/> - Sono necessari una soluzione di crittografia software e un decodificatore offuscato. <br/> - Il materiale della chiave e le operazioni di crittografia devono essere eseguiti all'interno di un ambiente di esecuzione affidabile basato su hardware. <br/> - Le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.  <br/> - Le operazioni di crittografia, decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware. |
| content_key_specs <br/> required_output_protection.hdc |Stringa, uno di HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se è necessario il protocollo HDCP. |
| content_key_specs <br/>key |Base64<br/>codifica Base64 |Chiave simmetrica da usare per la traccia. Se specificato, è necessario immettere il valore track_type o key_id Il provider di contenuti può usare questa opzione per inserire la chiave simmetrica della traccia ed evitare quindi che sia il server di licenze Widevine a generare o cercare una chiave. |
| content_key_specs.key_id |Stringa binaria con codifica Base64, 16 byte |Identificatore univoco della chiave. |

## <a name="policy-overrides"></a>Override dei criteri
| NOME | Valore | DESCRIZIONE |
| --- | --- | --- |
| policy_overrides can_play |Booleano: true o false |Indica che è consentita la riproduzione del contenuto. Il valore predefinito è false. |
| policy_overrides can_persist |Booleano: true o false |Indica che la licenza può essere mantenuta in una memoria non volatile per l'uso offline. Il valore predefinito è false. |
| policy_overrides can_renew |Booleano: true o false |Indica che è consentito il rinnovo della licenza. Se true, è possibile estendere la durata della licenza mediante heartbeat. Il valore predefinito è false. |
| policy_overrides license_duration_seconds |int64 |Indica la finestra temporale di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides rental_duration_seconds |int64 |Indica la finestra temporale in cui è consentita la riproduzione. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides playback_duration_seconds |int64 |La finestra temporale di visualizzazione a partire dall'inizio della riproduzione nell'ambito del periodo di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides renewal_server_url |stringa |Tutte le richieste di heartbeat (rinnovo) per la licenza sono inoltrate all'URL specificato. Questo campo viene usato solo se can_renew è true. |
| policy_overrides renewal_delay_seconds |int64 |Numero di secondi prima che venga eseguito il primo tentativo di rinnovo, a partire dal valore license_start_time. Questo campo viene usato solo se can_renew è true. Il valore predefinito è 0. |
| policy_overrides renewal_retry_interval_seconds |int64 |Specifica l'intervallo di tempo, espresso in secondi, tra richieste di rinnovo della licenza consecutive, in caso di errore. Questo campo viene usato solo se can_renew è true. |
| policy_overrides renewal_recovery_duration_seconds |int64 |La finestra temporale nella quale può continuare la riproduzione mentre vengono eseguiti nuovi tentativi di rinnovo, ancora con esito negativo a causa di problemi di back-end con il server licenze. Il valore 0 indica che non esiste alcun limite di durata. Questo campo viene usato solo se can_renew è true. |
| policy_overrides renew_with_usage |Booleano: true o false |Indica che la licenza viene inviata per il rinnovo nel momento in cui si inizia a usarla. Questo campo viene usato solo se can_renew è true. |

## <a name="session-initialization"></a>Inizializzazione della sessione
| NOME | Valore | DESCRIZIONE |
| --- | --- | --- |
| provider_session_token |Stringa con codifica Base64 |Questo token di sessione viene restituito alla licenza e persiste nei rinnovi successivi, ma non in altre sessioni. |
| provider_client_token |Stringa con codifica Base64 |Token client da restituire nella risposta di licenza. Se la richiesta di licenza contiene un token client, questo valore viene ignorato. Il token client persiste anche in altre sessioni della licenza. |
| override_provider_client_token |Booleano: true o false |Se false e la richiesta di licenza contiene un token client, usare il token della richiesta anche se nella struttura è stato specificato un token client. Se true, usare sempre il token specificato nella struttura. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Configurare licenze Widevine usando tipi .NET
Servizi multimediali fornisce API .NET che consentono di configurare licenze Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classi definite nell'SDK di Servizi multimediali per .NET
Le classi seguenti sono le definizioni di questi tipi:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Esempio
L'esempio seguente mostra come usare le API .NET per configurare una licenza Widevine semplice:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche 
[Usare la crittografia comune dinamica Widevine e/o PlayReady](media-services-protect-with-playready-widevine.md)

