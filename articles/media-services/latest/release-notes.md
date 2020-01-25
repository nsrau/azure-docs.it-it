---
title: Note sulla versione di Servizi multimediali v3 | Microsoft Docs
description: Per stare al passo con gli sviluppi più recenti, questo articolo fornisce gli ultimi aggiornamenti relativi a Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: e7770a3faef0ccf7ca0f5b8c56f98007b8d89436
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719841"
---
# <a name="azure-media-services-v3-release-notes"></a>Note sulla versione di Servizi multimediali v3

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="known-issues"></a>Problemi noti

> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-sdk), l'interfaccia della riga di comando o uno degli SDK supportati.

Per altre informazioni, vedere [Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="november-2019"></a>Novembre 2019

### <a name="live-transcription-preview"></a>Anteprima della trascrizione in tempo reale

La trascrizione in tempo reale è ora disponibile in anteprima pubblica e disponibile per l'uso nell'area Stati Uniti occidentali 2.

La trascrizione in tempo reale è progettata per funzionare insieme agli eventi Live come funzionalità di componente aggiuntivo.  È supportato sia per gli eventi live di codifica pass-through sia per quelli standard o Premium.  Quando questa funzionalità è abilitata, il servizio usa la funzionalità di [riconoscimento vocale](../../cognitive-services/speech-service/speech-to-text.md) dei servizi cognitivi per trascrivere le parole pronunciate nell'audio in ingresso nel testo. Questo testo viene quindi reso disponibile per il recapito insieme a video e audio nei protocolli MPEG-DASH e HLS. La fatturazione è basata su un nuovo contatore aggiuntivo che rappresenta un costo aggiuntivo per l'evento Live quando si trova nello stato "in esecuzione".  Per informazioni dettagliate sulla trascrizione in tempo reale e sulla fatturazione, vedere [Traduzione in tempo reale](live-transcription.md)

> [!NOTE]
> Attualmente, la trascrizione in tempo reale è disponibile solo come funzionalità di anteprima nell'area Stati Uniti occidentali 2. Supporta la trascrizione di parole pronunciate in inglese (en-US) solo in questo momento.

### <a name="content-protection"></a>Protezione del contenuto

La funzionalità di *prevenzione della riproduzione dei token* rilasciata in aree limitate a settembre è ora disponibile in tutte le aree geografiche.
I clienti di servizi multimediali possono ora impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Per ulteriori informazioni, vedere [prevenzione della riproduzione dei token](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nuovi partner del codificatore Live consigliati

Aggiunta del supporto per i nuovi codificatori partner consigliati seguenti per lo streaming live RTMP:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [Videocamere GoPro Hero7/8 e Max Action](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Miglioramenti della codifica file

- È ora disponibile un nuovo set di impostazioni di codifica compatibile con il contenuto. Produce un set di MP4s allineati a GOP usando la codifica compatibile con il contenuto. Dato un contenuto di input, il servizio esegue un'analisi leggera iniziale del contenuto di input. USA tali risultati per determinare il numero ottimale di livelli, la velocità in bit appropriata e le impostazioni di risoluzione per il recapito tramite flusso adattivo. Questo set di impostazioni è particolarmente efficace per i video di bassa complessità e di complessità media, in cui i file di output sono a velocità in bit inferiori, ma con una qualità che offre comunque una valida esperienza ai visualizzatori. L'output conterrà file MP4 con interfoliazione video e audio. Per altre informazioni, vedere le [specifiche API aperte](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Miglioramento delle prestazioni e del multithreading per il ridimensionamento in Media Encoder Standard. In condizioni specifiche, il cliente dovrebbe vedere un miglioramento delle prestazioni compreso tra il 5-40% di codifica VOD. Il contenuto con complessità bassa codificato in più velocità in bit vedrà un aumento delle prestazioni più elevato. 
- La codifica standard ora mantiene una cadenza del GOP normale per il contenuto della frequenza dei fotogrammi variabile (VFR) durante la codifica VOD quando si usa l'impostazione GOP basata sul tempo.  Questo significa che il cliente che invia un contenuto misto di frequenza dei fotogrammi che varia da 15-30 fps ad esempio dovrebbe ora visualizzare le distanze GOP regolari calcolate nell'output per i file MP4 in streaming a bitrate adattivo. Ciò consente di migliorare la capacità di passare da una traccia all'altra durante la distribuzione tramite HLS o DASH. 
-  Miglioramento della sincronizzazione AV per il contenuto di origine con frequenza dei fotogrammi variabile (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, analisi video

- I fotogrammi chiave estratti utilizzando il set di impostazioni VideoAnalyzer sono ora nella risoluzione originale del video anziché essere ridimensionati. L'estrazione di fotogrammi chiave a risoluzione elevata offre immagini di qualità originali e consente di usare i modelli di intelligenza artificiale basati su immagini forniti da Microsoft Visione artificiale e Visione personalizzata Services per ottenere informazioni ancora più approfondite dal video.

## <a name="september-2019"></a>Settembre 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codifica live lineare di eventi Live

Media Services V3 annuncia l'anteprima di 24 ore x 365 giorni di Live Linear encoding of Live Events.

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

È stata annunciata la deprecazione di *Azure Media Indexer* e *Azure Media Indexer 2 Preview*. Per le date di ritiro, vedere l'argomento relativo ai [componenti legacy](../previous/legacy-components.md) . [Servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questi processori di contenuti multimediali legacy.

Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer e Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>La coppia di aree di Sudafrica è aperta per servizi multimediali 

Servizi multimediali è ora disponibile nelle aree Sud Africa settentrionale e Sud Africa occidentale.

Per altre informazioni, vedere [cloud e aree in cui è disponibile Media Services V3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

Si sta annunciando la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (biaigi) e *Azure Media Encoder* (AME), che sono in fase di ritiro. Per le date di ritiro, vedere l'argomento relativo ai [componenti legacy](../previous/legacy-components.md) .

Per informazioni dettagliate, vedere [eseguire la migrazione di biaigi a Media Encoder standard](https://go.microsoft.com/fwlink/?LinkId=2101334) ed [eseguire la migrazione di Ame a Media Encoder standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Luglio 2019

### <a name="content-protection"></a>Protezione del contenuto

Quando si esegue il flusso di contenuti protetti con la restrizione dei token, gli utenti finali devono ottenere un token inviato come parte della richiesta di recapito della chiave. La funzionalità di *prevenzione della riproduzione dei token* consente ai clienti di servizi multimediali di impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Per ulteriori informazioni, vedere [prevenzione della riproduzione dei token](content-protection-overview.md#token-replay-prevention).

A partire da luglio la funzionalità di anteprima era disponibile solo negli Stati Uniti centrali e negli Stati Uniti centro-occidentali.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-subclipping"></a>Suddivisione video

È ora possibile tagliare o sottotagliare un video quando lo si codifica usando un [processo](https://docs.microsoft.com/rest/api/media/jobs). 

Questa funzionalità può essere utilizzata con qualsiasi [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) compilata mediante i set di impostazioni [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

Vedere gli esempi:

* [Sottoclip di un video con .NET](subclip-video-dotnet-howto.md)
* [Sottoclip di un video con REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maggio 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Supporto di monitoraggio di Azure per i log di diagnostica e le metriche di servizi multimediali

È ora possibile usare monitoraggio di Azure per visualizzare i dati di telemetria emessi da servizi multimediali.

* Usare i log di diagnostica di monitoraggio di Azure per monitorare le richieste inviate dall'endpoint di distribuzione delle chiavi di servizi multimediali. 
* Monitorare le metriche emesse dagli [endpoint di streaming](streaming-endpoint-concept.md)di servizi multimediali.   

Per informazioni dettagliate, vedere [monitorare le metriche di servizi multimediali e i log di diagnostica](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Supporto per tracce audio multifunzione nella creazione dinamica dei pacchetti 

Quando si esegue lo streaming di asset che includono più tracce audio con più codec e lingue, la creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) supporta ora più tracce audio per l'output HLS (versione 4 o successive).

### <a name="korea-regional-pair-is-open-for-media-services"></a>La coppia di regioni coreana è aperta per servizi multimediali 

Servizi multimediali è ora disponibile nelle aree Corea centrale e Corea meridionale. 

Per altre informazioni, vedere [cloud e aree in cui è disponibile Media Services V3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

Sono stati aggiunti aggiornamenti che includono miglioramenti delle prestazioni di servizi multimediali.

* Sono state aggiornate le dimensioni massime del file supportate per l'elaborazione. Vedere, [quote e limitazioni](limits-quotas-constraints.md).
* [Miglioramenti della velocità di codifica](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Aprile 2019

### <a name="new-presets"></a>Nuovi set di impostazioni

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) è stato aggiunto ai set di impostazioni predefiniti dell'analizzatore.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) è stato aggiunto ai set di impostazioni predefiniti del codificatore. Per altre informazioni, vedere [codifica compatibile](content-aware-encoding.md)con i contenuti. 

## <a name="march-2019"></a>Marzo 2019

La creazione dinamica dei pacchetti ora supporta Dolby Atmos. Per altre informazioni, vedere [codec audio supportati dalla creazione dinamica dei pacchetti](dynamic-packaging-overview.md#audio-codecs).

È ora possibile specificare un elenco di filtri per asset o account, che si applicano al localizzatore di streaming. Per altre informazioni, vedere [associare filtri a streaming Locator](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Febbraio 2019

Media Services V3 è ora supportato nei cloud nazionali di Azure. Non tutte le funzionalità sono già disponibili in tutti i cloud. Per informazioni, vedere [Cloud e aree in cui sono presenti Servizi multimediali di Azure v3](azure-clouds-regions.md).

L'evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) è stato aggiunto agli schemi di Griglia di eventi di Azure per Servizi multimediali.

## <a name="january-2019"></a>gennaio 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard e file MPI 

Durante la codifica con Media Encoder Standard per produrre file MP4, un nuovo file con estensione mpi viene generato e aggiunto all'asset di output. Questo file MPI è progettato per migliorare le prestazioni per scenari di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e streaming.

Non è consigliabile modificare o rimuovere il file MPI né creare dipendenze nel proprio servizio sull'esistenza o meno di tale file.

## <a name="december-2018"></a>Dicembre 2018

Gli aggiornamenti dalla versione disponibile a livello generale dell'API V3 includono:
       
* Le proprietà **PresentationTimeRange** non sono più "obbligatorie" per i **filtri degli asset** e i **filtri degli account**. 
* Sono state rimosse le opzioni di query $top e $skip per **Processi** e **Trasformazioni** ed è stata aggiunta $orderby. Come parte dell'aggiunta della nuova funzionalità di ordinamento, è stato rilevato che le opzioni $top e $skip sono state accidentalmente esposte in precedenza anche se non sono implementate.
* L'estendibilità di enumerazione è stata riabilitata. Questa funzionalità è stata abilitata nelle versioni di anteprima di SDK ed è stata disattivata accidentalmente nella versione disponibile a livello generale.
* Due criteri di streaming predefiniti sono stati rinominati. **SecureStreaming** è ora **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** è ora **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembre 2018

Il modulo dell'interfaccia della riga di comando 2.0 è ora disponibile per [Servizi multimediali di Azure v3 (disponibilità a livello generale)](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) - v 2.0.50.

### <a name="new-commands"></a>Nuovi comandi

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest): consente di gestire le Media Reserved Unit. Per altre informazioni, vedere [Ridimensionare le Media Reserved Unit](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nuove funzionalità e modifiche di rilievo

#### <a name="asset-commands"></a>Comandi per gli asset

- Sono stati aggiunti gli argomenti ```--storage-account``` e ```--container```.
- Sono stati aggiunti i valori predefiniti per l'ora di scadenza (Now+23h) e per le autorizzazioni (lettura) nel comando ```az ams asset get-sas-url```.

#### <a name="job-commands"></a>Comandi per i processi

- Sono stati aggiunti gli argomenti ```--correlation-data``` e ```--label```.
- ```--output-asset-names``` è stato rinominato in ```--output-assets```. Ora accetta un elenco di asset separati da spazi nel formato 'assetName=label'. Un asset senza etichetta può essere inviato nel formato seguente: "assetName=".

#### <a name="streaming-locator-commands"></a>Comandi per i localizzatori di streaming

- Il comando base ```az ams streaming locator``` è stato sostituito con ```az ams streaming-locator```.
- Sono stati aggiunti gli argomenti ```--streaming-locator-id``` e ```--alternative-media-id support```.
- L'argomento ```--content-keys argument``` è stato aggiornato.
- ```--content-policy-name``` è stato rinominato in ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandi per i criteri di streaming

- Il comando base ```az ams streaming policy``` è stato sostituito con ```az ams streaming-policy```.
- È stato aggiunto il supporto dei parametri di crittografia in ```az ams streaming-policy create```.

#### <a name="transform-commands"></a>Comandi di trasformazione

- L'argomento ```--preset-names``` è stato sostituito con ```--preset```. Ora è possibile impostare solo 1 output/set di impostazioni alla volta (per aggiungerne altri, è necessario eseguire ```az ams transform output add```). Inoltre, è possibile impostare il set di impostazioni StandardEncoderPreset personalizzato passando il percorso del file JSON personalizzato.
- ```az ams transform output remove``` può essere eseguito passando l'indice di output da rimuovere.
- Gli argomenti ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` sono stati aggiunti nei comandi ```az ams transform create``` e ```az ams transform output add```.

## <a name="october-2018---ga"></a>Ottobre 2018 - Disponibilità generale

Questa sezione descrive gli aggiornamenti di ottobre di Servizi multimediali di Azure.

### <a name="rest-v3-ga-release"></a>Versione di disponibilità generale di REST v3

La [versione di disponibilità generale di REST v3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) include altre API per Live, filtri di manifesto a livello di account/asset e il supporto DRM.

#### <a name="azure-resource-management"></a>Gestione delle risorse di Azure 

Il supporto per la gestione delle risorse di Azure consente API di gestione e delle operazioni unificate (ora tutte in un'unica posizione).

A partire da questa versione, è possibile usare modelli di Resource Manager per creare eventi live.

#### <a name="improvement-of-asset-operations"></a>Miglioramento delle operazioni degli asset 

Sono stati introdotti i miglioramenti seguenti:

- Inserimento da URL HTTP(s) o da URL di firma di accesso condiviso di Archiviazione BLOB di Azure.
- Possibilità di specificare i nomi dei contenitori per gli asset. 
- Maggiore semplicità di supporto dell'output per la creazione di flussi di lavoro personalizzati con Funzioni di Azure.

#### <a name="new-transform-object"></a>Nuovo oggetto Transform

Il nuovo oggetto **Transform** semplifica il modello di codifica. Consente infatti di creare e condividere facilmente set di impostazioni e modelli di codifica di Resource Manager. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticazione e controllo degli accessi in base al ruolo di Azure Active Directory

L'autenticazione e il controllo degli accessi in base al ruolo di Azure AD consentono trasformazioni sicure, LiveEvent, criteri di chiave simmetrica e asset in base al ruolo o agli utenti in Azure AD.

#### <a name="client-sdks"></a>Client SDK  

Linguaggi supportati in Servizi multimediali v3: .NET Core, Java, Node. js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Aggiornamenti della codifica live

Sono stati introdotti gli aggiornamenti della codifica live seguenti:

- Nuova modalità a bassa latenza per i live (10 secondi end-to-end).
- Supporto RTMP migliorato (maggiore stabilità e più supporto per i codificatori di origine).
- Inserimento RTMPS sicuro.

    Quando si crea un live event, ora si ottengono quattro URL di inserimento. pressoché identici: hanno lo stesso token di streaming (AppId) e solo la parte del numero di porta è diversa. Due URL sono primari e due sono di backup per RTMPS. 
- Supporto per la transcodifica 24 ore su 24. 
- Supporto per annunci pubblicitari migliorato in RTMP tramite SCTE35.

#### <a name="improved-event-grid-support"></a>Supporto di Griglia di eventi migliorato

È possibile osservare i miglioramenti seguenti al supporto di Griglia di eventi:

- Integrazione di Griglia di eventi di Azure per facilitare lo sviluppo con App per la logica e Funzioni di Azure. 
- Possibilità di eseguire la sottoscrizione a eventi di codifica, canali live e altro ancora.

### <a name="cmaf-support"></a>Supporto per CMAF

Supporto della crittografia CMAF e 'cbcs' per lettori Apple HLS (iOS 11 +) e MPEG-DASH che supportano CMAF.

### <a name="video-indexer"></a>Indicizzatore video

La versione di disponibilità generale di Video Indexer era stata annunciata nel mese di agosto. Per informazioni aggiornate sulle funzionalità attualmente supportate, vedere [Informazioni su Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Modifiche pianificate

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
 
Il modulo dell'interfaccia della riga di comando di Azure 2.0 che include operazioni per tutte le funzionalità, tra cui Live, criteri di chiave simmetrica, filtri di account/risorse e criteri di streaming, sarà presto disponibile. 

### <a name="known-issues"></a>Problemi noti

Solo i clienti che hanno usato l'API di anteprima per i filtri di asset o di account sono interessati da questo problema.

Se sono stati creati filtri di asset o di account tra il 28/09 e il 12/10 con le API o con l'interfaccia della riga di comando di Servizi multimediali v3, è necessario rimuovere tutti i filtri di asset o di account e crearli di nuovo a causa di un conflitto di versione. 

## <a name="may-2018---preview"></a>Maggio 2018 - Anteprima

### <a name="net-sdk"></a>.NET SDK

In .NET SDK sono presenti le funzionalità seguenti:

* **Transforms** e **Jobs** per codificare o analizzare i contenuti multimediali. Per alcuni esempi, vedere [Eseguire lo streaming di file](stream-files-tutorial-with-api.md) e [Analyze](analyze-videos-tutorial-with-api.md) (Analizzare).
* **Localizzatori di streaming** per pubblicare ed eseguire lo streaming dei contenuti ai dispositivi degli utenti finali
* **Criteri di streaming** e **Criteri di chiave simmetrica** per configurare il recapito della chiave e la protezione del contenuto (Digital Rights Management) per la distribuzione dei contenuti.
* **Eventi live** e **Output live** per configurare l'inserimento e l'archiviazione dei contenuti in streaming live.
* **Assets** per archiviare e pubblicare i contenuti multimediali in Archiviazione di Azure. 
* **Endpoint di streaming** per configurare e ridimensionare la creazione dinamica dei pacchetti, la crittografia e lo streaming di contenuti multimediali live e on demand.

### <a name="known-issues"></a>Problemi noti

* Durante l'invio di un processo, è possibile specificare di inserire un video di origine usando gli URL HTTPS, gli URL SAS o i percorsi ai file che si trovano nell'archivio Blob di Azure. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

- [Overview](media-services-overview.md)
- [Note sulla versione di servizi multimediali V2](../previous/media-services-release-notes.md)
