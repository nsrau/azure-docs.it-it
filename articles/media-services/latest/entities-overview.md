---
title: Applicazione di filtri, ordinamento e paginazione delle entità di Servizi multimediali
titleSuffix: Azure Media Services
description: Informazioni sull'applicazione di filtri, ordinamento e paginazione delle entità di Servizi multimediali v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9a8cff3685cdaad011332adf58dc76f74976cd44
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89300188"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e paginazione delle entità di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo argomento illustra le opzioni di query OData e il supporto per la paginazione disponibili quando si elencano le entità di Servizi multimediali di Azure v3.

## <a name="considerations"></a>Considerazioni

* Le proprietà delle entità di tipo `Datetime` sono sempre in formato UTC.
* Lo spazio vuoto nella stringa di query deve essere con codifica URL prima dell'invio di una richiesta.

## <a name="comparison-operators"></a>Operatori di confronto

Per confrontare un campo con un valore costante, è possibile usare gli operatori seguenti:

Operatori di uguaglianza:

- `eq`: verifica se un campo è *uguale a* un valore costante.
- `ne`: verifica se un campo è *diverso da* un valore costante.

Operatori di intervallo:

- `gt`: verifica se un campo è *maggiore di* un valore costante.
- `lt`: verifica se un campo è *minore di* un valore costante.
- `ge`: verifica se un campo è *maggiore o uguale a* un valore costante.
- `le`: verifica se un campo è *minore o uguale a* un valore costante.

## <a name="filter"></a>Filtro

Usare `$filter` per fornire un parametro di filtro OData per trovare solo gli oggetti a cui si è interessati.

L'esempio REST seguente applica un filtro in base al valore `alternateId` di un asset:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

L'esempio C# seguente applica un filtro in base alla data di creazione dell'asset:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Order by

Usare `$orderby` per ordinare gli oggetti restituiti in base al parametro specificato. Ad esempio:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Per ordinare i risultati in ordine crescente o decrescente, aggiungere `asc` o `desc` al nome del campo, separati da uno spazio. Ad esempio: `$orderby properties/created desc`.

## <a name="skip-token"></a>Skip token

Se la risposta di una query contiene molti elementi, il servizio restituisce un valore `$skiptoken` (`@odata.nextLink`) da usare per ottenere la pagina di risultati successiva. Usarlo per scorrere l'intero set di risultati.

In Servizi multimediali v3 non è possibile configurare le dimensioni delle pagine. Le dimensioni delle pagine variano in base al tipo di entità. Leggere le singole sezioni che seguono per informazioni dettagliate.

Se vengono create o eliminate entità durante lo scorrimento delle pagine della raccolta, le modifiche si riflettono nei risultati restituiti (se tali modifiche riguardano la parte della raccolta che non è stata scaricata).

> [!TIP]
> Usare sempre `nextLink` per enumerare la raccolta e non dipendere da determinate dimensioni di pagina.
>
> Il valore `nextLink` sarà presente solo se esistono più pagine di entità.

Si consideri l'esempio seguente per vedere dove viene usato `$skiptoken`. Assicurarsi di sostituire *amstestaccount* con il proprio nome dell'account e di impostare il valore *api-version* sulla versione più recente.

Se si richiede un elenco degli asset simile al seguente:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Si ottiene una risposta simile a questa:

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

L'esempio in C# seguente illustra come enumerare tutti i localizzatori di streaming nell'account.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Uso di operatori logici per combinare le opzioni di query

Servizi multimediali v3 supporta gli operatori logici **OR** e **AND**. 

L'esempio REST seguente controlla lo stato del processo:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

La stessa query viene creata in C# come segue: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opzioni di filtro e ordinamento delle entità

La tabella seguente illustra come applicare le opzioni di filtro e ordinamento a entità diverse:

|Nome dell'entità|Nome della proprietà|Filtro|JSON|
|---|---|---|---|
|[Asset](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Criteri chiave simmetrica](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Processi](/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizzatori di streaming](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Criteri di streaming](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Trasformazioni](/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Passaggi successivi

* [Elencare gli asset](/rest/api/media/assets/list)
* [Elencare i criteri di chiave simmetrica](/rest/api/media/contentkeypolicies/list)
* [Elencare i processi](/rest/api/media/jobs/list)
* [Elencare i criteri di streaming](/rest/api/media/streamingpolicies/list)
* [Elencare i localizzatori di streaming](/rest/api/media/streaminglocators/list)
* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Quote e limiti](limits-quotas-constraints.md)
