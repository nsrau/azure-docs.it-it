---
title: Montare un volume gitRepo in Istanze di contenitore di Azure
description: Informazioni su come montare un volume gitRepo per clonare un repository Git nelle istanze di contenitore
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
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

## <a name="mount-gitrepo-volume-azure-cli"></a>Montare un volume gitRepo: interfaccia della riga di comando di Azure

Per montare un volume gitRepo quando si distribuiscono le istanze di contenitore con l'[interfaccia della riga di comando di Azure](/cli/azure), fornire i parametri `--gitrepo-url` e `--gitrepo-mount-path` al comando [az container create][az-container-create]. Facoltativamente, è possibile specificare la directory del volume in cui eseguire la clonazione (`--gitrepo-dir`) e l'hash commit della revisione da clonare (`--gitrepo-revision`).

Questo comando di esempio clona l'applicazione di esempio [aci-helloworld][aci-helloworld] in `/mnt/aci-helloworld` nell'istanza di contenitore:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Per verificare che il volume gitRepo sia stato montato, avviare una shell nel contenitore con [az container exec][az-container-exec] ed elencare la directory:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montare un volume gitRepo: Resource Manager

Per montare un volume gitRepo quando si distribuiscono le istanze di contenitore con un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), popolare la matrice `volumes` nella sezione `properties` del gruppo di contenitore del modello. Successivamente, per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *gitRepo*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da un solo contenitore. Il contenitore clona due repository GitHub specificati dai blocchi del volume *gitRepo*. Il secondo volume include proprietà aggiuntive che specificano una directory in cui eseguire la clonazione e l'hash commit di una revisione specifica da clonare.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

La struttura della directory risultante dei due repository clonati definiti nel modello precedente è:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Per un esempio di distribuzione di istanze di contenitore con un modello di Azure Resource Manager, vedere [Deploy multi-container groups in Azure Container Instances](container-instances-multi-container-group.md) (Distribuire gruppi multicontenitore in istanze di contenitore Azure).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di contenitore di Azure:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di contenitore di Azure)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Montare un volume emptyDir in Istanze di contenitore di Azure)
* [Mount a secret volume in Azure Container Instances](container-instances-volume-secret.md) (Montare un volume segreto in Istanze di contenitore di Azure)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec