---
title: Filtraggio, ordinamento e paging delle entità di Servizi multimediali
titleSuffix: Azure Media Services
description: Informazioni su filtri, ordinamento e paging delle entità di Servizi multimediali di Azure.Learn about filtering, ordering, and paging of Azure Media Services v3 entities.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500040"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtraggio, ordinamento e paging delle entità di Servizi multimediali

Questo argomento illustra le opzioni di query OData e il supporto per l'impaginazione disponibili quando si elencano le entità di Servizi multimediali di Azure.This topic discusses the OData query options and pagination support available when you're listing Azure Media Services v3 entities.

## <a name="considerations"></a>Considerazioni

* Le proprietà delle entità `Datetime` di tipo sono sempre in formato UTC.
* Gli spazi vuoti nella stringa di query devono essere codificati in URL prima di inviare una richiesta.

## <a name="comparison-operators"></a>Operatori di confronto

È possibile utilizzare gli operatori seguenti per confrontare un campo con un valore costante:

Operatori di uguaglianza:

- `eq`: verifica se un campo è *uguale a* un valore costante.
- `ne`: verifica se un campo non è *uguale a* un valore costante.

Operatori di intervallo:

- `gt`: verifica se un campo è *maggiore di* un valore costante.
- `lt`: verifica se un campo è *minore di* un valore costante.
- `ge`: verifica se un campo è *maggiore o uguale a* un valore costante.
- `le`: verifica se un campo è *minore o uguale a* un valore costante.

## <a name="filter"></a>Filtro

Utilizzare `$filter` per fornire un parametro di filtro OData per trovare solo gli oggetti a cui si è interessati.

L'esempio REST seguente `alternateId` filtra il valore di un asset:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

L'esempio seguente di C'è filtra in base alla data di creazione dell'asset:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Ordina per

Utilizzare `$orderby` per ordinare gli oggetti restituiti in base al parametro specificato. Ad esempio:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Per ordinare i risultati in ordine crescente `asc` o `desc` decrescente, aggiungere o al nome del campo, separati da uno spazio. Ad esempio `$orderby properties/created desc`.

## <a name="skip-token"></a>Salta token

Se una risposta a una query `$skiptoken` contiene`@odata.nextLink`molti elementi, il servizio restituisce un valore ( ) utilizzato per ottenere la pagina successiva dei risultati. Utilizzarlo per scorrere l'intero set di risultati.

In Servizi multimediali v3 non è possibile configurare le dimensioni della pagina. Le dimensioni della pagina variano in base al tipo di entità. Leggi le singole sezioni che seguono per i dettagli.

Se le entità vengono create o eliminate durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata).

> [!TIP]
> Utilizzare `nextLink` sempre per enumerare la raccolta e non dipendono da una particolare dimensione di pagina.
>
> Il `nextLink` valore sarà presente solo se è presente più di una pagina di entità.

Si consideri l'esempio seguente di dove `$skiptoken` viene utilizzato. Assicurarsi di sostituire *amstestaccount* con il proprio nome dell'account e di impostare il valore *api-version* sulla versione più recente.

Se richiedi un elenco di asset come questo:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Otterrai una risposta simile a questa:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Si può quindi richiedere la pagina successiva inviando una richiesta get per:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Nell'esempio in C'è riportato di seguito viene illustrato come enumerare tutti i localizzatori di streaming nell'account.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Utilizzo di operatori logici per combinare le opzioni di queryUsing logical operators to combine query options

Media Services v3 supporta gli operatori logici **OR** e **AND.** 

L'esempio REST seguente controlla lo stato del processo:The following REST example checks the job's state:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Si costruisce la stessa query in C , in questo modo: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opzioni di filtro e ordinamento delle entità

Nella tabella seguente viene illustrato come applicare le opzioni di filtro e ordinamento a entità diverse:

|Nome dell'entità|Nome proprietà|Filtro|JSON|
|---|---|---|---|
|[Asset](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Norme sulle chiavi dei contenuti](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Processi](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizzatori di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Passaggi successivi

* [Elenca risorse](https://docs.microsoft.com/rest/api/media/assets/list)
* [Elencare i criteri della chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Elenca processi](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Elencare i criteri di streamingList Streaming Policies](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Elenca i localizzatori di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Quote e limitazioni](limits-quotas-constraints.md)
