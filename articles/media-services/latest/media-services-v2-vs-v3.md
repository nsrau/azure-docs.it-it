---
title: Eseguire la migrazione da Servizi multimediali di Azure versione 2 a v3Migrate from Azure Media Services v2 to v3
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087818"
---
# <a name="media-services-v2-vs-v3"></a>Servizi multimediali v2 e v3

In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni.

## <a name="general-changes-from-v2"></a>Modifiche generali rispetto a v2

* Per gli asset creati con v3, Servizi multimediali supporta solo la crittografia di [archiviazione lato server](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)di Archiviazione server di Azure.
    * È possibile usare le API v3 con gli asset creati con le API v2 che dispongono di [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) (AES 256) offerta da Servizi multimediali.
    * Non è possibile usare le API v3 per creare nuovi asset con [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) AES 256.
* Le proprietà [dell'asset](assets-concept.md)nella v3 differiscono da v2, vedere [come mappare le proprietà](#map-v3-asset-properties-to-v2).
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
* Per ottenere informazioni su un processo, è necessario conoscere il nome della trasformazione con cui è stato creato il processo. 
* Nella versione 2 i file di [metadati](../previous/media-services-input-metadata-schema.md) di input e [output](../previous/media-services-output-metadata-schema.md) XML vengono generati come risultato di un processo di codifica. Nella versione 3 il formato dei metadati è cambiato da XML a JSON. 
* In Servizi multimediali v2 è possibile specificare il vettore di inizializzazione (IV). In Servizi multimediali v3, non è possibile specificare FairPlay IV. Anche se non influisce sui clienti che utilizzano Servizi multimediali sia per la creazione di pacchetti che per la distribuzione delle licenze, può trattarsi di un problema quando si utilizza un sistema DRM di terze parti per fornire le licenze FairPlay (modalità ibrida). In tal caso, è importante sapere che FairPlay IV è derivato dall'ID chiave cbcs e può essere recuperato utilizzando questa formula:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    con

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Per altre informazioni, vedere il [codice di Funzioni di Azure per Servizi multimediali v3 in modalità ibrida per le operazioni Live e VOD.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)
 
> [!NOTE]
> Esaminare le convenzioni di denominazione applicate alle risorse di [Servizi multimediali v3.](media-services-apis-overview.md#naming-conventions) Esaminare anche [la denominazione dei BLOB](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Gap di funzionalità rispetto alle API v2

L'API v3 include i gap di funzionalità seguenti rispetto all'API v2. È in corso la risoluzione di questi gap.

* Il [codificatore Premium](../previous/media-services-premium-workflow-encoder-formats.md) e i [processori di Analisi Servizi multimediali](../previous/media-services-analytics-overview.md) legacy (Azure Media Services Indexer 2 Preview, lo strumento di offuscamento dei volti e così via) non sono accessibili tramite v3.<br/>I clienti che intendo eseguire la migrazione da Media Indexer 1 o 2 Preview possono usare immediatamente il set di impostazioni di AudioAnalyzer nell'API v3.  Questo nuovo set di impostazioni contiene maggiori funzionalità rispetto a Media Indexer 1 o 2 (meno recente). 
* Molte delle funzionalità avanzate di Media Encoder Standard nelle API [v2](../previous/media-services-advanced-encoding-with-mes.md) non sono attualmente disponibili nella v3, ad esempio:
  
    * Unione degli asset
    * Sovrimpressioni
    * Ritaglio
    * Sprite di anteprima
    * Inserimento di una traccia audio silenziosa quando l'ingresso non ha audio
    * Inserimento di una traccia video quando l'input non contiene video
* Attualmente gli eventi live con transcodifica non supportano l'inserimento di slate nel corso del flusso e l'inserimento di marcatori di annunci tramite chiamata API. 
 
## <a name="asset-specific-changes"></a>Modifiche specifiche delle risorse

### <a name="map-v3-asset-properties-to-v2"></a>Mappare le proprietà delle risorse v3 alla versione 2

Nella tabella seguente viene illustrato il mapping delle proprietà [dell'asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)in v3 alle proprietà dell'asset nella versione 2.

|proprietà v3|proprietà v2|
|---|---|
|`id`- (unico) il percorso completo di Azure Resource Manager, vedere gli esempi in Asset-(unique) the full Azure Resource Manager path, see examples in [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (unico) vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- valore (univoco) `nb:cid:UUID:` inizia con il prefisso.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(opzioni di creazione)|
|`type`||

### <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali:

|Opzione di crittografia|Descrizione|Servizi multimediali v2|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali|Crittografia AES-256, chiave gestita da Servizi multimediali.|Supportata<sup>(1)</sup>|Non supportata<sup>(2)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente.|Supportato|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta dall'archiviazione di Azure, chiave gestita dal cliente in Key Vault.Client-side encryption offered by Azure storage, key managed by customer in Key Vault.|Non supportate|Non supportate|

<sup>1</sup> Mentre Servizi multimediali supporta la gestione del contenuto in modo chiaro/senza alcuna forma di crittografia, questa operazione non è consigliata.

<sup>2</sup> In Servizi multimediali versione 3, la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. Significato v3 funziona con le risorse crittografate di archiviazione esistenti, ma non consente la creazione di nuovi.

## <a name="code-differences"></a>Differenze a livello di codice

La tabella seguente illustra le differenze a livello di codice tra v2 e v3 per scenari comuni.

|Scenario|API V2|API V3|
|---|---|---|
|Creare un asset e caricare un file |[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Inviare un processo|[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra come creare una trasformazione e quindi inviare un processo.|
|Pubblicare un asset con crittografia AES |1. Creare ContentKeyAuthorizationPolicyOption<br/>2. Creare ContentKeyAuthorizationPolicy<br/>3. Creare AssetDeliveryPolicy<br/>4. Creare asset e caricare contenuti OPPURE Invia lavoro e utilizza asset di output<br/>5. Associare AssetDeliveryPolicy a Asset<br/>6. Creare ContentKey<br/>7. Allega ContentKey all'asset<br/>8. Creare AccessPolicy<br/>9. Crea localizzatore<br/><br/>[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Creare criteri chiave di contenuto<br/>2. Crea risorsa<br/>3. Caricare contenuti o utilizzare Asset come JobOutput<br/>4. Creare Streaming Locator<br/><br/>[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Ottenere i dettagli del processo e gestire i processi |[Gestire i processi con v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gestire i processi con v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Aggiungere questo articolo ai Preferiti e consultarlo periodicamente per eventuali aggiornamenti.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md)
