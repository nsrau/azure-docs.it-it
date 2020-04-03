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
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582683"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per creare un URL, è necessario concatenare il nome host dell'endpoint di streaming e il percorso del localizzatore di streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore** di streaming è denominato pubblicazione. Per impostazione predefinita, **il localizzatore** di flusso è valido immediatamente dopo aver eseguito le chiamate API e dura fino a quando non viene eliminato, a meno che non si configurino l'ora di inizio e di fine facoltativa. 

Quando si crea un **localizzatore**di streaming , è necessario specificare un nome **di asset** e un nome di criteri di **streaming.** Per altre informazioni, vedere gli argomenti seguenti:

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md)

Puoi anche specificare l'ora di inizio e di fine sul localizzatore di streaming, che consentirà solo all'utente di riprodurre il contenuto tra questi orari (ad esempio, tra il 01/05 e il 2019 e il 5/5/2019).  

## <a name="considerations"></a>Considerazioni

* **I localizzatori** di streaming non sono aggiornabili. 
* Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
* È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per ulteriori informazioni, vedere [Quote e limiti](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Creazione di localizzatori di streaming  

### <a name="not-encrypted"></a>Non crittografato

Se si desidera trasmettere il file in-the-clear (non crittografato), impostare i criteri di streaming chiaro predefiniti: su 'Predefined_ClearStreamingOnly' (in .NET, è possibile utilizzare l'enumerazione PredefinedStreamingPolicy.ClearStreamingOnly).

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

Se è necessario crittografare il contenuto con la crittografia CENC, impostare il criterio su "Predefined_MultiDrmCencStreaming". La crittografia Widevine verrà applicata a un flusso DASH e PlayReady a Smooth. La chiave verrà recapitata a un client di riproduzione in base alle licenze DRM configurate.

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

Se vuoi anche crittografare il tuo flusso HLS con CBCS (FairPlay), usa 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associare filtri ai localizzatori di flusso

Consultate [Filtri: associati ai localizzatori](filters-concept.md#associating-filters-with-streaming-locator)di streaming.

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrare, ordinare, pagina Streaming Locator entità

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Elenca i localizzatori di streaming in base al nome dell'assetList Streaming Locators by Asset name

Per ottenere i localizzatori di streaming in base al nome dell'asset associato, usa le operazioni seguenti:

|Linguaggio|API|
|---|---|
|REST|[liststreaminglocatori](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Vedere anche

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md)
* [Esercitazione: Caricare, codificare e riprodurre video in streaming con .NETTutorial: Upload, encode, and stream videos using .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Passaggi successivi

[Come creare un localizzatore di streaming e creare URL](create-streaming-locator-build-url.md)
