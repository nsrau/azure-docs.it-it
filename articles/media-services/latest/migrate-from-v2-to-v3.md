---
title: Eseguire la migrazione di Servizi multimediali di Azure dalla versione 2 alla versione 3 | Microsoft Docs
description: In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659528"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Eseguire la migrazione di Servizi multimediali dalla versione 2 alla versione 3

> [!NOTE]
> La versione più recente di Servizi multimediali di Azure è disponibile in anteprima e può essere indicata come v3.

In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure (AMS) e sono illustrate le differenze tra le due versioni.

## <a name="why-should-you-move-to-v3"></a>Perché è consigliabile passare a v3?

### <a name="api-is-more-approachable"></a>API più accessibile

*  v3 si basa su una superficie API unificata che espone funzionalità operative e di gestione basate su Azure Resource Manager. I modelli di Azure Resource Manager possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, LiveEvent e altro ancora.
* Documento di specifica OpenAPI (noto anche come Swagger).
* SDK per .NET, .NET Core, Node.js, Python, Java e Ruby.
* Integrazione dell'interfaccia della riga di comando di Azure.

### <a name="new-features"></a>Nuove funzionalità

* La codifica ora supporta l'inserimento HTTPS (input basato su URL).
* Le trasformazioni sono una novità di v3. Una trasformazione consente di condividere configurazioni, creare modelli di Azure Resource Manager e isolare le impostazioni di codifica per un cliente o un tenant specifico. 
* Un asset può avere più StreamingLocator con diverse impostazioni di creazione dinamica dei pacchetti e crittografia dinamica.
* La protezione del contenuto supporta le funzionalità con più chiavi. 
* L'anteprima di LiveEvent supporta la creazione dinamica dei pacchetti e la crittografia dinamica. Questo consente la protezione del contenuto nell'anteprima, nonché la creazione di pacchetti DASH e HLS.
* LiveOuput è più semplice da usare rispetto all'entità Program precedente. 
* È stato aggiunto il supporto RBAC per le entità.

## <a name="changes-from-v2"></a>Modifiche rispetto a v2

* Gli SDK di Servizi multimediali sono stati separati da Storage SDK, per offrire maggiore controllo sullo Storage SDK in uso ed evitare problemi di controllo delle versioni. 
* In v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo comportamento è diverso dalle impostazioni predefinite di Media Encoder Standard v2 REST. Ad esempio, una velocità in bit che in v2 viene specificata come 128, nella versione v3 sarà 128000. 
* Le entità AssetFiles, AccessPolicies e IngestManifests non esistono in v3.
* ContentKey non è più un'entità, ma una proprietà di StreamingLocator.
* Il supporto della griglia di eventi sostituisce NotificationEndpoints.
* Alcune entità sono state rinominate

  * JobOutput sostituisce Task, che ora fa semplicemente parte di Job.
  * LiveEvent sostituisce Channel.
  * LiveOutput sostituisce Program.
  * StreamingLocator sostituisce Locator.

## <a name="code-changes"></a>Modifiche al codice

### <a name="create-an-asset-and-upload-a-file"></a>Creare un asset e caricare un file 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Inviare un processo

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Pubblicare un asset con crittografia AES 

#### <a name="v2"></a>v2

1. Creare ContentKeyAuthorizationPolicyOption
2. Creare ContentKeyAuthorizationPolicy
3. Creare AssetDeliveryPolicy
4. Creare asset e caricare contenuto o inviare processi e usare asset di output
5. Associare AssetDeliveryPolicy all'asset
6. Creare ContentKey
7. Collegare ContentKey all'asset
8. Creare AccessPolicy
9. Creare Locator

#### <a name="v3"></a>v3

1. Creare criteri di chiave simmetrica
2. Creare asset
3. Caricare contenuto o usare asset come JobOutput
4. Creare Locator

## <a name="next-steps"></a>Passaggi successivi

Per scoprire quanto è semplice eseguire la codifica e lo streaming di file video, vedere l'articolo relativo allo [streaming di file](stream-files-dotnet-quickstart.md). 

