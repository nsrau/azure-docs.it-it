---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa istanza del contenitore di Azure dall'interfaccia della riga di comando di Azure.Learn how to create an Azure container instance resource from the Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689430"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Creare una risorsa Istanza contenitore di Azure dall'interfaccia della riga di comando di AzureCreate an Azure Container Instance resource from the Azure CLI

Il valore YAML riportato di seguito definisce la risorsa Istanza contenitore di Azure.The YAML below defines the Azure Container Instance resource. Copiare e incollare il contenuto `my-aci.yaml` in un nuovo file, denominato e sostituire i valori commentati con il proprio. Fare riferimento al formato del [modello][template-format] per YAML valido. Fare riferimento [ai repository contenitore e][repositories-and-images] alle immagini per i nomi di immagine disponibili e il repository corrispondente. Per altre informazioni sul riferimento YAML per le istanze del contenitore, vedere [Riferimento A YAML: Istanze del contenitore][aci-yaml-ref]di Azure.For more information of the YAML reference for Container instances, see YAML reference: Azure Container Instances .

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
> Non tutte le posizioni hanno la stessa disponibilità di CPU e memoria. Fare riferimento alla tabella [del percorso e][location-to-resource] delle risorse per l'elenco delle risorse disponibili per i contenitori per posizione e sistema operativo.

Ci affidiamo al file YAML [`az container create`][azure-container-create] che abbiamo creato per il comando. Dall'interfaccia della riga `az container create` di comando `<resource-group>` di Azure eseguire il comando sostituendo il comando con il proprio. Inoltre, per proteggere i valori all'interno di una distribuzione YAML, fare riferimento a [valori protetti][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

L'output del `Running...` comando è se valido, dopo qualche tempo l'output viene modificato in una stringa JSON che rappresenta la risorsa ACI appena creata. L'immagine del contenitore è più che probabile che non sia disponibile per un po', ma la risorsa è ora distribuita.

> [!TIP]
> Prestare molta attenzione alle posizioni delle offerte di servizio cognitivo di Azure di anteprima pubblica, poiché YAML dovrà essere regolato di conseguenza per corrispondere alla posizione.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
