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
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769760"
---
# <a name="azure-media-services-v3-release-notes"></a>Note sulla versione di Servizi multimediali v3

>Ricevere una notifica su quando rivedere questa pagina per gli `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` aggiornamenti copiando e incollando questo URL: nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="known-issues"></a>Problemi noti

> [!NOTE]
> È possibile usare il [portale di Azure](https://portal.azure.com/) per gestire gli [eventi live](live-events-outputs-concept.md) v3, visualizzare [asset](assets-concept.md) v3 e ottenere informazioni sull'accesso alle API. Per tutte le altre attività di gestione, ad esempio trasformazioni e processi, usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) oppure uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

Per altre informazioni, vedere [Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>Aprile 2020

### <a name="improvements-in-documentation"></a>Miglioramenti nella documentazione

I documenti di Azure Media Player sono stati migrati nella documentazione di [Azure.](../azure-media-player/azure-media-player-overview.md)

## <a name="january-2020"></a>Gennaio 2020

### <a name="improvements-in-media-processors"></a>Miglioramenti nei processori multimediali

- Migliorato il supporto per le sorgenti interlacciate nell'analisi video: tali contenuti ora vengono de-interlacciati correttamente prima di essere inviati ai motori di inferenza.
- Durante la generazione di miniature con la modalità "Migliore", l'encoder ora cerca oltre 30 secondi per selezionare un fotogramma non monocromatico.

### <a name="azure-government-cloud-updates"></a>Aggiornamenti del cloud di Azure per enti pubbliciAzure Government cloud updates

Servizi multimediali GA'ed nelle seguenti aree di Azure per enti pubblici: *USGov Arizona* e *USGov Texas*.

## <a name="december-2019"></a>Dicembre 2019

Aggiunto il supporto CDN per le intestazioni *Origin-Assist Prefetch* per lo streaming live e video on-demand; disponibile per i clienti che hanno un contratto diretto con Akamai CDN. La funzionalità di prelettura della rete CDN di Origin-Assist prevede i seguenti scambi di intestazione HTTP tra la rete CDN Akamai e l'origine di Servizi multimediali di Azure:

|Intestazione HTTP|Valori|Mittente|Ricevitore|Scopo|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Prefetch-Abilitato | 1 (predefinito) o 0 |RETE CDN|Origine|Per indicare che la rete CDN è abilitata per la prelettura|
|CDN-Origin-Assist-Prefetch-Path| Esempio: <br/>Frammenti(video -1400000000,format-mpd-time-cmaf)|Origine|RETE CDN|To provide prefetch path to CDN|
|RICHIESTA CDN-Origin-Assist-Prefetch-Request|1 (richiesta di prelettura) o 0 (richiesta regolare)|RETE CDN|Origine|Per indicare che la richiesta dalla rete CDN è una prelettura|

Per visualizzare parte dello scambio di intestazione in azione, è possibile provare i seguenti passaggi:

1. Usa Postman o curl per inviare una richiesta all'origine di Servizi multimediali per un segmento o un frammento audio o video. Assicurarsi di aggiungere l'intestazione CDN-Origin-Assist-Prefetch-Enabled: 1 nella richiesta.
2. Nella risposta verrà visualizzata l'intestazione CDN-Origin-Assist-Prefetch-Path con un percorso relativo come valore.

## <a name="november-2019"></a>Novembre 2019

### <a name="live-transcription-preview"></a>Anteprima trascrizione dal vivo

La trascrizione dal vivo è ora in anteprima pubblica e disponibile per l'uso nella regione degli Stati Uniti Occidentali 2.

La trascrizione dal vivo è progettata per funzionare in combinazione con eventi live come funzionalità aggiuntiva.  È supportato sia negli eventi live di codifica pass-through che standard o Premium.  Quando questa funzionalità è abilitata, il servizio utilizza la funzionalità [di sintesi](../../cognitive-services/speech-service/speech-to-text.md) vocale di Servizi cognitivi per trascrivere le parole pronunciate nell'audio in ingresso in testo. Questo testo viene quindi reso disponibile per la consegna insieme a video e audio nei protocolli MPEG-DASH e HLS. La fatturazione si basa su un nuovo misuratore di componente aggiuntivo che è un costo aggiuntivo per l'evento live quando è nello stato "In esecuzione".  Per informazioni dettagliate sulla trascrizione e la fatturazione in tempo reale, vedere [Trascrizione in tempo reale](live-transcription.md)

> [!NOTE]
> Attualmente, la trascrizione dal vivo è disponibile solo come funzione di anteprima nella regione degli Stati Uniti occidentali 2. Supporta la trascrizione delle parole pronunciate in inglese (en-us) solo in questo momento.

### <a name="content-protection"></a>Protezione del contenuto

La funzionalità *Di prevenzione della riproduzione* dei token rilasciata in aree limitate a settembre è ora disponibile in tutte le aree geografiche.
I clienti di Servizi multimediali possono ora impostare un limite al numero di volte in cui lo stesso token può essere utilizzato per richiedere una chiave o una licenza. Per ulteriori informazioni, consultate Prevenzione della [riproduzione](content-protection-overview.md#token-replay-prevention)di token .

### <a name="new-recommended-live-encoder-partners"></a>Nuovi partner di codificatori live consigliati

Aggiunto il supporto per i seguenti nuovi codificatori partner consigliati per lo streaming live RTMP:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Telecamere d'azione GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Miglioramenti alla codifica dei file

- È ora disponibile un nuovo predefinito Codifica in base al contenuto. Produce un set di MP4 allineati a GOP utilizzando la codifica in grado di riconoscere il contenuto. Dato qualsiasi contenuto di input, il servizio esegue un'analisi iniziale a peso leggero del contenuto di input. Utilizza questi risultati per determinare il numero ottimale di livelli, la velocità in bit appropriata e le impostazioni di risoluzione per la distribuzione tramite streaming adattivo. Questo preset è particolarmente efficace per i video a bassa complessità e media complessità, dove i file di output sono a velocità di bit più basse, ma con una qualità che offre ancora una buona esperienza agli spettatori. L'output conterrà file MP4 con interfoliazione video e audio. Per ulteriori informazioni, vedere le [specifiche API aperte](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Miglioramento delle prestazioni e multithreading per il resizer in Standard Encoder. In condizioni specifiche, il cliente dovrebbe vedere un aumento delle prestazioni tra la codifica VOD del 5-40%. I contenuti a bassa complessità codificati in più velocità di bit vedranno i massimi aumenti delle prestazioni. 
- La codifica standard ora mantiene una normale cadenza GOP per i contenuti VFR (Variable frame rate) durante la codifica VOD quando si utilizza l'impostazione GOP basata sul tempo.  Ciò significa che il cliente che invia contenuti con frequenza fotogrammi misti che variano tra 15 e 30 fps, ad esempio, ora dovrebbero vedere le normali distanze GOP calcolate sull'output ai file MP4 in streaming bitrate adattivo. Questo migliorerà la capacità di passare senza soluzione di continuità tra le tracce durante la consegna su HLS o DASH. 
-  Sincronizzazione AV migliorata per il contenuto di origine della frequenza fotogrammi variabile (VFR)

### <a name="video-indexer-video-analytics"></a>Indicizzatore video, Analisi video

- I fotogrammi chiave estratti utilizzando il preset VideoAnalyzer sono ora nella risoluzione originale del video invece di essere ridimensionati. L'estrazione di fotogrammi chiave ad alta risoluzione offre immagini di qualità originali e consente di utilizzare i modelli di intelligenza artificiale basati su immagini forniti dai servizi Microsoft Computer Vision e Custom Vision per ottenere ulteriori informazioni dal video.

## <a name="september-2019"></a>Settembre 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codifica lineare live degli eventi live

Media Services v3 annuncia l'anteprima di 24 ore x 365 giorni di codifica lineare live degli eventi live.

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori multimediali

Si annuncia la deprecazione *dell'indicizzatore multimediale* di Azure e dell'anteprima dell'indicizzatore multimediale di *Azure.* Per le date di pensionamento, vedere l'argomento [dei componenti legacy.](../previous/legacy-components.md) [L'indicizzatore video di Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questi processori multimediali legacy.

Per altre informazioni, vedere [Eseguire la migrazione dall'indicizzatore multimediale di Azure e l'indicizzatore multimediale di Azure 2 all'indicizzatore video di Servizi multimediali](../previous/migrate-indexer-v1-v2.md)di Azure.For more information, see Migrate from Azure Media Indexer and Azure Media Indexer 2 to Azure Media Services Video Indexer.

## <a name="august-2019"></a>Agosto 2019

###  <a name="media-services-v3"></a>Servizi multimediali v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>La coppia regionale sudafricana è aperta per i servizi multimediali 

Servizi multimediali è ora disponibile nelle regioni del Sud Africa Nord e Sud Africa Ovest.

Per ulteriori informazioni, consultate [Cloud e aree geografiche in cui è presente Servizi multimediali v3.](azure-clouds-regions.md)

###  <a name="media-services-v2"></a>Servizi multimediali v2  

#### <a name="deprecation-of-media-processors"></a>Deprecazione dei processori multimediali

Stiamo annunciando la deprecazione dei processori multimediali Windows *Azure Media Encoder* (WAME) e Azure Media Encoder *(AME),* che vengono ritirati. Per le date di pensionamento, vedere questo argomento relativo ai [componenti legacy.](../previous/legacy-components.md)

Per informazioni dettagliate, consultate [Eseguire la migrazione di WAME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e Eseguire la migrazione di [AME a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Luglio 2019

### <a name="content-protection"></a>Protezione del contenuto

Quando si trasmette contenuto protetto con restrizioni token, gli utenti finali devono ottenere un token inviato come parte della richiesta di recapito della chiave. La funzionalità *Di prevenzione* della riproduzione di token consente ai clienti di Servizi multimediali di impostare un limite sul numero di volte in cui lo stesso token può essere utilizzato per richiedere una chiave o una licenza. Per ulteriori informazioni, consultate Prevenzione della [riproduzione](content-protection-overview.md#token-replay-prevention)di token .

A partire da luglio, la funzione di anteprima era disponibile solo in US Central e Stati Uniti centro-occidentali.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-subclipping"></a>Sottoclip video

Ora potete tagliare o ritagliare un video durante la codifica utilizzando un [lavoro](https://docs.microsoft.com/rest/api/media/jobs). 

Questa funzionalità funziona con qualsiasi [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) compilata utilizzando i predefiniti [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Vedi esempi:

* [Ritagliare un video con .NET](subclip-video-dotnet-howto.md)
* [Clip secondario con REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maggio 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Supporto di Monitoraggio di Azure per i log e le metriche di diagnostica di Servizi multimedialiAzure Monitor support for Media Services diagnostic logs and metrics

È ora possibile usare Monitoraggio di Azure per visualizzare i dati di telemetria generati da Servizi multimediali.

* Usare i log di diagnostica di Monitoraggio di Azure per monitorare le richieste inviate dall'endpoint di recapito delle chiavi di Servizi multimediali. 
* Monitorare le metriche generate dagli endpoint di [streaming](streaming-endpoint-concept.md)di Servizi multimediali.   

Per informazioni dettagliate, vedere [Monitorare le metriche e i log di diagnostica](media-services-metrics-diagnostic-logs.md)di Servizi multimediali.

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Supporto di tracce audio più in Dynamic Packaging 

Durante lo streaming di asset con più tracce audio con più codec e lingue, [Dynamic Packaging](dynamic-packaging-overview.md) ora supporta più tracce audio per l'output HLS (versione 4 o successiva).

### <a name="korea-regional-pair-is-open-for-media-services"></a>La coppia regionale coreana è aperta per i servizi multimediali 

Servizi multimediali è ora disponibile nelle regioni del Sud di Corea e Corea. 

Per ulteriori informazioni, consultate [Cloud e aree geografiche in cui è presente Servizi multimediali v3.](azure-clouds-regions.md)

### <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

Sono stati aggiunti aggiornamenti che includono miglioramenti delle prestazioni di Servizi multimediali.

* La dimensione massima del file supportata per l'elaborazione è stata aggiornata. Vedi, [Quote e limiti](limits-quotas-constraints.md).
* [La codifica velocizza i miglioramenti](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Aprile 2019

### <a name="new-presets"></a>Nuovi preset

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) è stato aggiunto ai predefiniti dell'analizzatore incorporato.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) è stato aggiunto alle preimpostazioni del codificatore incorporate. Per ulteriori informazioni, consultate Codifica in base al [contenuto.](content-aware-encoding.md) 

## <a name="march-2019"></a>Marzo 2019

Dynamic Packaging ora supporta Dolby Atmos. Per ulteriori informazioni, consultate [Codec audio supportati dalla creazione di pacchetti dinamici.](dynamic-packaging-overview.md#audio-codecs)

Ora puoi specificare un elenco di filtri per asset o account, che si applicheranno al localizzatore di streaming. Per ulteriori informazioni, consultate [Associare filtri a Streaming Locator](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Febbraio 2019

Servizi multimediali v3 è ora supportato nei cloud nazionali di Azure.Media Services v3 is now supported in Azure national clouds. Non tutte le funzionalità sono già disponibili in tutti i cloud. Per informazioni, vedere [Cloud e aree in cui sono presenti Servizi multimediali di Azure v3](azure-clouds-regions.md).

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
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - consente di gestire le unità riservate ai supporti. Per altre informazioni, vedere [Ridimensionare le Media Reserved Unit](media-reserved-units-cli-how-to.md).

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
- [Aggiornamenti della documentazione di Servizi multimediali v3](docs-release-notes.md)
- [Note sulla versione di Servizi multimediali v2](../previous/media-services-release-notes.md)
