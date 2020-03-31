---
title: Montare il volume emptyDir nel gruppo di contenitori
description: Informazioni su come montare un volume emptyDir per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di Azure Container
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117748"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montare un volume emptyDir in Istanze di Azure Container

Informazioni su come montare un volume *emptyDir* per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di Azure Container. Usare i volumi *emptyDir* come cache effimere per i carichi di lavoro in contenitori.

> [!NOTE]
> Il montaggio di un volume *emptyDir* è attualmente limitato ai contenitori di Linux. Mentre stiamo lavorando per portare tutte le funzionalità nei contenitori di Windows, è possibile trovare le differenze di piattaforma correnti nella [panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>Volume emptyDir

Il volume *emptyDir* fornisce una directory scrivibile accessibile per ogni contenitore in un gruppo di contenitori. I contenitori nel gruppo possono leggere e scrivere gli stessi file nel volume, che può essere montato usando lo stesso percorso o percorsi diversi in ogni contenitore.

Alcuni esempi d'uso per un volume *emptyDir*:

* Area scratch
* Checkpoint durante le attività a esecuzione prolungata
* Archiviare i dati recuperati da un contenitore collaterale e fornito da un contenitore applicazione

I dati in un volume *emptyDir* vengono salvati in modo permanente tramite l'arresto anomalo del contenitore. Tuttavia per i contenitori che vengono riavviati non viene garantito il salvataggio in modo permanente dei dati in un volume *emptyDir*. Se si arresta un gruppo di contenitori, il volume *emptyDir* non viene mantenuto.

La dimensione massima di un volume *Linux emptyDir* è 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montare un volume emptyDir

Per montare un volume emptyDir in un'istanza del contenitore, è possibile eseguire la distribuzione usando un modello di [Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups)un [file YAML](container-instances-reference-yaml.md)o altri metodi a livello di codice per distribuire un gruppo di contenitori.

In primo `volumes` luogo, popolare `properties` la matrice nella sezione del gruppo di contenitori del file. Successivamente per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *emptyDir*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da due contenitori, ognuno dei quali monta il volume *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Per visualizzare esempi di distribuzione di gruppi di contenitori, vedere [Distribuire un gruppo multicontenitore usando un modello](container-instances-multi-container-group.md) di Resource Manager e Distribuire un gruppo [multicontenitore usando un file YAML.](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di Azure Container:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)
* [Montare un volume gitRepo in Istanze di Azure Container](container-instances-volume-gitrepo.md)
* [Montare un volume segreto in Istanze di Azure Container](container-instances-volume-secret.md)