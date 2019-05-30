---
title: Localizzatori di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei localizzatori di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299157"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per compilare un URL, è necessario concatenare il nome host dell'Endpoint di Streaming e il percorso del localizzatore di Streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea una **localizzatore di Streaming**, è necessario specificare un' **Asset** nome e un **Streaming criteri** nome. Per altre informazioni, vedere i seguenti argomenti:

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md)

È anche possibile specificare l'ora di inizio e fine il localizzatore di Streaming, che consentirà solo l'utente riprodurre il contenuto di questo intervallo di tempo (ad esempio, compreso tra 5/1/2019 per 5/5/2019).  

## <a name="considerations"></a>Considerazioni

* **I localizzatori di streaming** non sono aggiornabili. 
* Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
* È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Creare i localizzatori di Streaming  

### <a name="not-encrypted"></a>Non crittografato

Se si vuole trasmettere il file in-the-clear (non crittografati), impostare i criteri predefiniti di streaming chiaro: a 'Predefined_ClearStreamingOnly' (in .NET, è possibile usare l'enumerazione PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Crittografato 

Se è necessario crittografare i contenuti con la crittografia CENC, impostare il criterio 'Predefined_MultiDrmCencStreaming'. La crittografia Widevine verrà applicata a un flusso DASH e PlayReady a Smooth. La chiave venga recapitata a un client di riproduzione di licenze DRM configurate in base.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se si vuole crittografare anche il flusso HLS con CBCS (FairPlay), usare 'Predefined_MultiDrmStreaming'.

## <a name="associate-filters-with-streaming-locators"></a>Associare i filtri con i localizzatori di Streaming

Visualizzare [filtri: associare con i localizzatori di Streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtro, ordine, le entità del localizzatore di Streaming di pagina

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Localizzatori di Streaming di elenco in base al nome di Asset

Per ottenere i localizzatori di Streaming in base al nome di Asset associato, usare le operazioni seguenti:

|Linguaggio|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Vedere anche

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md)

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video con .NET](stream-files-tutorial-with-api.md)
