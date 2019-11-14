---
title: Eseguire la migrazione di Servizi multimediali di Azure dalla versione 2 alla versione 3 | Microsoft Docs
description: In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni. Questo articolo fornisce inoltre materiale sussidiario sulla migrazione per aggiornare Servizi multimediali dalla versione 2 alla versione 3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: fb36387764efbdaa1ad3d164ba419bee49770871
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049011"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3

Questo articolo descrive le modifiche introdotte in Servizi multimediali di Azure v3, illustra le differenze tra le due versioni e offre materiale sussidiario sulla migrazione.

In caso di sviluppo di un servizio video basato sulle [API legacy di Servizi multimediali v2](../previous/media-services-overview.md), è necessario rivedere le linee guida e considerazioni seguenti prima della migrazione alle API v3. L'API v3 include numerosi vantaggi e nuove funzionalità che migliorano l'esperienza di sviluppo e le funzionalità di Servizi multimediali. Tuttavia, come definito nella sezione [Problemi noti](#known-issues) di questo articolo, esistono anche alcune limitazioni causate da differenze tra le versioni delle API. Questa pagina viene aggiornata man mano che il team di Servizi multimediali apporta costanti miglioramenti alle API v3 e colma le lacune tra le versioni. 

> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

## <a name="benefits-of-media-services-v3"></a>Vantaggi di Servizi multimediali v3
  
### <a name="api-is-more-approachable"></a>API più accessibile

*  v3 si basa su una superficie API unificata, che espone funzionalità operative e di gestione basate su Azure Resource Manager. I modelli di Azure Resource Manager possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, eventi live e altro ancora.
* Documento di [specifica openapi (noto in precedenza come spavalderia)](https://aka.ms/ams-v3-rest-sdk) .
    Espone lo schema per tutti i componenti del servizio, tra cui la codifica basata su file.
* SDK disponibili per [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) e Ruby.
* Integrazione dell'[interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli-ref) per semplificare il supporto di scripting.

### <a name="new-features"></a>Nuove funzionalità

* Per l'elaborazione di processi basata su file è possibile usare un URL HTTP(S) come input.<br/>Non è necessario avere contenuto già archiviato in Azure né creare asset.
* Introduce il concetto di [trasformazioni](transforms-jobs-concept.md) per l'elaborazione di processi basata su file. Una trasformazione consente di creare configurazioni riutilizzabili, creare modelli di Azure Resource Manager e isolare le impostazioni di elaborazione tra più clienti o tenant.
* Un asset può avere più [localizzatori di streaming](streaming-locators-concept.md), ognuno con impostazioni diverse di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e crittografia dinamica.
* La [protezione del contenuto](content-key-policy-concept.md) supporta le funzionalità con più chiavi.
* È possibile trasmettere eventi live per una durata massima di 24 ore quando si usa Servizi multimediali per la transcodifica di un feed di contributi a bitrate singolo in un flusso di output a bitrate multipli.
* Nuovo supporto per streaming live a bassa latenza per eventi live. Per altre informazioni, vedere [Latenza](live-event-latency.md).
* L'anteprima degli eventi live supporta la [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e la crittografia dinamica. Questo consente la protezione del contenuto nell'anteprima, nonché la creazione di pacchetti DASH e HLS.
* L'output live è più semplice da usare rispetto all'entità Program nelle API v2. 
* Supporto RTMP migliorato (maggiore stabilità e più supporto per i codificatori di origine).
* Inserimento RTMPS sicuro.<br/>Quando si crea un evento live, si ottengono 4 URL di inserimento. pressoché identici: hanno lo stesso token di streaming (AppId) e solo la parte del numero di porta è diversa. Due URL sono primari e due sono di backup per RTMPS.   
* Controllo degli accessi in base al ruolo sulle entità. 

## <a name="changes-from-v2"></a>Modifiche rispetto a v2

* Per gli asset creati con V3, servizi multimediali supporta solo la [crittografia di archiviazione lato server di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * È possibile usare le API v3 con gli asset creati con le API v2 che dispongono di [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) (AES 256) offerta da Servizi multimediali.
    * Non è possibile usare le API v3 per creare nuovi asset con [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) AES 256.
* Le proprietà dell' [Asset](assets-concept.md)in V3 sono diverse da V2, vedere [come viene mappata la proprietà](assets-concept.md#map-v3-asset-properties-to-v2).
* Gli SDK v3 di Servizi multimediali sono ora separati da Storage SDK, che conferisce maggiore controllo sulla versione di Storage SDK che si intende usare ed evitare problemi di controllo delle versioni. 
* Nelle API v3 tutte le velocità in bit di codifica sono in bit al secondo. Questo comportamento è diverso dai set di impostazioni di Media Encoder Standard v2. Ad esempio, la velocità in bit che in v2 viene specificata come 128 (kbps), nella versione v3 sarà 128000 (bit/secondo). 
* Le entità AssetFiles, AccessPolicies e IngestManifests non esistono in v3.
* La proprietà IAsset.ParentAssets non esiste nella versione 3.
* ContentKey non è più un'entità, ma una proprietà del localizzatore di streaming.
* Il supporto della griglia di eventi sostituisce NotificationEndpoints.
* Le entità seguenti sono state rinominate
    * Job Output sostituisce Task e fa ora parte di un processo.
    * Streaming Locator sostituisce Locator.
    * Live Event sostituisce Channel.<br/>La fatturazione degli eventi live si basa sui contatori dei canali live. Per altre informazioni, vedere [fatturazione](live-event-states-billing.md) e [prezzi](https://azure.microsoft.com/pricing/details/media-services/).
    * Live Output sostituisce Program.
* Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. I programmi funzionano in modo diverso nelle API v2 e devono essere avviati dopo la creazione.
* Per ottenere informazioni su un processo, è necessario conoscere il nome della trasformazione in cui è stato creato il processo. 
* Nella versione V2, i file di metadati di [input](../previous/media-services-input-metadata-schema.md) e [output](../previous/media-services-output-metadata-schema.md) XML vengono generati come risultato di un processo di codifica. In V3, il formato dei metadati è stato modificato da XML a JSON. 

> [!NOTE]
> Esaminare le convenzioni di denominazione applicate alle [risorse di servizi multimediali V3](media-services-apis-overview.md#naming-conventions). Esaminare anche i [BLOB di denominazione](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Gap di funzionalità rispetto alle API v2

L'API v3 include i gap di funzionalità seguenti rispetto all'API v2. È in corso la risoluzione di questi gap.

* Il [codificatore Premium](../previous/media-services-premium-workflow-encoder-formats.md) e i [processori di Analisi Servizi multimediali](../previous/media-services-analytics-overview.md) legacy (Azure Media Services Indexer 2 Preview, lo strumento di offuscamento dei volti e così via) non sono accessibili tramite v3.<br/>I clienti che intendo eseguire la migrazione da Media Indexer 1 o 2 Preview possono usare immediatamente il set di impostazioni di AudioAnalyzer nell'API v3.  Questo nuovo set di impostazioni contiene maggiori funzionalità rispetto a Media Indexer 1 o 2 (meno recente). 
* Molte delle [funzionalità avanzate della media encoder standard nelle API v2](../previous/media-services-advanced-encoding-with-mes.md) non sono attualmente disponibili in V3, ad esempio:
  
    * Unione degli asset
    * Sovrimpressioni
    * Ritaglio
    * Sprite di anteprima
    * Inserimento di una traccia audio invisibile all'utente quando l'input non ha audio
    * Inserimento di una traccia video quando l'input non ha video
* Attualmente gli eventi live con transcodifica non supportano l'inserimento di slate nel corso del flusso e l'inserimento di marcatori di annunci tramite chiamata API. 

> [!NOTE]
> Aggiungere questo articolo ai Preferiti e consultarlo periodicamente per eventuali aggiornamenti.
 
## <a name="code-differences"></a>Differenze a livello di codice

La tabella seguente illustra le differenze a livello di codice tra v2 e v3 per scenari comuni.

|Scenario|API V2|API V3|
|---|---|---|
|Creare un asset e caricare un file |[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Inviare un processo|[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra come creare una trasformazione e quindi inviare un processo.|
|Pubblicare un asset con crittografia AES |1. creare ContentKeyAuthorizationPolicyOption<br/>2. creare ContentKeyAuthorizationPolicy<br/>3. creare AssetDeliveryPolicy<br/>4. creare asset e caricare il contenuto o inviare un processo e usare l'asset di output<br/>5. associare AssetDeliveryPolicy a Asset<br/>6. creare ContentKey<br/>7. connessione di ContentKey all'asset<br/>8. creare AccessPolicy<br/>9. creare un localizzatore<br/><br/>[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. creare un criterio della chiave simmetrica<br/>2. crea asset<br/>3. caricare il contenuto o usare asset come JobOutput<br/>4. creare un localizzatore di streaming<br/><br/>[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Ottenere i dettagli del processo e gestire i processi |[Gestire i processi con V2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gestire i processi con V3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Problemi noti

* Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-sdk), l'interfaccia della riga di comando o uno degli SDK supportati.
* È necessario effettuare il provisioning di unità riservate di codifica nell'account per controllare la concorrenza e le prestazioni dei processi, in particolare quelli che includono l'analisi audio o video. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md). È possibile gestire le unità riservate di codifica usando l'[interfaccia della riga di comando 2.0 per Servizi multimediali v3](media-reserved-units-cli-how-to.md), il [portale di Azure](../previous/media-services-portal-scale-media-processing.md) o le [API v2](../previous/media-services-dotnet-encoding-units.md). È necessario effettuare il provisioning delle unità riservate di codifica, indipendentemente dall'uso di Servizi multimediali v2 o delle API v3.
* Le entità di Servizi multimediali create con l'API v3 non possono essere gestite dall'API v2.  
* Non è consigliabile gestire con le API v3 le entità create con le API v2. Di seguito sono riportati esempi delle differenze che rendono incompatibili le entità nelle due versioni:   
    * I processi e le attività creati in v2 non vengono visualizzati in v3 in quanto non sono associati a una trasformazione. È consigliabile passare a trasformazioni e processi v3. Per un periodo di tempo relativamente breve è necessario monitorare i processi v2 in fase di elaborazione durante il passaggio.
    * I canali e i programmi creati con v2 (corrispondenti a eventi live e output live in v3) non possono continuare a essere gestiti con v3. È consigliabile passare agli eventi live e agli output live v3 in corrispondenza di un arresto canale pratico.<br/>Non è attualmente possibile eseguire la migrazione di canali in continua esecuzione.  

> [!NOTE]
> Questa pagina viene aggiornata man mano che il team di Servizi multimediali apporta costanti miglioramenti alle API v3 e colma le lacune tra le versioni.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire quanto è semplice eseguire la codifica e lo streaming di file video, vedere l'articolo relativo allo [streaming di file](stream-files-dotnet-quickstart.md). 

