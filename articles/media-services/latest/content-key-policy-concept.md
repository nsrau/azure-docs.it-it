---
title: Criteri di chiave simmetrica in Servizi multimediali - Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei criteri di chiave simmetrica e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f12632b20d516c81e21a50cfdda7e40d4163afc1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742219"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

In Servizi multimediali di Azure v3 i [criteri di chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies) consentono di specificare le modalità di recapito della chiave simmetrica ai client finali usando il componente di distribuzione delle chiavi di Servizi multimediali. Per altre informazioni, vedere [Panoramica della protezione del contenuto](content-protection-overview.md).

È consigliabile usare gli stessi criteri di chiave simmetrica per tutti gli asset. I criteri di chiave simmetrica possono essere aggiornati. Pertanto, se si vuole eseguire una rotazione delle chiavi, è possibile aggiungere una nuova opzione ai criteri di chiave simmetrica esistenti con una restrizione di token con le nuove chiavi oppure è possibile aggiornare la chiave di verifica primaria e l'elenco delle chiavi di verifica alternative nei criteri e nell'opzione esistenti. Per l'aggiornamento e la selezione dei criteri aggiornati le cache di distribuzione delle chiavi possono impiegare al massimo 15 minuti.

## <a name="contentkeypolicy-definition"></a>Definizione dei criteri di chiave simmetrica

Nella tabella seguente vengono illustrate le proprietà dei criteri di chiave simmetrica e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|id|ID di risorsa completo per la risorsa.|
|name|Nome della risorsa.|
|properties.created |Data di creazione dei criteri|
|properties.description |Descrizione dei criteri.|
|properties.lastModified|Data dell'ultima modifica dei criteri|
|properties.options |Le opzioni dei criteri di chiave.|
|properties.policyId|L'ID dei criteri legacy.|
|type|Tipo di risorsa.|

Per la definizione completa, vedere [Criteri di chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Servizi multimediali supporta le opzioni di query OData seguenti per i criteri di chiave simmetrica: 

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

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei criteri di chiave simmetrica: 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|Eq, ne, ge, le, gt, lt|Crescente e decrescente|
|properties.created |Eq, ne, ge, le,  gt, lt|Crescente e decrescente|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Crescente e decrescente|
|properties.options |||
|properties.policyId|Eq, ne||
|type|||

### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. 

Se i criteri di chiave simmetrica vengono creati o eliminati durante il paging della raccolta, le modifiche si riflettono nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

Il seguente esempio in C# illustra come enumerare i criteri di chiave simmetrica nell'account.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Per esempi di REST, vedere [Content Key Policies - List](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list) (Criteri di chiave simmetrica - Elenco)

## <a name="next-steps"></a>Passaggi successivi

[Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](protect-with-aes128.md)

[Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
