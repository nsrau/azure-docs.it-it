---
title: Filtraggio, ordinamento e paging delle entità di servizi multimediali-Azure | Microsoft Docs
description: Questo articolo illustra come filtrare, ordinare e restituire in più pagine le entità di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d13ff3944e53f103c03a92e03d217b0066bc97df
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693319"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtraggio, ordinamento e paging delle entità di servizi multimediali

Questo argomento illustra le opzioni di query OData e il supporto per l'impaginazione disponibili quando si elencano le entità di servizi multimediali di Azure V3.

## <a name="considerations"></a>Considerazioni

* Le proprietà delle entità di tipo `Datetime` sono sempre in formato UTC.
* Gli spazi vuoti nella stringa di query devono essere codificati in URL prima di inviare una richiesta.

## <a name="comparison-operators"></a>Operatori di confronto

Per confrontare un campo con un valore costante, è possibile usare gli operatori seguenti:

Operatori di uguaglianza:

- `eq`: verificare se un campo è *uguale a* un valore costante.
- `ne`: verificare se un campo *non è uguale a* un valore costante.

Operatori di intervallo:

- `gt`: verificare se un campo è *maggiore di* un valore costante.
- `lt`: verificare se un campo è *minore di* un valore costante.
- `ge`: verificare se un campo è *maggiore o uguale a* una costante. value
- `le`: verificare se un campo è *minore o uguale a* un valore costante.

## <a name="filter"></a>Filtra

Usare `$filter` per fornire un parametro di filtro OData per trovare solo gli oggetti a cui si è interessati.

Nell'esempio REST seguente viene filtrato il valore `alternateId` di un asset:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

L'esempio C# seguente Filtra la data di creazione dell'asset:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Ordina per

Utilizzare `$orderby` per ordinare gli oggetti restituiti in base al parametro specificato. ad esempio:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Per ordinare i risultati in ordine crescente o decrescente, aggiungere `asc` o `desc` al nome del campo, separati da uno spazio. Ad esempio: `$orderby properties/created desc`.

## <a name="skip-token"></a>Ignora token

Se una risposta alla query contiene molti elementi, il servizio restituisce un valore `$skiptoken` (`@odata.nextLink`) usato per ottenere la pagina di risultati successiva. Utilizzarlo per eseguire il paging dell'intero set di risultati.

In servizi multimediali V3 non è possibile configurare le dimensioni della pagina. Le dimensioni della pagina variano in base al tipo di entità. Leggere le singole sezioni che seguono per informazioni dettagliate.

Se le entità vengono create o eliminate durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

> [!TIP]
> Usare sempre `nextLink` per enumerare la raccolta e non dipendere da una particolare dimensione della pagina.
>
> Il valore `nextLink` sarà presente solo se è presente più di una pagina di entità.

Si consideri l'esempio seguente in cui viene usato `$skiptoken`. Assicurarsi di sostituire *amstestaccount* con il proprio nome dell'account e di impostare il valore *api-version* sulla versione più recente.

Se si richiede un elenco di asset come il seguente:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Verrà restituita una risposta simile alla seguente:

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

Nell'esempio C# seguente viene illustrato come enumerare tutti i localizzatori di streaming nell'account.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Utilizzo di operatori logici per combinare le opzioni di query

Media Services V3 supporta gli operatori logici **or** e **e** . 

L'esempio REST seguente controlla lo stato del processo:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

La stessa query viene costruita in C# modo analogo al seguente: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtro e ordinamento delle opzioni delle entità

La tabella seguente illustra come applicare le opzioni di filtro e ordinamento a entità diverse:

|Nome dell'entità|Nome proprietà|Filtra|Ordina|
|---|---|---|---|
|[Asset](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Criteri chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Processi](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizzatori di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Trasforma](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Passaggi successivi

* [Elenca asset](https://docs.microsoft.com/rest/api/media/assets/list)
* [Elenca criteri chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Elencare i processi](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Elencare i criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Elencare i localizzatori di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Quote e limitazioni](limits-quotas-constraints.md)
