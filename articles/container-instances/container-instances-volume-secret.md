---
title: Montare un volume segreto in Istanze di contenitore di Azure
description: Informazioni su come montare un volume segreto per archiviare informazioni sensibili per l'accesso da parte delle istanze di contenitore
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 6f8e1b6faac11b668a143f8013a198831a428c51
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montare un volume segreto in Istanze di contenitore di Azure

Informazioni su come montare un volume *segreto* nelle istanze di contenitore per l'archiviazione e il recupero delle informazioni sensibili da parte dei contenitori nei gruppi di contenitori.

> [!NOTE]
> Il montaggio di un volume *segreto* è attualmente limitato ai contenitori Linux. Microsoft si impegna per rendere disponibili tutte le funzionalità anche per i contenitori Windows, ma nel frattempo è possibile trovare le differenze correnti tra le piattaforme in [Quotas and region availability for Azure Container Instances](container-instances-quotas.md) (Quote e aree disponibili per Istanze di contenitore di Azure).

## <a name="secret-volume"></a>Volume segreto

È possibile usare un volume *segreto* per inserire informazioni sensibili nei contenitori di un gruppo di contenitori. Il volume *segreto* archivia i segreti specificati nei file all'interno del volume, a cui i contenitori del gruppo contenitore possono quindi accedere. Tramite i segreti in un volume *segreto* è possibile evitare di inserire dati sensibili, quali chiavi SSH o credenziali del database, nel codice dell'applicazione.

Tutti i volumi *segreti* sono supportati da [tmpfs][tmpfs], un file system supportato da RAM; i relativi contenuti non vengono mai scritti in risorse di archiviazione non volatili.

## <a name="mount-a-secret-volume"></a>Montare un volume segreto

Per montare un volume *segreto* in un'istanza di contenitore, è necessario eseguire la distribuzione tramite un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo di contenitori del modello. Successivamente per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *segreto*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da un solo contenitore. Il contenitore monta un volume *segreto* costituito da due segreti con codifica Base64.

[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Per un esempio di distribuzione di istanze di contenitore con un modello di Azure Resource Manager, vedere [Deploy multi-container groups in Azure Container Instances](container-instances-multi-container-group.md) (Distribuire gruppi multicontenitore in istanze di contenitore Azure).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di contenitori di Azure:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di contenitore di Azure)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Montare un volume emptyDir in Istanze di contenitore di Azure)
* [Mount a gitRepo volume in Azure Container Instances](container-instances-volume-gitrepo.md) (Montare un volume gitRepo in Istanze di contenitore di Azure)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs