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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: cb34855fc9451679c885eebb0ef5a2fab0be8c57
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086846"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere disponibile sui client un URL da usare per riprodurre file audio o video codificati, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e compilare gli URL di streaming. Per altre informazioni, vedere [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definizione di StreamingLocator

Nella tabella seguente sono illustrate le proprietà dei localizzatori di streaming e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|id |ID di risorsa completo per la risorsa.|
|name   |Nome della risorsa.|
|properties.alternativeMediaId|ID supporto alternativo di questo localizzatore di streaming.|
|properties.assetName   |Nome dell'asset|
|properties.contentKeys |Gli elementi ContentKey usati da questo localizzatore di streaming.|
|properties.created |Ora di creazione del localizzatore di streaming.|
|properties.defaultContentKeyPolicyName|Nome dell'oggetto ContentKeyPolicy predefinito usato da questo localizzatore di streaming.|
|properties.endTime |Ora di fine del localizzatore di streaming.|
|properties.startTime|Ora di inizio del localizzatore di streaming.|
|properties.streamingLocatorId|Elemento StreamingLocatorId del localizzatore di streaming.|
|properties.streamingPolicyName |Nome dei criteri di streaming usati da questo localizzatore di streaming. Specificare il nome dei criteri di streaming creati oppure usare uno dei criteri di streaming predefiniti. I criteri di streaming predefiniti disponibili sono: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" e "Predefined_MultiDrmStreaming".|
|type|Tipo di risorsa.|

Per la definizione completa, vedere [Localizzatori di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Servizi multimediali supporta le opzioni di query OData seguenti per i localizzatori di streaming: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Descrizione dell'operatore:

* Eq = uguale a
* Ne = diverso da
* Ge = maggiore o uguale a
* Le = minore o uguale a
* Gt = maggiore di
* Lt = minore di

### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei localizzatori di streaming: 

|NOME|Filtro|Ordine|
|---|---|---|
|id |||
|name|Eq, ne, ge, le, gt, lt|Crescente e decrescente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq, ne, ge, le,  gt, lt|Crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|Crescente e decrescente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. 

Se i localizzatori di streaming vengono creati o eliminati durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

Il seguente esempio in C# illustra come enumerare tutti i localizzatori di streaming nell'account.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Per esempi REST, vedere [Localizzatori di streaming - Elenco](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
