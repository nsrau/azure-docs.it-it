---
title: Gestire le risorse in Servizi multimediali di AzureManage assets in Azure Media Services
titleSuffix: Azure Media Services
description: Risorsa in cui inserisci contenuti multimediali (ad esempio, tramite upload o inserimento dal vivo), supporti di output (da un output del processo) e pubblichi contenuti multimediali da (per lo streaming). Questo argomento fornisce una panoramica su come creare una nuova risorsa e caricare i file.
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
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345891"
---
# <a name="manage-assets"></a>Gestire le risorse

In Servizi multimediali di Azure, un [asset](https://docs.microsoft.com/rest/api/media/assets) è dove 

* caricare file multimediali in una risorsa,
* ingestione e archiviazione di live streaming in una risorsa,
* produrre i risultati di una codifica del lavoro di analisi in un asset,
* pubblicare contenuti multimediali per lo streaming, 
* scaricare file da una risorsa.

Questo argomento fornisce una panoramica su come caricare file in un asset ed eseguire altre operazioni comuni. Vengono inoltre forniti collegamenti a esempi di codice e argomenti correlati.

## <a name="prerequisite"></a>Prerequisito 

Prima di iniziare lo sviluppo, esaminare:

* [Concetti](concepts-overview.md)
* [Sviluppo con le API Servizi multimediali v3](media-services-apis-overview.md) (sono incluse informazioni sull'accesso alle API, sulle convenzioni di denominazione e così via) 

## <a name="upload-media-files-into-an-asset"></a>Caricare file multimediali in una risorsa

Dopo che i file digitali sono stati caricati nell'archivio e associati a un asset, possono essere utilizzati nei flussi di lavoro di codifica, streaming e analisi dei contenuti di Servizi multimediali. Uno dei flussi di lavoro comuni di Servizi multimediali consiste nel caricare, codificare e trasmettere un file. Questa sezione descrive i passaggi generali.

1. Usare l'API Servizi multimediali v3 per creare un nuovo asset "input". Questa operazione crea un contenitore nell'account di archiviazione associato all'account di Servizi multimediali. L'API restituisce il nome del contenitore (ad esempio, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se si dispone già di un contenitore BLOB che si vuole associare a un asset, è possibile specificare il nome del contenitore quando si crea l'asset. Servizi multimediali supporta attualmente solo i BLOB nella radice del contenitore e non quelli con i percorsi nel nome del file. Un contenitore con il nome file "input.mp4" andrà quindi bene. Tuttavia, un contenitore con il nome del file "videos/inputs/input.mp4" non funzionerà.

    È possibile usare l'interfaccia della riga di comando di Azure per eseguire il caricamento direttamente in qualsiasi account di archiviazione e contenitore della sottoscrizione, per cui si hanno i diritti.

    Il nome del contenitore deve essere univoco e seguire le linee guida sulla denominazione delle risorse di archiviazione. Il nome non deve necessariamente seguire il formato dei nomi di contenitore di asset di Servizi multimediali (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Ottenere un URL di firma di accesso condiviso con autorizzazioni di lettura/scrittura che verranno usate per caricare i file digitali nel contenitore di asset.

    È possibile usare l'API Servizi multimediali per [elencare gli URL dei contenitori di asset](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** accetta un parametro [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) su cui si imposta `expiryTime`. L'ora deve essere impostata su < 24 ore.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) restituisce più URL di accesso utenti come sono presenti due chiavi dell'account di archiviazione per ogni account di archiviazione. Un account di archiviazione ha due chiavi perché consente una rotazione uniforme delle chiavi dell'account di archiviazione (ad esempio, modificarne una durante l'uso dell'altra, quindi iniziare a usare la nuova chiave e ruotare l'altra). Il primo URL di accesso condiviso rappresenta la chiave di archiviazione1 e la seconda chiave di archiviazione2.The 1st SAS URL represents storage key1 and second one storage key2.
3. Usare le API di Archiviazione di Azure o gli SDK (ad esempio, [l'API REST](../../storage/common/storage-rest-api-auth.md) di archiviazione o .NET SDK ) per caricare i file nel contenitore Asset.Use the Azure Storage APIs or SDKs (for example, the Storage REST API or [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) to upload files into the Asset container.
4. Usare le API di Servizi multimediali v3 per creare una trasformazione e un processo per elaborare l'asset "input". Per ulteriori informazioni, consultate [Trasformazioni e processi.](transform-concept.md)
5. Trasmettere il contenuto dall'asset "output".

### <a name="create-a-new-asset"></a>Creare un nuovo asset

> [!NOTE]
> Le proprietà di un asset di tipo Datetime sono sempre in formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Per un esempio REST, vedere l'esempio [Create an Asset with REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) (Creare un asset con REST).

Nell'esempio viene illustrato come creare il corpo della **richiesta** in cui è possibile specificare la descrizione, il nome del contenitore, l'account di archiviazione e altre informazioni utili.

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

* [Creare un input di processo da un file localeCreate a job input from a local file](job-input-from-local-file-how-to.md)
* [Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Inserimento e archiviazione di flussi live in una risorsa

In Servizi multimediali, un oggetto [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) è come un videoregistratore digitale che catturerà e registrerà il tuo flusso live in una risorsa nel tuo account Di Servizi multimediali. Il contenuto registrato viene salvato in modo permanente nel contenitore definito dalla risorsa [Asset.](https://docs.microsoft.com/rest/api/media/assets)

Per altre informazioni, vedere:

* [Utilizzo di un DVR cloud](live-event-cloud-dvr.md)
* [Esercitazione in streaming in diretta](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Produrre i risultati di un processo in un cespite

In Servizi multimediali, durante l'elaborazione dei video (ad esempio, codifica o analisi) è necessario creare una [risorsa](assets-concept.md) di output per archiviare il risultato del [lavoro.](transforms-jobs-concept.md)

Per altre informazioni, vedere:

* [Codifica di un video](encoding-concept.md)
* [Creare un input di processo da un file localeCreate a job input from a local file](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Pubblicare una risorsa per lo streaming

Per pubblicare un asset per lo streaming, è necessario creare un [localizzatore](streaming-locators-concept.md)di streaming . Il localizzatore di streaming deve conoscere il nome dell'asset che si desidera pubblicare. 

Per altre informazioni, vedere:

[Esercitazione: Caricare, codificare e trasmettere video in streaming con Servizi multimediali v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Scaricare i risultati di un processo da un asset di outputDownload results of a job from an output asset

È quindi possibile scaricare questi risultati del processo in una cartella locale usando Servizio multimediale e le API di archiviazione. 

Vedere l'esempio dei file di [download.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Vedi gli esempi di codice completi che illustrano come caricare, codificare, analizzare, trasmettere in diretta e su richiesta: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [RESTO](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
