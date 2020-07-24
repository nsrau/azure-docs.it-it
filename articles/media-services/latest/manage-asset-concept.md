---
title: Gestire gli asset in servizi multimediali di Azure
titleSuffix: Azure Media Services
description: Un asset in cui si immettono supporti (ad esempio, tramite il caricamento o l'inserimento Live), i supporti di output (dall'output di un processo) e la pubblicazione di supporti da (per il flusso). Questo argomento fornisce una panoramica su come creare un nuovo asset e caricare i file.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6accd303ba11c4c1406c7a157fa8176972fc7a3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022908"
---
# <a name="manage-assets"></a>Gestire le risorse

In servizi multimediali di Azure, un [Asset](/rest/api/media/assets) è il punto in cui 

* caricare i file multimediali in un asset,
* inserire e archiviare i flussi live in un asset,
* restituire i risultati di una codifica del processo di analisi a un asset,
* pubblicare supporti per lo streaming, 
* scaricare i file da un asset.

Questo argomento offre una panoramica di come caricare file in un asset ed eseguire altre operazioni comuni. Vengono inoltre forniti collegamenti a esempi di codice e argomenti correlati.

## <a name="prerequisite"></a>Prerequisito 

Prima di iniziare lo sviluppo, esaminare:

* [Concetti](concepts-overview.md)
* [Sviluppo con le API Servizi multimediali v3](media-services-apis-overview.md) (sono incluse informazioni sull'accesso alle API, sulle convenzioni di denominazione e così via) 

## <a name="upload-media-files-into-an-asset"></a>Caricare file multimediali in un asset

Dopo che i file digitali sono stati caricati nell'archiviazione e associati a un asset, possono essere usati nella codifica, nello streaming e nell'analisi dei flussi di lavoro del contenuto di servizi multimediali. Uno dei flussi di lavoro comuni di Servizi multimediali consiste nel caricare, codificare e trasmettere un file. Questa sezione descrive i passaggi generali.

1. Usare l'API Servizi multimediali v3 per creare un nuovo asset "input". Questa operazione crea un contenitore nell'account di archiviazione associato all'account di Servizi multimediali. L'API restituisce il nome del contenitore (ad esempio, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se si dispone già di un contenitore BLOB che si vuole associare a un asset, è possibile specificare il nome del contenitore durante la creazione dell'asset. Servizi multimediali supporta attualmente solo i BLOB nella radice del contenitore e non quelli con i percorsi nel nome del file. Un contenitore con il nome file "input.mp4" andrà quindi bene. Tuttavia, un contenitore con il nome file "video/input/input.mp4" non funzionerà.

    È possibile usare l'interfaccia della riga di comando di Azure per eseguire il caricamento direttamente in qualsiasi account di archiviazione e contenitore della sottoscrizione, per cui si hanno i diritti.

    Il nome del contenitore deve essere univoco e seguire le linee guida sulla denominazione delle risorse di archiviazione. Il nome non deve necessariamente seguire il formato dei nomi di contenitore di asset di Servizi multimediali (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Ottenere un URL di firma di accesso condiviso con autorizzazioni di lettura/scrittura che verranno usate per caricare i file digitali nel contenitore di asset.

    È possibile usare l'API Servizi multimediali per [elencare gli URL dei contenitori di asset](/rest/api/media/assets/listcontainersas).

    **AssetContainerSas. listContainerSas** accetta un parametro [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) su cui è stato impostato `expiryTime` . Il tempo deve essere impostato su < 24 ore.

    [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) restituisce più URL SAS perché sono disponibili due chiavi dell'account di archiviazione per ogni account di archiviazione. Un account di archiviazione ha due chiavi perché consente il failover e la rotazione trasparente delle chiavi dell'account di archiviazione. Il primo URL di firma di accesso condiviso rappresenta la prima chiave dell'account di archiviazione e il secondo URL SAS rappresenta la seconda chiave.
3. Usare le API di archiviazione di Azure o gli SDK (ad esempio, l' [API REST di archiviazione](../../storage/common/storage-rest-api-auth.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) per caricare i file nel contenitore di asset.
4. Usare le API di Servizi multimediali v3 per creare una trasformazione e un processo per elaborare l'asset "input". Per altre informazioni, vedere [Trasformazioni e processi](./transforms-jobs-concept.md).
5. Trasmettere il contenuto dall'asset "output".

### <a name="create-a-new-asset"></a>Creare un nuovo asset

> [!NOTE]
> Le proprietà di un asset del tipo DateTime sono sempre in formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Per un esempio REST, vedere l'esempio [Create an Asset with REST](/rest/api/media/assets/createorupdate#examples) (Creare un asset con REST).

Nell'esempio viene illustrato come creare il **corpo della richiesta** in cui è possibile specificare la descrizione, il nome del contenitore, l'account di archiviazione e altre informazioni utili.

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

### <a name="see-also"></a>Vedere anche

* [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md)
* [Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Inserire e archiviare flussi live in un asset

In servizi multimediali un oggetto di [output attivo](/rest/api/media/liveoutputs) è come un registratore video digitale che rileverà e registrerà il flusso Live in un asset nell'account di servizi multimediali. Il contenuto registrato viene reso permanente nel contenitore definito dalla risorsa [Asset](/rest/api/media/assets) .

Per altre informazioni, vedere:

* [Utilizzo di un DVR cloud](live-event-cloud-dvr.md)
* [Esercitazione Live di streaming](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Restituire i risultati di un processo a un asset

In servizi multimediali, quando si elaborano i video, ad esempio la codifica o l'analisi, è necessario creare un [Asset](assets-concept.md) di output per archiviare il risultato del [processo](transforms-jobs-concept.md).

Per altre informazioni, vedere:

* [Codifica di un video](encoding-concept.md)
* [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Pubblicare un asset per lo streaming

Per pubblicare un asset per lo streaming, è necessario creare un [localizzatore di streaming](streaming-locators-concept.md). Il localizzatore di streaming deve comprendere il nome dell'asset che si desidera pubblicare. 

Per altre informazioni, vedere:

[Esercitazione: Caricare, codificare ed eseguire lo streaming di video con Servizi multimediali v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Scaricare i risultati di un processo da un asset di output

È quindi possibile scaricare questi risultati del processo in una cartella locale usando le API di archiviazione e del servizio multimediale. 

Vedere l'esempio di [download dei file](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi di codice completo che illustrano come caricare, codificare, analizzare, trasmettere in diretta e su richiesta: 

* [Java](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Rest](/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
