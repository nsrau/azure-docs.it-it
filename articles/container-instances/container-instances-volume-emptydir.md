---
title: Montare un volume emptyDir in Istanze di contenitore di Azure
description: Informazioni su come montare un volume emptyDir per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di contenitore di Azure
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 01b44f357d160a48852ee843cd8bebd138e95dfc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montare un volume emptyDir in Istanze di contenitore di Azure

Informazioni su come montare un volume *emptyDir* per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di contenitore di Azure.

> [!NOTE]
> Il montaggio di un volume *emptyDir* è attualmente limitato ai contenitori di Linux. Microsoft si impegna per rendere disponibili tutte le funzionalità anche per i contenitori Windows, ma nel frattempo è possibile trovare le differenze correnti tra le piattaforme in [Quotas and region availability for Azure Container Instances](container-instances-quotas.md) (Quote e aree disponibili per Istanze di contenitore di Azure).

## <a name="emptydir-volume"></a>Volume emptyDir

Il volume *emptyDir* fornisce una directory scrivibile accessibile per ogni contenitore in un gruppo di contenitori. I contenitori nel gruppo possono leggere e scrivere gli stessi file nel volume, che può essere montato usando lo stesso percorso o percorsi diversi in ogni contenitore.

Alcuni esempi d'uso per un volume *emptyDir*:

* Area scratch
* Checkpoint durante le attività a esecuzione prolungata
* Archiviare i dati recuperati da un contenitore collaterale e fornito da un contenitore applicazione

I dati in un volume *emptyDir* vengono salvati in modo permanente tramite l'arresto anomalo del contenitore. Tuttavia per i contenitori che vengono riavviati non viene garantito il salvataggio in modo permanente dei dati in un volume *emptyDir*.

## <a name="mount-an-emptydir-volume"></a>Montare un volume emptyDir

Per montare un volume emptyDir in un'istanza di contenitore, è necessario eseguire la distribuzione tramite un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo del contenitore del modello. Successivamente per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *emptyDir*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da due contenitori, ognuno dei quali monta il volume *emptyDir*:

[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Per un esempio di distribuzione di istanze di contenitore con un modello di Azure Resource Manager, vedere [Deploy multi-container groups in Azure Container Instances](container-instances-multi-container-group.md) (Distribuire gruppi multicontenitore in istanze di contenitore Azure).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di contenitori di Azure:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di contenitore di Azure)
* [Mount a gitRepo volume in Azure Container Instances](container-instances-volume-gitrepo.md) (Montare un volume gitRepo in Istanze di contenitore di Azure)
* [Mount a secret volume in Azure Container Instances](container-instances-volume-secret.md) (Montare un volume segreto in Istanze di contenitore di Azure)