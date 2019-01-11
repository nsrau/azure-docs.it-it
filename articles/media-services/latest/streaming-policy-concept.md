---
title: Criteri di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei criteri di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/22/2018
ms.author: juliako
ms.openlocfilehash: d74ce913a2189dd1062b30f9def919cbbabe7b64
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742525"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i criteri di streaming consentono di definire protocolli di streaming e opzioni di crittografia per StreamingLocators. È possibile assegnare un nome ai criteri di streaming creati oppure usare uno dei criteri di streaming predefiniti. I criteri di streaming predefiniti attualmente disponibili sono: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> Se si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare gli stessi protocolli e le stesse opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di criteri di streaming. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.

## <a name="streamingpolicy-definition"></a>Definizione di "criteri di streaming"

Nella tabella seguente vengono illustrate le proprietà dei criteri di streaming e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|id|ID di risorsa completo per la risorsa.|
|name|Nome della risorsa.|
|properties.commonEncryptionCbcs|Configurazione di CommonEncryptionCbcs|
|properties.commonEncryptionCenc|Configurazione di CommonEncryptionCenc|
|properties.created |Ora di creazione dei criteri di streaming|
|properties.defaultContentKeyPolicyName |ContentKey predefinita usata dai criteri di streaming correnti|
|properties.envelopeEncryption  |Configurazione di EnvelopeEncryption|
|properties.noEncryption|Configurazione di NoEncryption|
|type|Tipo di risorsa.|

Per la definizione completa, vedere [Criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Servizi multimediali supporta le opzioni di query OData seguenti per i criteri di streaming: 

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

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei criteri di streaming: 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|Eq, ne, ge, le, gt, lt|Crescente e decrescente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |Eq, ne, ge, le,  gt, lt|Crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. 

Se i criteri di streaming vengono creati o eliminati durante il paging della raccolta, le modifiche si riflettono nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

L'esempio C# seguente illustra come enumerare tutti i criteri di streaming nell'account.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Per esempi REST, vedere [Criteri di streaming - Elenco](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
