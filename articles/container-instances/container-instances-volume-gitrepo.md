---
title: Montare un volume gitRepo in Istanze di contenitore di Azure
description: Informazioni su come montare un volume gitRepo per clonare un repository Git nelle istanze di contenitore
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montare un volume gitRepo in Istanze di contenitore di Azure

Informazioni su come montare un volume *gitRepo* per clonare un repository Git nelle istanze di contenitore.

> [!NOTE]
> Il montaggio di un volume *gitRepo* è attualmente limitato ai contenitori di Linux. Microsoft si impegna per rendere disponibili tutte le funzionalità anche per i contenitori Windows, ma nel frattempo è possibile trovare le differenze correnti tra le piattaforme in [Quotas and region availability for Azure Container Instances](container-instances-quotas.md) (Quote e aree disponibili per Istanze di contenitore di Azure).

## <a name="gitrepo-volume"></a>Volume gitRepo

Il volume *gitRepo* monta una directory e clona il repository Git specificato al suo interno all'avvio del contenitore. Usando un volume *gitRepo* nelle istanze di contenitore, è possibile evitare di aggiungere il codice per eseguire questa operazione nelle applicazioni.

Quando si monta un volume *gitRepo*, è possibile impostare tre proprietà per configurare il volume:

| Proprietà | Obbligatoria | DESCRIZIONE |
| -------- | -------- | ----------- |
| `repository` | Sì | URL completo, incluso `http://` o `https://`, del repository Git da clonare.|
| `directory` | No  | Directory in cui clonare il repository. Il percorso non deve contenere né iniziare con "`..`".  Se si specifica "`.`", il repository viene clonato nella directory del volume. In caso contrario il repository Git viene clonato in una sottodirectory del nome dato all'interno della directory del volume. |
| `revision` | No  | Hash commit della revisione da clonare. Se non specificato, la revisione `HEAD` viene clonata. |

## <a name="mount-a-gitrepo-volume"></a>Montare un volume gitRepo

Per montare un volume *gitRepo* in un'istanza di contenitore, è necessario eseguire la distribuzione tramite un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo del contenitore del modello. Successivamente, per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *gitRepo*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo del contenitore costituito da un solo contenitore. Il contenitore clona due repository GitHub specificati dai blocchi del volume *gitRepo*. Il secondo volume include proprietà aggiuntive che specificano una directory in cui eseguire la clonazione e l'hash commit di una revisione specifica da clonare.

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

La struttura della directory risultante dei due repository clonati definiti nel modello precedente è:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Per un esempio di distribuzione di istanze di contenitore con un modello di Azure Resource Manager, vedere [Deploy multi-container groups in Azure Container Instances](container-instances-multi-container-group.md) (Distribuire gruppi multicontenitore in istanze di contenitore Azure).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di contenitori di Azure:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di contenitore di Azure)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Montare un volume emptyDir in Istanze di contenitore di Azure)
* [Mount a secret volume in Azure Container Instances](container-instances-volume-secret.md) (Montare un volume segreto in Istanze di contenitore di Azure)
