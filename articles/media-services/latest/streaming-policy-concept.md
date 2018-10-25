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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 118660e8947663328554ac0116b5519267197336
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984299"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i criteri di streaming consentono di definire protocolli di streaming e opzioni di crittografia per StreamingLocators. È possibile assegnare un nome ai criteri di streaming creati oppure usare uno dei criteri di streaming predefiniti. I criteri di streaming predefiniti attualmente disponibili sono: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> Quando si usa un oggetto [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizzato, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusarli per gli oggetti StreamingLocator ogni volta che si devono usare gli stessi protocolli e opzioni di crittografia. L'account di Servizi multimediali prevede una quota per il numero di occorrenze di StreamingPolicy. Evitare quindi di creare un nuovo oggetto StreamingPolicy per ogni StreamingLocator.

## <a name="streamingpolicy-definition"></a>Definizione di "criteri di streaming"

Nella tabella seguente vengono illustrate le proprietà dei criteri di streaming e le relative definizioni.

|NOME|type|DESCRIZIONE|
|---|---|---|
|id|stringa|ID di risorsa completo per la risorsa.|
|name|stringa|Nome della risorsa.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs|Configurazione di CommonEncryptionCbcs|
|properties.commonEncryptionCenc|CommonEncryptionCenc|Configurazione di CommonEncryptionCenc|
|properties.created |stringa|Ora di creazione dei criteri di streaming|
|properties.defaultContentKeyPolicyName |stringa|ContentKey predefinita usata dai criteri di streaming correnti|
|properties.envelopeEncryption  |EnvelopeEncryption|Configurazione di EnvelopeEncryption|
|properties.noEncryption|NoEncryption|Configurazione di NoEncryption|
|type|stringa|Tipo di risorsa.|

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

Se i criteri di streaming vengono creati o eliminati durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

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
