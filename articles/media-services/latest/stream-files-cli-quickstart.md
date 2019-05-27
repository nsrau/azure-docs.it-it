---
title: Eseguire lo streaming di file video con Servizi multimediali di Azure e l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per creare un nuovo account di Servizi multimediali di Azure, codificare un file ed eseguirne lo streaming su Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, streaming
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: cce424b11cc4cd587c6e7c50bc8bdf988004a43a
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550208"
---
# <a name="tutorial-stream-video-files---cli"></a>Esercitazione: Eseguire lo streaming di file video - Interfaccia della riga di comando

Questa esercitazione illustra come codificare ed eseguire lo streaming di video su un'ampia gamma di browser e dispositivi con la massima semplicità tramite Servizi multimediali di Azure e l'interfaccia della riga di comando di Azure. È possibile specificare il contenuto di input usando URL HTTPS, URL di firma di accesso condiviso o percorsi a file che si trovano nell'archivio BLOB di Azure.

L'esempio in questo articolo codifica contenuti resi accessibili tramite un URL HTTPS. Attualmente Servizi multimediali v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS.

Al termine di questa esercitazione, sarà possibile eseguire lo streaming di un video.  

![Riprodurre il video](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

Prima che sia possibile avviare operazioni di crittografia, codifica, gestione, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. L'account deve essere associato a uno o più account di archiviazione.

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione situati nella stessa posizione dell'account di Servizi multimediali, per limitare la latenza e i costi relativi al traffico in uscita.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

In questo esempio viene creato un account di archiviazione con ridondanza locale Standard per utilizzo generico v2.

Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Quando si sceglie uno SKU per la produzione è consigliabile usare `--sku Standard_RAGRS`, che offre la replica geografica per la continuità aziendale. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Si ottiene una risposta simile alla seguente:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Avviare l'endpoint di streaming

Il comando seguente dell'interfaccia della riga di comando di Azure avvia l'**endpoint di streaming** predefinito.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Si ottiene una risposta simile alla seguente:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Se l'endpoint di streaming è già in esecuzione, viene visualizzato questo messaggio:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Creare una trasformazione per la codifica con bitrate adattivo

Creare una **trasformazione** per configurare attività comuni per la codifica o l'analisi di video. In questo esempio si esegue una codifica con bitrate adattivo. Quindi, si invia un processo nella trasformazione creata. Il processo è la richiesta inviata a Servizi multimediali per applicare la trasformazione a determinati contenuti audio o video di input.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Si ottiene una risposta simile alla seguente:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Creare un asset di output

Crea un **asset** di output da usare come output del processo di codifica.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Si ottiene una risposta simile alla seguente:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Avviare un processo usando input HTTPS

Quando si inviano processi per elaborare i video, è necessario indicare a Servizi multimediali dove trovare il video di input. Una delle opzioni consiste nello specificare un URL HTTPS come input del processo, come illustrato in questo esempio.

Quando si esegue `az ams job start`, è possibile impostare un'etichetta per l'output del processo. L'etichetta può essere usata successivamente per identificare a cosa serve l'asset di output.

- Se si assegna un valore all'etichetta, impostare '--output-assets' su "assetname=label".
- Se non si assegna un valore all'etichetta, impostare '--output-assets' su "assetname=".

  Si noti che si aggiunge "=" a `output-assets`.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Si ottiene una risposta simile alla seguente:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Controlla stato

Controllare lo stato del processo dopo cinque minuti. Deve essere "Completato". In caso contrario, controllare di nuovo dopo qualche minuto. Dopo il completamento del processo, andare al passaggio successivo e creare un **localizzatore di streaming**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Creare un localizzatore di streaming e ottenere un percorso

Al termine della codifica, il passaggio successivo consiste nel rendere disponibile ai client il video nell'asset di output per la riproduzione. A questo scopo, prima di tutto creare un localizzatore di streaming. Successivamente, creare gli URL di streaming che i client possono usare.

### <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Si ottiene una risposta simile alla seguente:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Ottenere i percorsi del localizzatore di streaming

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Si ottiene una risposta simile alla seguente:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Copiare il percorso HLS (HTTP Live Streaming). In questo caso, è `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>Compilare l'URL 

### <a name="get-the-streaming-endpoint-host-name"></a>Ottenere il nome host dell'endpoint di streaming

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
Copiare il valore `hostName`. In questo caso, è `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>Assemblare l'URL

"https:// " + &lt;valore nomehost&gt; + &lt;valore percorso HLS&gt;

Ad esempio:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Testare la riproduzione usando Azure Media Player

> [!NOTE]
> Se un lettore è ospitato in un sito HTTPS, assicurarsi che l'URL inizi con "https".

1. Aprire un Web browser e passare a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL** incollare l'URL compilato nella sezione precedente. È possibile incollare l'URL in formato HLS, Dash o Smooth. Azure Media Player userà automaticamente un protocollo di streaming appropriato per la riproduzione sul dispositivo.
3. Selezionare **Update Player** (Aggiorna il lettore).

>[!NOTE]
>Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'intero contenuto del gruppo di risorse non è più necessario, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse.

Eseguire questo comando dell'interfaccia della riga di comando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali](media-services-overview.md)

