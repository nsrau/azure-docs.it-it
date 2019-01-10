---
title: Asset in Servizi multimediali - Azure | Microsoft Docs
description: Questo articolo descrive le caratteristiche degli asset e come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969576"
---
# <a name="assets"></a>Asset

In Servizi multimediali di Azure un [asset](https://docs.microsoft.com/rest/api/media/assets) contiene file digitali, tra cui video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, con i relativi metadati. Una volta caricati in un asset, i file digitali possono essere usati nei flussi di lavoro di codifica, trasmissione e analisi di Servizi multimediali di Azure. Per altre informazioni, vedere più avanti la sezione [Caricare i file digitali negli asset](#upload-digital-files-into-assets).

Un asset viene mappato a un contenitore BLOB nell'[account di archiviazione di Azure](storage-account-concept.md) e i file contenuti nell'asset vengono archiviati come BLOB in blocchi in tale contenitore. Servizi multimediali supporta i livelli BLOB quando l'account usa l'archiviazione Utilizzo generico v2 (GPv2). Con GPv2, è possibile spostare i file in uno [spazio di archiviazione ad accesso sporadico o archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). L'archiviazione **archivio** è adatta all'archiviazione di file di origine quando non sono più necessari (ad esempio, dopo la codifica).

Il livello di archiviazione **archivio** è consigliato solo per file di origine di dimensioni molto estese già codificati e con l'output del processo di codifica inserito in un contenitore BLOB di output. I BLOB nel contenitore di output che si vuole associare a un asset e usare per trasmettere o analizzare il contenuto, devono esistere in un livello di archiviazione ad **accesso frequente** o ad **accesso sporadico**.

## <a name="asset-definition"></a>Definizione di asset

Nella tabella seguente vengono illustrate le proprietà di un asset e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|id|ID di risorsa completo per la risorsa.|
|name|Nome della risorsa.|
|properties.alternateId |ID alternativo dell'asset.|
|properties.assetId |ID dell'asset.|
|properties.container |Nome del contenitore BLOB dell'asset.|
|properties.created |Data di creazione dell'asset.<br/> La data/ora è sempre in formato UTC.|
|properties.description|Descrizione dell'asset.|
|properties.lastModified |Data dell'ultima modifica dell'asset. <br/> La data/ora è sempre in formato UTC.|
|properties.storageAccountName |Nome dell'account di archiviazione.|
|properties.storageEncryptionFormat |Formato di crittografia dell'asset. None oppure MediaStorageEncryption.|
|type|Tipo di risorsa.|

Per una definizione completa, vedere [Assets](https://docs.microsoft.com/rest/api/media/assets) (Asset).

## <a name="upload-digital-files-into-assets"></a>Caricare i file digitali negli asset

Uno dei flussi di lavoro comuni di Servizi multimediali consiste nel caricare, codificare e trasmettere un file. Questa sezione descrive i passaggi generali.

1. Usare l'API Servizi multimediali v3 per creare un nuovo asset "input". Questa operazione crea un contenitore nell'account di archiviazione associato all'account di Servizi multimediali. L'API restituisce il nome del contenitore (ad esempio, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Se si ha già un contenitore BLOB che si vuole associare a un asset, è possibile specificare il nome del contenitore durante la creazione dell'asset. Servizi multimediali supporta attualmente solo i BLOB nella radice del contenitore e non quelli con i percorsi nel nome del file. Un contenitore con il nome file "input.mp4" andrà quindi bene. Un contenitore con il nome file "videos/inputs/input.mp4" non andrà invece bene.

    È possibile usare l'interfaccia della riga di comando di Azure per eseguire il caricamento direttamente in qualsiasi account di archiviazione e contenitore della sottoscrizione, per cui si hanno i diritti. <br/>Il nome del contenitore deve essere univoco e seguire le linee guida sulla denominazione delle risorse di archiviazione. Il nome non deve necessariamente seguire il formato dei nomi di contenitore di asset di Servizi multimediali (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Ottenere un URL di firma di accesso condiviso con autorizzazioni di lettura/scrittura che verranno usate per caricare i file digitali nel contenitore di asset. È possibile usare l'API Servizi multimediali per [elencare gli URL dei contenitori di asset](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Usare le API o gli SDK di Archiviazione di Azure (ad esempio, l'[API REST di Archiviazione](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) per caricare i file nel contenitore di asset. 
4. Usare le API di Servizi multimediali v3 per creare una trasformazione e un processo per elaborare l'asset "input". Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md).
5. Trasmettere il contenuto dall'asset "output".

> [!TIP]
> Per un esempio .NET completo che illustra come creare l'asset, ottenere un URL di firma di accesso condiviso scrivibile del contenitore dell'asset nella risorsa di archiviazione e caricare il file nel contenitore nella risorsa di archiviazione usando l'URL di firma di accesso condiviso, vedere [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Creare un nuovo asset

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Per un esempio REST, vedere l'esempio [Create an Asset with REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) (Creare un asset con REST).

L'esempio illustra come creare il **corpo della richiesta** in cui è possibile specificare utili informazioni come la descrizione, il nome del contenitore, l'account di archiviazione e altre informazioni.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Per un esempio completo, vedere [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md). In Servizi multimediali v3 l'input di un processo può essere creato anche da URL HTTPS. Vedere [Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md).

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Servizi multimediali supporta le opzioni di query OData seguenti per gli asset: 

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

La tabella seguente illustra come queste opzioni possono essere applicate alle proprietà di un asset: 

|NOME|Filtro|Ordine|
|---|---|---|
|id|||
|name|Supporta: Eq, Gt, Lt|Supporta: Crescente e decrescente|
|properties.alternateId |Supporta: Eq||
|properties.assetId |Supporta: Eq||
|properties.container |||
|properties.created|Supporta: Eq, Gt, Lt| Supporta: Crescente e decrescente|
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

### <a name="pagination"></a>Paginazione

La paginazione è supportata per ognuno dei quattro ordinamenti abilitati. Attualmente, la dimensione della pagina è 1000.

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. 

Se gli asset vengono creati o eliminati durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

#### <a name="c-example"></a>Esempio in C#

L'esempio C# seguente illustra come enumerare tutti gli asset nell'account.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Esempio REST

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

## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali:

|Opzione di crittografia|DESCRIZIONE|Servizi multimediali v2|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali|Crittografia AES-256, chiave gestita da Servizi multimediali|Supportata<sup>(1)</sup>|Non supportata<sup>(2)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente|Supportato|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da Archiviazione di Azure, chiave gestita dal cliente in Key Vault|Non supportate|Non supportate|

<sup>1</sup> Servizi multimediali supporta la gestione di contenuto non crittografato/senza alcuna forma di crittografia, ma tale modalità non è consigliata.

<sup>2</sup> In Servizi multimediali versione 3, la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. In altre parole, la versione 3 funziona con asset con crittografia di archiviazione esistenti, ma non consente la creazione di nuovi asset di questo tipo.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)

[Differenze tra Servizi multimediali v2 e v3](migrate-from-v2-to-v3.md)
