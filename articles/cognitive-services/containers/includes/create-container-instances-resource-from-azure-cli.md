---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa dell'istanza di contenitore di Azure dall'interfaccia della riga di comando di Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689430"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Creare una risorsa dell'istanza di contenitore di Azure dall'interfaccia della riga di comando di Azure

Il YAML seguente definisce la risorsa dell'istanza di contenitore di Azure. Copiare e incollare il contenuto in un nuovo file, denominato `my-aci.yaml` e sostituire i valori commentati con quelli personalizzati. Vedere il [formato del modello][template-format] per YAML valido. Vedere i [repository del contenitore e le immagini][repositories-and-images] per i nomi delle immagini disponibili e il repository corrispondente. Per altre informazioni sul riferimento YAML per le istanze di contenitore, vedere informazioni di [riferimento su YAML: istanze di contenitore di Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Non tutti i percorsi hanno la stessa disponibilità di CPU e memoria. Vedere la tabella [location and resources][location-to-resource] per l'elenco delle risorse disponibili per i contenitori per località e sistema operativo.

Si farà affidamento sul file YAML creato per il comando [`az container create`][azure-container-create] . Dall'interfaccia della riga di comando di Azure eseguire il comando `az container create` sostituendo l'`<resource-group>` con il proprio. Inoltre, per proteggere i valori in una distribuzione YAML, fare riferimento a [valori sicuri][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

L'output del comando è `Running...` se valido, dopo qualche minuto l'output viene modificato in una stringa JSON che rappresenta la risorsa ACI appena creata. L'immagine del contenitore è più che probabile che non sia disponibile per un periodo di tempo, ma la risorsa viene ora distribuita.

> [!TIP]
> Prestare particolare attenzione alle posizioni delle offerte di servizi cognitivi di Azure in anteprima pubblica, poiché YAML deve essere modificato di conseguenza in modo da corrispondere al percorso.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
