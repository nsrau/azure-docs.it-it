---
title: Eseguire la migrazione da servizi multimediali di Azure V2 a V3
description: In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: be0c12eacae9bb13a475de4634746e9d38d35e43
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267565"
---
# <a name="media-services-v2-vs-v3"></a>Media Services V2 e V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni.

## <a name="general-changes-from-v2"></a>Modifiche generali da V2

* Per gli asset creati con V3, servizi multimediali supporta solo la [crittografia di archiviazione lato server di archiviazione di Azure](../../storage/common/storage-service-encryption.md).
    * È possibile usare le API v3 con gli asset creati con le API v2 che dispongono di [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) (AES 256) offerta da Servizi multimediali.
    * Non è possibile usare le API v3 per creare nuovi asset con [crittografia di archiviazione](../previous/media-services-rest-storage-encryption.md) AES 256.
* Le proprietà dell' [Asset](assets-concept.md)in V3 sono diverse da V2, vedere [come viene mappata la proprietà](#map-v3-asset-properties-to-v2).
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
* In servizi multimediali V2 è possibile specificare il vettore di inizializzazione (IV). In servizi multimediali V3 non è possibile specificare FairPlay IV. Anche se non ha alcun effetto sui clienti che usano servizi multimediali per la creazione di pacchetti e la distribuzione di licenze, può trattarsi di un problema quando si usa un sistema DRM di terze parti per distribuire le licenze FairPlay (modalità ibrida). In tal caso, è importante tenere presente che FairPlay IV è derivato dall'ID della chiave CBCS e può essere recuperato utilizzando questa formula:

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

    Per altre informazioni, vedere il [codice C# di funzioni di Azure per servizi multimediali V3 in modalità ibrida per le operazioni Live e VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Esaminare le convenzioni di denominazione applicate alle [risorse di servizi multimediali V3](media-services-apis-overview.md#naming-conventions). Esaminare anche i [BLOB di denominazione](assets-concept.md#naming).

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
* Vedere `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` il codice di esempio per le procedure consigliate e i modelli relativi all'uso dell'API REST V2 in. SDK di NETCore.

## <a name="asset-specific-changes"></a>Modifiche specifiche dell'asset

### <a name="map-v3-asset-properties-to-v2"></a>Mappare le proprietà Asset V3 alla versione V2

La tabella seguente illustra in che modo le proprietà dell' [Asset](/rest/api/media/assets/createorupdate#asset)in V3 sono mappate alle proprietà dell'asset nella versione V2.

|Proprietà V3|V2 (proprietà)|
|---|---|
|`id` -(univoco) il percorso di Azure Resource Manager completo, vedere esempi nell' [Asset](/rest/api/media/assets/createorupdate)||
|`name` -(univoco) vedere [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(Unique) il valore inizia con il `nb:cid:UUID:` prefisso.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opzioni di creazione)|
|`type`||

### <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali:

|Opzione di crittografia|Descrizione|Servizi multimediali v2|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali|Crittografia AES-256, chiave gestita da servizi multimediali.|Supportata<sup>(1)</sup>|Non supportata<sup>(2)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](../../storage/common/storage-service-encryption.md)|Crittografia lato server offerta da archiviazione di Azure, chiave gestita da Azure o dal cliente.|Supportato|Supportato|
|[Crittografia lato client di archiviazione](../../storage/common/storage-client-side-encryption.md)|Crittografia lato client offerta da archiviazione di Azure, la chiave gestita dal cliente in Key Vault.|Non supportato|Non supportato|

<sup>1</sup> anche se servizi multimediali supporta la gestione del contenuto in chiaro o senza alcuna forma di crittografia, questa operazione non è consigliata.

<sup>2</sup> In Servizi multimediali versione 3, la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. Il significato di V3 funziona con asset crittografati di archiviazione esistenti ma non consente la creazione di nuove risorse.

## <a name="code-differences"></a>Differenze a livello di codice

La tabella seguente illustra le differenze a livello di codice tra v2 e v3 per scenari comuni.

|Scenario|API V2|API V3|
|---|---|---|
|Creare un asset e caricare un file |[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Inviare un processo|[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra come creare una trasformazione e quindi inviare un processo.|
|Pubblicare un asset con crittografia AES |1. creare ContentKeyAuthorizationPolicyOption<br/>2. creare ContentKeyAuthorizationPolicy<br/>3. creare AssetDeliveryPolicy<br/>4. creare asset e caricare il contenuto o inviare un processo e usare l'asset di output<br/>5. associare AssetDeliveryPolicy a Asset<br/>6. creare ContentKey<br/>7. connessione di ContentKey all'asset<br/>8. creare AccessPolicy<br/>9. creare un localizzatore<br/><br/>[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. creare un criterio della chiave simmetrica<br/>2. crea asset<br/>3. caricare il contenuto o usare asset come JobOutput<br/>4. creare un localizzatore di streaming<br/><br/>[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Ottenere i dettagli del processo e gestire i processi |[Gestire i processi con V2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gestire i processi con V3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Aggiungere questo articolo ai Preferiti e consultarlo periodicamente per eventuali aggiornamenti.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md)
