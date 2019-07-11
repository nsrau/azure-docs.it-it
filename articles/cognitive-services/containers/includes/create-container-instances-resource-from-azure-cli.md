---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di istanza di contenitore di Azure della riga di comando di Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717057"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Creare una risorsa di istanza di contenitore di Azure della riga di comando di Azure

Il codice YAML seguente definisce la risorsa dell'istanza di contenitore di Azure. Copiare e incollare il contenuto in un nuovo file denominato `my-aci.yaml` e sostituire i valori con commenti con quelli personalizzati. Fare riferimento a [formato del modello] [modello-formattare] per YAML valido. Vedere la [repository di contenitori e immagini][repositories-and-images] per i nomi delle immagini disponibili e i repository corrispondenti.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> Non tutte le località hanno la stessa disponibilità di CPU e memoria. Vedere le [posizione e le risorse][location-to-resource] tabella per l'elenco delle risorse disponibili per i contenitori per ogni posizione e il sistema operativo.

Ci sarà si affida il file YAML creati per il [ `az container create` ][azure-container-create] comando. Della riga di comando di Azure, eseguire la `az container create` comandi sostituendo le `<resource-group>` con quelli personalizzati. Inoltre, per la protezione dei valori all'interno di un YAML distribuzione si intende [proteggere i valori][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

L'output del comando è `Running...` se valido, attendere qualche minuto il cambiamento dell'output in una stringa JSON che rappresenta la risorsa ACI appena creata. L'immagine del contenitore è più probabile che non sia disponibile per un periodo di tempo, ma è stata distribuita la risorsa.

> [!TIP]
> Prestare particolare attenzione alle posizioni delle offerte di servizi cognitivi di Azure versione di anteprima pubblica, come il codice YAML saranno necessari per essere adattato per corrispondere al percorso.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values