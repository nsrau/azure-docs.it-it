---
title: Montare il volume emptyDir al gruppo di contenitori
description: Informazioni su come montare un volume emptyDir per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di Azure Container
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117748"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montare un volume emptyDir in Istanze di Azure Container

Informazioni su come montare un volume *emptyDir* per condividere dati tra i contenitori in un gruppo di contenitori in Istanze di Azure Container. Usare i volumi *emptyDir* come cache effimera per i carichi di lavoro in contenitori.

> [!NOTE]
> Il montaggio di un volume *emptyDir* è attualmente limitato ai contenitori di Linux. Mentre stiamo lavorando per riunire tutte le funzionalità nei contenitori di Windows, è possibile trovare le attuali differenze della piattaforma nella [Panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>Volume emptyDir

Il volume *emptyDir* fornisce una directory scrivibile accessibile per ogni contenitore in un gruppo di contenitori. I contenitori nel gruppo possono leggere e scrivere gli stessi file nel volume, che può essere montato usando lo stesso percorso o percorsi diversi in ogni contenitore.

Alcuni esempi d'uso per un volume *emptyDir*:

* Area scratch
* Checkpoint durante le attività a esecuzione prolungata
* Archiviare i dati recuperati da un contenitore collaterale e fornito da un contenitore applicazione

I dati in un volume *emptyDir* vengono salvati in modo permanente tramite l'arresto anomalo del contenitore. Tuttavia per i contenitori che vengono riavviati non viene garantito il salvataggio in modo permanente dei dati in un volume *emptyDir*. Se si arresta un gruppo di contenitori, il volume *emptyDir* non viene reso permanente.

Le dimensioni massime di un volume di *EmptyDir* Linux sono di 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montare un volume emptyDir

Per montare un volume emptyDir in un'istanza di contenitore, è possibile eseguire la distribuzione usando un [modello Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), un [file YAML](container-instances-reference-yaml.md)o altri metodi programmatici per distribuire un gruppo di contenitori.

Per prima cosa, `volumes` popolare la matrice nella sezione `properties` del gruppo di contenitori del file. Successivamente per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *emptyDir*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da due contenitori, ognuno dei quali monta il volume *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Per esempi di distribuzione del gruppo di contenitori, vedere [distribuire un gruppo di contenitori multicontenitore usando un modello di gestione risorse](container-instances-multi-container-group.md) e [distribuire un gruppo multicontenitore usando un file YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di Azure Container:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)
* [Montare un volume gitRepo in Istanze di Azure Container](container-instances-volume-gitrepo.md)
* [Montare un volume segreto in Istanze di Azure Container](container-instances-volume-secret.md)