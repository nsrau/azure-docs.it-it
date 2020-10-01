---
title: Note sulla versione di Servizi multimediali v3 | Microsoft Docs
description: Per stare al passo con gli sviluppi più recenti, questo articolo fornisce gli ultimi aggiornamenti relativi a Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 88b1eb70814c349d488933179a16c084a0af803c
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619968"
---
# <a name="azure-media-services-v3-release-notes"></a>Note sulla versione di Servizi multimediali v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="known-issues"></a>Problemi noti

> [!NOTE]
> È possibile usare la [portale di Azure](https://portal.azure.com/) per gestire [gli eventi live](live-events-outputs-concept.md)V3, visualizzare [Asset](assets-concept.md) e processi V3, ottenere informazioni sull'accesso alle API, crittografare il contenuto. Per tutte le altre attività di gestione (ad esempio, gestione di trasformazioni e processi), usare l' [API REST](https://aka.ms/ams-v3-rest-ref), l' [interfaccia](https://aka.ms/ams-v3-cli-ref)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.
>
> Per informazioni dettagliate, vedere: [limitazioni portale di Azure per servizi multimediali V3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).


## <a name="august-2020"></a>Agosto 2020

### <a name="dynamic-encryption"></a>Crittografia dinamica
Il supporto per la crittografia legacy PlayReady protected Interoperable file Format (PIFF 1,1) è ora disponibile in Dynamic Packager. Fornisce supporto per i set di Smart TV legacy da Samsung e LG che hanno implementato le prime bozze dello standard crittografia comune (CENC) pubblicate da Microsoft.  Il formato PIFF 1,1 è noto anche come formato di crittografia precedentemente supportato dalla libreria client Silverlight. Attualmente, l'unico scenario di utilizzo per questo formato di crittografia è quello di puntare al mercato Smart TV legacy, in cui rimane un numero non semplice di TV intelligenti in alcune aree che supportano solo Smooth Streaming con la crittografia PIFF 1,1. 

Per usare il nuovo supporto per la crittografia PIFF 1,1, modificare il valore di crittografia in ' Piff ' nel percorso URL del localizzatore di streaming. Per ulteriori informazioni, vedere la [Panoramica di protezione del contenuto.](content-protection-overview.md)
Per esempio: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Il supporto di PIFF 1,1 viene fornito come soluzione compatibile con le versioni precedenti per Smart TV (Samsung, LG) che ha implementato la versione "Silverlight" iniziale di crittografia comune. Si consiglia di usare solo il formato PIFF, dove necessario per il supporto di Samsung o LG Smart TV legacy fornito tra 2009-2015 che supporta la versione PIFF 1,1 della crittografia PlayReady. 

## <a name="july-2020"></a>Luglio 2020

### <a name="live-transcriptions"></a>Trascrizioni in tempo reale

Le trascrizioni Live supportano ora 19 lingue e 8 aree.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Protezione dei contenuti con servizi multimediali e Azure AD

È stata pubblicata un'esercitazione denominata [protezione del contenuto end-to-end con Azure ad](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Disponibilità elevata

È stato pubblicato un [esempio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)di disponibilità elevata con servizi multimediali e [Panoramica](./media-services-high-availability-encoding.md) di video on demand (VOD).

## <a name="june-2020"></a>Giugno 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Analisi video in tempo reale su IoT Edge versione di anteprima

L'anteprima di analisi video in tempo reale su IoT Edge è stata pubblica. Per ulteriori informazioni, vedere [Note sulla versione](../live-video-analytics-edge/release-notes.md).

Analisi video in tempo reale su IoT Edge è un'espansione alla famiglia di servizi multimediali. Consente di analizzare video in tempo reale con i modelli di intelligenza artificiale scelti nei propri dispositivi perimetrali e, facoltativamente, di acquisire e registrare il video. È ora possibile creare app con analisi video in tempo reale al perimetro senza doversi preoccupare della complessità della creazione e della gestione di una pipeline video live.

## <a name="may-2020"></a>Maggio 2020

Servizi multimediali di Azure è ora disponibile a livello generale nelle aree seguenti: "Germania settentrionale", "Germania centro-occidentale", "Svizzera settentrionale" e "Svizzera occidentale". I clienti possono distribuire Servizi multimediali di Azure in queste aree tramite il portale di Azure.

## <a name="april-2020"></a>Aprile 2020

### <a name="improvements-in-documentation"></a>Ottimizzazione della documentazione

È stata eseguita la migrazione della documentazione di Azure Media Player alla [documentazione di Azure](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Gennaio 2020

### <a name="improvements-in-media-processors"></a>Ottimizzazione dei processori di contenuti multimediali

- Supporto migliorato per le origini interlacciate nell'analisi video: il contenuto è ora deinterlacciato correttamente prima di essere inviato ai motori di inferenza.
- Quando si generano anteprime con la modalità "Best", il codificatore esegue ora una ricerca di oltre 30 secondi per selezionare un frame non monocromatico.

### <a name="azure-government-cloud-updates"></a>Aggiornamenti al cloud di Azure per enti pubblici

Disponibilità generale di Servizi multimediali nelle aree di Azure per enti pubblici seguenti: *USGov Arizona* e *USGov Texas*.

## <a name="december-2019"></a>Dicembre 2019

Aggiunta del supporto della rete CDN per le intestazioni *Origin-Assist Prefetch* per lo streaming live e video on demand; disponibile per i clienti con contratto diretto con la rete CDN Akamai. La funzionalità Origin-Assist CDN-Prefetch prevede gli scambi di intestazioni HTTP seguenti tra la rete CDN Akamai e l'origine di Servizi multimediali di Azure:

|Intestazione HTTP|Valori|Mittente|Ricevitore|Scopo|
| ---- | ---- | ---- | ---- | ----- |
|Funzionalità CDN-Origin-Assist-Prefetch abilitata | 1 (valore predefinito) o 0 |RETE CDN|Origine|Per indicare che sulla rete CDN è abilitata la prelettura|
|CDN-Origin-Assist-Prefetch-Path| Esempio: <br/>Frammenti (video=1400000000,format=mpd-time-cmaf)|Origine|RETE CDN|Per fornire il percorso di prelettura alla rete CDN|
|CDN-Origin-Assist-Prefetch-Request|1 (richiesta di prelettura) o 0 (richiesta normale)|RETE CDN|Origine|Per indicare che la richiesta dalla rete CDN è una richiesta di prelettura|

Per visualizzare parte dello scambio di intestazioni in azione, è possibile provare a eseguire la procedura seguente:

1. Usare Postman o copiare come URL per inviare una richiesta all'origine di Servizi multimediali per un segmento o un frammento audio o video. Assicurarsi di aggiungere l'intestazione CDN-Origin-Assist-Prefetch-Enabled: 1 nella richiesta.
2. Nella risposta verrà visualizzata l'intestazione CDN-Origin-Assist-Prefetch-Path con un percorso relativo come valore.

## <a name="november-2019"></a>Novembre 2019

### <a name="live-transcription-preview"></a>Trascrizione live: anteprima

La trascrizione live è ora disponibile in anteprima pubblica e per l'uso nell'area Stati Uniti occidentali 2.

La trascrizione live è progettata per funzionare insieme agli eventi live come capacità di componente aggiuntivo.  È supportata sia per gli eventi live di codifica pass-through sia per quelli Standard o Premium.  Quando questa funzionalità è abilitata, il servizio usa la funzionalità [Riconoscimento vocale](../../cognitive-services/speech-service/speech-to-text.md) di Servizi cognitivi per trascrivere le parole pronunciate nell'audio in ingresso in testo. Questo testo viene quindi reso disponibile per il recapito insieme a video e audio nei protocolli MPEG-DASH e HLS. La fatturazione si basa su un nuovo contatore aggiuntivo che rappresenta un costo aggiuntivo per l'evento live quando si trova nello stato "in esecuzione".  Per informazioni dettagliate sulla trascrizione e la fatturazione di eventi live, vedere [Trascrizione live](live-transcription.md)

> [!NOTE]
> Attualmente, la trascrizione live è disponibile solo come funzionalità di anteprima nell'area Stati Uniti occidentali 2. Attualmente supporta solo la trascrizione di parole pronunciate in inglese (en-US).

### <a name="content-protection"></a>Protezione del contenuto

La funzionalità di *Protezione riproduzione token* rilasciata in aree limitate a settembre è ora disponibile in tutte le aree geografiche.
I clienti di Servizi multimediali possono ora impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Per altre informazioni, vedere [Protezione riproduzione token](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nuovi partner consigliati per i codificatori live

Aggiunta del supporto per i nuovi codificatori partner consigliati indicati di seguito per lo streaming live RTMP:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Videocamere GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Ottimizzazione della codifica file

- È ora disponibile un nuovo set di impostazioni di codifica con riconoscimento del contenuto, che produce un set di MP4 allineati a GOP mediante codifica con riconoscimento del contenuto. Dato un determinato contenuto di input, il servizio esegue un'analisi iniziale semplice del contenuto di input e usa i risultati ottenuti per determinare il numero ottimale di livelli, la velocità in bit appropriata e le impostazioni di risoluzione per il recapito tramite flusso adattivo. Questo set di impostazioni è particolarmente efficace per i video di bassa e media complessità, in cui i file di output presentano una velocità in bit inferiore, ma con una qualità che offre comunque una valida esperienza ai visualizzatori. L'output conterrà file MP4 con interfoliazione video e audio. Per altre informazioni, vedere le [specifiche OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Miglioramento delle prestazioni e del multithreading per il ridimensionamento del codificatore standard. In condizioni specifiche, il cliente dovrebbe vedere un miglioramento delle prestazioni compreso tra il 5 e il 40% di codifica VOD. Il contenuto con complessità bassa codificato con una velocità in bit multipla vedrà il massimo aumento delle prestazioni. 
- La codifica standard ora mantiene una cadenza GOP regolare per il contenuto della frequenza dei fotogrammi variabile (VFR) durante la codifica VOD quando si usa l'impostazione GOP basata sul tempo.  Questo significa che i clienti che inviano contenuto misto di frequenza dei fotogrammi che variano tra 15-30 fps, ad esempio, dovrebbero ora visualizzare le normali distanze del GOP calcolate nell'output per i file MP4 di streaming a bitrate adattivo. Ciò consente di migliorare la capacità di passare perfettamente da un brano all'altro durante la distribuzione tramite HLS o DASH. 
-  Ottimizzazione della sincronizzazione AV per i contenuti di origine con frequenza fotogrammi variabile (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, analisi video

- I fotogrammi chiave estratti mediante il set di impostazioni VideoAnalyzer presentano ora la risoluzione video originale anziché essere ridimensionati. L'estrazione di fotogrammi chiave ad alta risoluzione offre immagini di qualità originali e consente di usare i modelli di intelligenza artificiale basati su immagini forniti da Microsoft Visione artificiale e Visione personalizzata Services per ottenere informazioni ancora più approfondite dal video.

## <a name="september-2019"></a>Settembre 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codifica live lineare di eventi live

Media Services V3 annuncia l'anteprima di 24 ore per 365 giorni della codifica live lineare di eventi live.

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

Si annuncia la deprecazione di *Azure Media Indexer* e *Anteprima di Azure Media Indexer 2*. Per le date di ritiro, vedere l'articolo relativo ai  [componenti legacy](../previous/legacy-components.md) . [Video Indexer di Servizi multimediali di Azure](../video-indexer/index.yml) sostituisce questi processori di contenuti multimediali legacy.

Per altre informazioni, vedere l'articolo [Eseguire la migrazione da Azure Media Indexer e Azure Media Indexer 2 a Video Indexer di Servizi multimediali di Azure](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>La coppia di aree del Sudafrica è aperta per Servizi multimediali 

Servizi multimediali è ora disponibile nelle aree Sudafrica settentrionale e Sudafrica occidentale.

Per altre informazioni, vedere [Cloud e aree in cui sono presenti Servizi multimediali v3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori di contenuti multimediali

Viene annunciata la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME), che sono in fase di ritiro. Per le date di ritiro, vedere l'articolo relativo ai [componenti legacy](../previous/legacy-components.md) .

Per informazioni dettagliate, vedere [Eseguire la migrazione da WAME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) ed [Eseguire la migrazione da AME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Luglio 2019

### <a name="content-protection"></a>Protezione del contenuto

Quando si esegue lo streaming di contenuti protetti mediante restrizione dei token, gli utenti finali devono ottenere un token inviato come parte della richiesta di recapito della chiave. La funzionalità *Protezione riproduzione token* consente ai clienti di Servizi multimediali di impostare un limite per il numero di volte in cui è possibile usare lo stesso token per richiedere una chiave o una licenza. Per altre informazioni, vedere [Protezione riproduzione token](content-protection-overview.md#token-replay-prevention).

A partire da luglio, la funzionalità di anteprima era disponibile solo negli Stati Uniti centrali e negli Stati Uniti centro-occidentali.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-subclipping"></a>Creazione di clip video secondarie

È ora possibile tagliare o suddividere in clip secondarie un video quando lo si codifica usando un [Processo](/rest/api/media/jobs). 

Questa funzionalità può essere usata con qualsiasi [Trasformazione](/rest/api/media/transforms) compilata mediante i set di impostazioni [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o i set di impostazioni [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Ecco alcuni esempi:

* [Creare una clip video secondaria con .NET](subclip-video-dotnet-howto.md)
* [Creare una clip video secondaria con REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maggio 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Supporto di Monitoraggio di Azure per i log di diagnostica e le metriche di Servizi multimediali

È ora possibile usare Monitoraggio di Azure per visualizzare i dati di telemetria emessi da Servizi multimediali.

* Usare i log di diagnostica di Monitoraggio di Azure per monitorare le richieste inviate dall'endpoint di recapito delle chiavi di Servizi multimediali. 
* Monitorare le metriche emesse dagli [endpoint di streaming](streaming-endpoint-concept.md) di Servizi multimediali.   

Per altre informazioni, vedere [Monitorare log di diagnostica e metriche di Servizi multimediali](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Supporto per brani audio multipli nella creazione dinamica dei pacchetti 

Per lo streaming di asset che hanno diverse tracce audio con più codec e linguaggi, la [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) supporta più brani audio per l'output HLS (versione 4 o successiva).

### <a name="korea-regional-pair-is-open-for-media-services"></a>La coppia di aree della Corea è aperta per Servizi multimediali 

Servizi multimediali è ora disponibile nelle aree Corea centrale e Corea meridionale. 

Per altre informazioni, vedere [Cloud e aree in cui sono presenti Servizi multimediali v3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

Sono stati aggiunti aggiornamenti che includono miglioramenti delle prestazioni di Servizi multimediali.

* Sono state aggiornate le dimensioni massime del file supportate per l'elaborazione. Vedere, [quote e limiti](limits-quotas-constraints.md).
* [Ottimizzazione della velocità di codifica](concept-media-reserved-units.md).

## <a name="april-2019"></a>Aprile 2019

### <a name="new-presets"></a>Nuovi set di impostazioni

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) è stato aggiunto ai set di impostazioni predefiniti dell'analizzatore.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) è stato aggiunto ai set di impostazioni predefiniti del codificatore. Per altre informazioni, vedere [Codifica con riconoscimento del contenuto](content-aware-encoding.md). 

## <a name="march-2019"></a>Marzo 2019

La creazione dinamica dei pacchetti ora supporta Dolby Atmos. Per altre informazioni, vedere [Codec audio supportati dalla creazione dinamica dei pacchetti](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

È ora possibile specificare un elenco di filtri per asset o account, che si applicano al localizzatore di streaming. Per altre informazioni, vedere [Associare filtri al localizzatore di streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Febbraio 2019

La versione 3 di Servizi multimediali è ora supportata nei cloud nazionali di Azure. Non tutte le funzionalità sono già disponibili in tutti i cloud. Per informazioni, vedere [Cloud e aree in cui sono presenti Servizi multimediali di Azure v3](azure-clouds-regions.md).

L'evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) è stato aggiunto agli schemi di Griglia di eventi di Azure per Servizi multimediali.

## <a name="january-2019"></a>Gennaio 2019

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

Il modulo dell'interfaccia della riga di comando 2.0 è ora disponibile per [Servizi multimediali di Azure v3 (disponibilità a livello generale)](/cli/azure/ams?view=azure-cli-latest) - v 2.0.50.

### <a name="new-commands"></a>Nuovi comandi

- [az ams account](/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest): consente di gestire le Media Reserved Unit. Per altre informazioni, vedere [Ridimensionare le Media Reserved Unit](media-reserved-units-cli-how-to.md).

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

### <a name="video-indexer"></a>Video Indexer

La versione di disponibilità generale di Video Indexer era stata annunciata nel mese di agosto. Per informazioni aggiornate sulle funzionalità attualmente supportate, vedere [Informazioni su Video Indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Modifiche pianificate

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
 
Il modulo dell'interfaccia della riga di comando di Azure 2.0 che include operazioni per tutte le funzionalità, tra cui Live, criteri di chiave simmetrica, filtri di account/risorse e criteri di streaming, sarà presto disponibile. 

### <a name="known-issues"></a>Problemi noti

Solo i clienti che hanno usato l'API di anteprima per i filtri di asset o di account sono interessati da questo problema.

Se sono stati creati filtri di asset o di account tra il 28/09 e il 12/10 con le API o con l'interfaccia della riga di comando di Servizi multimediali v3, è necessario rimuovere tutti i filtri di asset o di account e crearli di nuovo a causa di un conflitto di versione. 

## <a name="may-2018---preview"></a>Maggio 2018 - Anteprima

### <a name="net-sdk"></a>.NET SDK

In .Net SDK sono presenti le funzionalità seguenti:

* **Transforms** e **Jobs** per codificare o analizzare i contenuti multimediali. Per alcuni esempi, vedere [Eseguire lo streaming di file](stream-files-tutorial-with-api.md) e [Analyze](analyze-videos-tutorial-with-api.md) (Analizzare).
* **Localizzatori di streaming** per pubblicare ed eseguire lo streaming dei contenuti ai dispositivi degli utenti finali
* **Criteri di streaming** e **Criteri di chiave simmetrica** per configurare il recapito della chiave e la protezione del contenuto (Digital Rights Management) per la distribuzione dei contenuti.
* **Eventi live** e **Output live** per configurare l'inserimento e l'archiviazione dei contenuti in streaming live.
* **Assets** per archiviare e pubblicare i contenuti multimediali in Archiviazione di Azure. 
* **Endpoint di streaming** per configurare e ridimensionare la creazione dinamica dei pacchetti, la crittografia e lo streaming di contenuti multimediali live e on demand.

### <a name="known-issues"></a>Problemi noti

* Durante l'invio di un processo, è possibile specificare di inserire un video di origine usando gli URL HTTPS, gli URL SAS o i percorsi ai file che si trovano nell'archivio Blob di Azure. Attualmente, servizi multimediali V3 non supporta la codifica di trasferimento Chunked sugli URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

[Linee guida per la migrazione da Media Services V2 a V3](migrate-from-v2-to-v3.md#known-issues).

## <a name="next-steps"></a>Passaggi successivi

- [Overview](media-services-overview.md)
- [Aggiornamenti della documentazione di Servizi multimediali v3](docs-release-notes.md)
- [Note sulla versione di Servizi multimediali v2](../previous/media-services-release-notes.md)
