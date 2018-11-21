---
title: Eseguire la migrazione di Servizi multimediali di Azure dalla versione 2 alla versione 3 | Microsoft Docs
description: In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni.
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
ms.date: 11/07/2018
ms.author: juliako
ms.openlocfilehash: 8c3ff4af3b556614d0b2179dceed6cabd9cbabff
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616011"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3

Questo articolo descrive le modifiche introdotte in Servizi multimediali di Azure v3, illustra le differenze tra le due versioni e offre materiale sussidiario sulla migrazione.

In caso di sviluppo di un servizio video basato sulle [API legacy di Servizi multimediali v2](../previous/media-services-overview.md), è necessario rivedere le linee guida e considerazioni seguenti prima della migrazione alle API v3. L'API v3 include numerosi vantaggi e nuove funzionalità che migliorano l'esperienza di sviluppo e le funzionalità di Servizi multimediali. Tuttavia, come definito nella sezione [Problemi noti](#known-issues) di questo articolo, sussistono anche alcune limitazioni a causa di modifiche tra le versioni delle API. Questa pagina viene aggiornata man mano che il team di Servizi multimediali apporta costanti miglioramenti alle API v3 e colma le lacune tra le versioni. 

## <a name="benefits-of-media-services-v3"></a>Vantaggi di Servizi multimediali v3

### <a name="api-is-more-approachable"></a>API più accessibile

*  v3 si basa su una superficie API unificata, che espone funzionalità operative e di gestione basate su Azure Resource Manager. I modelli di Azure Resource Manager possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, LiveEvent e altro ancora.
* Documento di [specifica OpenAPI (noto anche come Swagger)](https://aka.ms/ams-v3-rest-sdk).
    Espone lo schema per tutti i componenti del servizio, tra cui la codifica basata su file.
* SDK disponibili per [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) e Ruby.
* Integrazione dell'[interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli-ref) per semplificare il supporto di scripting.

### <a name="new-features"></a>Nuove funzionalità

* Per l'elaborazione di processi basata su file è possibile usare un URL HTTP(S) come input.
    Non è necessario avere contenuto già archiviato in Azure né creare asset.
* Introduce il concetto di [trasformazioni](transforms-jobs-concept.md) per l'elaborazione di processi basata su file. Una trasformazione consente di creare configurazioni riutilizzabili, creare modelli di Azure Resource Manager e isolare le impostazioni di elaborazione tra più clienti o tenant.
* Un asset può avere [più StreamingLocator](streaming-locators-concept.md) con diverse impostazioni di creazione dinamica dei pacchetti e crittografia dinamica.
* La [protezione del contenuto](content-key-policy-concept.md) supporta le funzionalità con più chiavi.
* È possibile trasmettere eventi live per una durata massima di 24 ore quando si usa Servizi multimediali per la transcodifica di un feed di contributi a bitrate singolo in un flusso di output a bitrate multipli.
* Nuovo supporto per streaming live a bassa latenza nelle entità LiveEvent.
* L'anteprima di LiveEvent supporta la creazione dinamica dei pacchetti e la crittografia dinamica. Questo consente la protezione del contenuto nell'anteprima, nonché la creazione di pacchetti DASH e HLS.
* L'entità LiveOuput è più semplice da usare rispetto all'entità Program nelle API v2. 
* Controllo degli accessi in base al ruolo sulle entità. 

## <a name="changes-from-v2"></a>Modifiche rispetto a v2

* Per gli asset creati con v3, Servizi multimediali supporta solo la [crittografia di archiviazione lato server di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * È possibile usare le API v3 con gli asset creati con le API v2 che dispongono di [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) (AES 256) offerta da Servizi multimediali.
    * Non è possibile usare le API v3 per creare nuovi asset con [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) AES 256.
* Gli SDK v3 di Servizi multimediali sono ora separati da Storage SDK, che conferisce maggiore controllo sulla versione di Storage SDK che si intende usare ed evitare problemi di controllo delle versioni. 
* Nelle API v3 tutte le velocità in bit di codifica sono in bit al secondo. Questo comportamento è diverso dai set di impostazioni di Media Encoder Standard v2. Ad esempio, la velocità in bit che in v2 viene specificata come 128 (kbps), nella versione v3 sarà 128000 (bit/secondo). 
* Le entità AssetFiles, AccessPolicies e IngestManifests non esistono in v3.
* La proprietà IAsset.ParentAssets non esiste nella versione 3.
* ContentKey non è più un'entità, ma una proprietà di StreamingLocator.
* Il supporto della griglia di eventi sostituisce NotificationEndpoints.
* Le entità seguenti sono state rinominate
    * JobOutput sostituisce Task e fa ora parte di un processo.
    * StreamingLocator sostituisce Locator.
    * LiveEvent sostituisce Channel.
        
        La fatturazione delle entità LiveEvent si basa sui contatori dei canali live. Per altre informazioni, vedere la [panoramica dello streaming live](live-streaming-overview.md#billing) e i [prezzi](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput sostituisce Program.
* Le entità LiveOutput non devono essere necessariamente avviate in modo esplicito, ma vengono avviate alla creazione e arrestate durante l'eliminazione. I programmi funzionano in modo diverso nelle API v2 e devono essere avviati dopo la creazione.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Gap di funzionalità rispetto alle API v2

L'API v3 include i gap di funzionalità seguenti rispetto all'API v2. È in corso la risoluzione di questi gap.

* Il [codificatore Premium](../previous/media-services-premium-workflow-encoder-formats.md) e i [processori di Analisi Servizi multimediali](../previous/media-services-analytics-overview.md) legacy (Azure Media Services Indexer 2 Preview, lo strumento di offuscamento dei volti e così via) non sono accessibili tramite v3.

    I clienti che intendo eseguire la migrazione da Media Indexer 1 o 2 Preview possono usare immediatamente il set di impostazioni di AudioAnalyzer nell'API v3.  Questo nuovo set di impostazioni contiene maggiori funzionalità rispetto a Media Indexer 1 o 2 (meno recente). 

* Molte funzionalità avanzate di Media Encoder Standard nelle API v2 non sono attualmente disponibili in v3, ad esempio:
    * Ritaglio (per gli scenari live e on demand)
    * Unione degli asset
    * Sovrimpressioni
    * Ritaglio
    * Sprite di anteprima
* Attualmente le entità LiveEvent con transcodifica non supportano l'inserimento di slate nel corso del flusso, set di impostazioni personalizzati o l'inserimento di marcatori di annunci tramite chiamata API. 

> [!NOTE]
> Aggiungere questo articolo ai Preferiti e consultarlo periodicamente per eventuali aggiornamenti.

## <a name="code-differences"></a>Differenze a livello di codice

La tabella seguente illustra le differenze a livello di codice tra v2 e v3 per scenari comuni.

|Scenario|API V2|API V3|
|---|---|---|
|Creare un asset e caricare un file |[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Inviare un processo|[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra come creare una trasformazione e quindi inviare un processo.|
|Pubblicare un asset con crittografia AES |1. Creare ContentKeyAuthorizationPolicyOption<br/>2. Creare ContentKeyAuthorizationPolicy<br/>3. Creare AssetDeliveryPolicy<br/>4. Creare asset e caricare contenuto o inviare processi e usare asset di output<br/>5. Associare AssetDeliveryPolicy all'asset<br/>6. Creare ContentKey<br/>7. Collegare ContentKey all'asset<br/>8. Creare AccessPolicy<br/>9. Creare Locator<br/><br/>[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Creare criteri di chiave simmetrica<br/>2. Creare asset<br/>3. Caricare contenuto o usare asset come JobOutput<br/>4. Creare un oggetto StreamingLocator<br/><br/>[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Problemi noti

* Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-sdk), l'interfaccia della riga di comando o uno degli SDK supportati.
* È oggi possibile gestire Media Reserved Units (S1/S2/S3) solo mediante l'API di Servizi multimediali v2. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md).
* Le entità di Servizi multimediali create con l'API v3 non possono essere gestite dall'API v2.  
* Non è consigliabile gestire con le API v3 le entità create con le API v2. Di seguito sono riportati esempi delle differenze che rendono incompatibili le entità nelle due versioni:   
    * I processi e le attività creati in v2 non vengono visualizzati in v3 in quanto non sono associati a una trasformazione. È consigliabile passare a trasformazioni e processi v3. Per un periodo di tempo relativamente breve è necessario monitorare i processi v2 in fase di elaborazione durante il passaggio.
    * I canali e i programmi creati con v2 (sottoposti a mapping a entità LiveEvent e LiveOutput in v3) non possono continuare a essere gestiti con v3. È consigliabile passare alle entità LiveEvent e LiveOutput v3 in corrispondenza di un arresto canale pratico.
    
        Non è attualmente possibile eseguire la migrazione di canali in continua esecuzione.  
> [!NOTE]
> Aggiungere questo articolo ai Preferiti e consultarlo periodicamente per eventuali aggiornamenti.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire quanto è semplice eseguire la codifica e lo streaming di file video, vedere l'articolo relativo allo [streaming di file](stream-files-dotnet-quickstart.md). 

