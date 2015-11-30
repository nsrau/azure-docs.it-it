<properties 
	pageTitle="Panoramica del modello di licenza Widevine" 
	description="Questo argomento fornisce una panoramica di un modello di licenza Widevine usato per configurare licenze Widevine." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015"
	ms.author="juliako"/>

#Panoramica del modello di licenza Widevine

##Panoramica

Servizi multimediali di Azure consente ora di configurare e richiedere licenze Widevine. Quando il lettore dell'utente finale tenta di riprodurre contenuto protetto da Widevine, viene inviata una richiesta al servizio di distribuzione di licenze per ottenere una licenza. Se il servizio licenze approva la richiesta, la licenza viene rilasciata e inviata al client e potrà essere usata per decrittografare e riprodurre il contenuto specificato.

La richiesta per la licenza Widevine è formattata come messaggio JSON.

È possibile scegliere di creare un messaggio vuoto senza valori, contenente solo "{}", e verrà creato un modello di licenza con tutti i valori predefiniti.

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

##Messaggio JSON

Nome | Valore | Descrizione
---|---|---
payload |Stringa con codifica Base64 |Richiesta di licenza inviata da un client. 
content\_id | Stringa con codifica Base64|Identificatore usato per derivare KeyId(s) e Content Key(s) per ogni content\_key\_specs.track\_type.
provider |stringa |Consente di cercare criteri e chiavi simmetriche. Obbligatorio.
policy\_name | stringa |Nome di un criterio precedentemente registrato. Facoltativo
allowed\_track\_types | enum | SD\_ONLY o SD\_HD. Consente di specificare le chiavi simmetriche da includere in una licenza
content\_key\_specs | Matrice di strutture JSON. Vedere la sezione **Specifiche della chiave simmetrica** riportata di seguito | Controllo più granulare dei contenuti da restituire. Per informazioni, vedere la sezione Specifiche della chiave simmetrica riportata di seguito. È possibile specificare solo uno dei valori allowed\_track\_types e content\_key\_specs. 
use\_policy\_overrides\_exclusively | Booleano: true o false | Usare gli attributi di criteri specificati in policy\_overrides e omettere tutti i criteri memorizzati in precedenza.
policy\_overrides | Struttura JSON, vedere la sezione **Override dei criteri** riportata di seguito | Impostazioni di criteri per questa licenza. Nel caso in cui per questo asset esistano già criteri predefiniti, verranno usati questi valori specificati. 
session\_init | Struttura JSON, vedere la sezione **Inizializzazione della sessione** riportata di seguito | Dati facoltativi passati alla licenza.
parse\_only | Booleano: true o false | La richiesta di licenza viene analizzata, ma la licenza non viene generata. Nella risposta, tuttavia, vengono restituiti i valori componenti la richiesta di licenza.  

##Specifiche della chiave simmetrica 

In presenza di criteri pre-esistenti, non è necessario specificare alcun valore nella specifica della chiave simmetrica. Verranno infatti usati i criteri pre-esistenti associati al contenuto per determinare il tipo di protezione di output, ad esempio HDCP o CGMS. Se i criteri pre-esistenti non sono registrati con il server di licenze Widevine, il provider di contenuti può inserire manualmente i valori nella richiesta di licenza.


Ogni valore content\_key\_specs deve essere specificato per tutte le singole tracce, indipendentemente dall'opzione use\_policy\_overrides\_exclusively.


Nome | Valore | Descrizione
---|---|---
content\_key\_specs. track\_type | stringa | Nome di un tipo di traccia. Se nella richiesta di licenza è specificato un valore content\_key\_specs, assicurarsi di specificare esplicitamente tutti i tipi di traccia. In caso contrario, non potranno essere riprodotti gli ultimi 10 secondi. 
content\_key\_specs <br/> security\_level | Valore UInt32 | Definisce i requisiti di affidabilità client per la riproduzione. <br/> 1 - È richiesta una soluzione di crittografia white box basata su software. <br/> 2 - È necessaria una soluzione di crittografia software e un decodificatore offuscato. <br/> 3 - Il materiale della chiave e le operazioni di crittografia devono essere eseguiti all'interno di un ambiente di esecuzione affidabile basato su hardware. <br/> 4 - Le operazioni di crittografia e decodifica devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware. <br/> 5 - Le operazioni di crittografia e decodifica e l'intera gestione dei contenuti multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.  
content\_key\_specs <br/> required\_output\_protection.hdc | Stringa - uno di: HDCP\_NONE, HDCP\_V1, HDCP\_V2 | Indica se è necessario il protocollo HDCP
content\_key\_specs <br/>key | Stringa con <br/>codifica Base64|Chiave simmetrica da usare per la traccia. Se specificato, è necessario immettere il valore track\_type o key\_id Questa opzione consente al provider di contenuti di inserire manualmente la chiave simmetrica della traccia ed evitare quindi che sia il server di licenze Widevine a generare o cercare una chiave.
content\_key\_specs.key\_id| Stringa binaria con codifica Base64, 16 byte | Identificatore univoco della chiave. 


##Override dei criteri 

Nome | Valore | Descrizione
---|---|---
policy\_overrides. can\_play | Booleano: true o false | Indica che è consentita la riproduzione del contenuto. Il valore predefinito è false.
policy\_overrides. can\_persist | Booleano: true o false |Indica che la licenza può essere mantenuta in una risorsa di archiviazione non volatile per l'uso offline. Il valore predefinito è false.
policy\_overrides. can\_renew | Booleano: true o false |Indica che è consentito il rinnovo della licenza. Se true, è possibile estendere la durata della licenza mediante heartbeat. Il valore predefinito è false. 
policy\_overrides. license\_duration\_seconds | int64 | Indica la finestra temporale di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. 
policy\_overrides. rental\_duration\_seconds | int64 | Indica la finestra temporale in cui è consentita la riproduzione. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. 
policy\_overrides. playback\_duration\_seconds | int64 | La finestra temporale di visualizzazione a partire dall'inizio della riproduzione nell'ambito del periodo di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. 
policy\_overrides. renewal\_server\_url |stringa | Tutte le richieste di heartbeat (rinnovo) per la licenza saranno inoltrate all'URL specificato. Questo campo viene usato solo se can\_renew è true.
policy\_overrides. renewal\_delay\_seconds |int64 |Numero di secondi prima che venga eseguito il primo tentativo di rinnovo, a partire dal valore license\_start\_time. Questo campo viene usato solo se can\_renew è true. Il valore predefinito è 0 
policy\_overrides. renewal\_retry\_interval\_seconds | int64 | Specifica l'intervallo di tempo, espresso in secondi, tra richieste di rinnovo della licenza consecutive, in caso di errore. Questo campo viene usato solo se can\_renew è true. 
policy\_overrides. renewal\_recovery\_duration\_seconds | int64 | Il periodo di tempo per il quale può continuare la riproduzione mentre vengono eseguiti nuovi tentativi di rinnovo, ancora con negativo a causa di problemi di back-end con il server licenze. Il valore 0 indica che non esiste alcun limite di durata. Questo campo viene usato solo se can\_renew è true.
policy\_overrides. renew\_with\_usage | Booleano: true o false |Indica che la licenza verrà inviata per il rinnovo nel momento in cui si inizia ad usarla. Questo campo viene usato solo se can\_renew è true. 

##Inizializzazione della sessione

Nome | Valore | Descrizione
---|---|---
provider\_session\_token | Stringa con codifica Base64 |Questo token di sessione viene restituito alla licenza e persisterà nei rinnovi successivi, ma non in altre sessioni. 
provider\_client\_token | Stringa con codifica Base64 | Token client da restituire nella risposta di licenza. Se la richiesta di licenza contiene un token client, questo valore viene ignorato. Il token client persisterà anche in altre sessioni della licenza.
override\_provider\_client\_token | Booleano: true o false |Se false e la richiesta di licenza contiene un token client, usare il token della richiesta anche se nella struttura è stato specificato un token client. Se true, usare sempre il token specificato nella struttura.

##Configurare licenze Widevine usando tipi .NET

Servizi multimediali fornisce API .NET che consentono di configurare licenze Widevine.

###Classi definite nell'SDK di Servizi multimediali per .NET

Di seguito sono riportate le definizioni di questi tipi.

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

###Esempio

L'esempio seguente mostra come usare le API .NET per configurare una licenza Widevine semplice.

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


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Vedere anche

[Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md)

<!---HONumber=Nov15_HO4-->