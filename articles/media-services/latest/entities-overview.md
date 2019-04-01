---
title: Sviluppo con le API v3 - Azure | Microsoft Docs
description: Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9a02030cb2b785b027bb78bad5ef636dff9dd8f3
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758541"
---
# <a name="developing-with-media-services-v3-apis"></a>Sviluppo con servizi multimediali v3 API

Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri. 

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere: [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Principi di progettazione dell'API v3

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. Le API della versione 3 non restituiscono segreti o credenziali per un'operazione **Get** o **List**. Le chiavi sono sempre Null, vuote o purificate dalla risposta. È necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Azioni distinte consentono di impostare autorizzazioni di sicurezza di controllo degli accessi in base al ruolo diverse, nel caso alcune API recuperino o visualizzino i segreti, mentre altre non lo fanno. Per informazioni su come gestire l'accesso con il controllo degli accessi in base al ruolo, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Alcuni esempi sono:

* Get del StreamingLocator non restituisce valori di chiave simmetrica.
* L'operazione get per ottenere il ContentKeyPolicy non restituisce le chiavi di restrizione.
* Non restituisce la parte di stringa di query dell'URL (per rimuovere la firma) dell'URL di Input HTTP Jobs'.

Vedere l'esempio incluso in [Ottenere i criteri di chiave simmetrica - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Servizi multimediali supporta le opzioni di query OData seguenti per le entità di Servizi multimediali v3: 

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

Le proprietà delle entità di tipo Datetime sono sempre in formato UTC.

### <a name="page-results"></a>Restituire i risultati in più pagine

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. La dimensione varia in base al tipo di entità. Per informazioni dettagliate, leggere le singole sezioni seguenti.

Se vengono create o eliminate entità durante la restituzione delle pagine della raccolta, le modifiche si riflettono sui risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

### <a name="assets"></a>Asset

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come le opzioni di filtro e ordinamento possono essere applicate alle proprietà dell'[asset](https://docs.microsoft.com/rest/api/media/assets): 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|eq, gt, lt| crescente e decrescente|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| crescente e decrescente|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

L'esempio C# seguente applica il filtro alla data di creazione:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>Paginazione 

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è 1000.

##### <a name="c-example"></a>Esempio in C#

L'esempio C# seguente illustra come enumerare tutti gli asset nell'account.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>Esempio REST

Si consideri l'esempio seguente per vedere dove viene usato $skiptoken. Assicurarsi di sostituire *amstestaccount* con il proprio nome dell'account e di impostare il valore *api-version* sulla versione più recente.

Se si richiede un elenco degli asset simile al seguente:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

La risposta ottenuta sarà simile a questa:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Si può quindi richiedere la pagina successiva inviando una richiesta get per:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Per altri esempi REST, vedere [Assets - List](https://docs.microsoft.com/rest/api/media/assets/list) (Asset - Elenco)

### <a name="content-key-policies"></a>Criteri di chiave simmetrica

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei [criteri di chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies): 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.created |eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.options |||
|properties.policyId|eq, ne||
|type|||

#### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

Il seguente esempio in C# illustra come enumerare tutti i **criteri di chiave simmetrica** nell'account.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Per esempi di REST, vedere [Content Key Policies - List](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list) (Criteri di chiave simmetrica - Elenco)

### <a name="jobs"></a>Processi

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei [processi](https://docs.microsoft.com/rest/api/media/jobs): 

| NOME    | Filtro                        | Ordine |
|---------|-------------------------------|-------|
| name                    | eq            | crescente e decrescente|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| crescente e decrescente|
| properties.lastModified | gt, ge, lt, le | crescente e decrescente| 


#### <a name="pagination"></a>Paginazione

La paginazione dei processi è supportata in Servizi multimediali v3.

Il seguente esempio in C# illustra come enumerare i processi nell'account.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Per esempi di REST, vedere [Jobs - List](https://docs.microsoft.com/rest/api/media/jobs/list) (Processi - Elenco)

### <a name="streaming-locators"></a>Localizzatori di streaming

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei localizzatori di streaming: 

|NOME|Filtro|Ordine|
|---|---|---|
|id |||
|name|eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

#### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

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

### <a name="streaming-policies"></a>Criteri di streaming

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà dei criteri di streaming: 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le, gt, lt|crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

#### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è pari a 10.

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


### <a name="transform"></a>Trasformare

#### <a name="filteringordering"></a>Filtri/Ordinamento

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà delle [trasformazioni](https://docs.microsoft.com/rest/api/media/transforms): 

| NOME    | Filtro                        | Ordine |
|---------|-------------------------------|-------|
| name                    | eq            | crescente e decrescente|
| properties.created      | gt, ge, lt, le| crescente e decrescente|
| properties.lastModified | gt, ge, lt, le | crescente e decrescente|

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
