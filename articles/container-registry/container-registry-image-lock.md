---
title: Bloccare un'immagine in Registro contenitori di Azure
description: Impostare gli attributi per un repository o immagine del contenitore in modo che non può essere eliminato o sovrascritto nel registro contenitori di Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361286"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloccare un'immagine del contenitore in Registro contenitori di Azure

In un registro contenitori di Azure, è possibile bloccare una versione di immagine o un repository in modo che non può essere eliminata o aggiornata. Per bloccare un'immagine o un repository, aggiornare i relativi attributi usando il comando di Azure CLI [aggiornamento di az acr repository][az-acr-repository-update]. 

Questo articolo è necessario eseguire il comando di Azure in Azure Cloud Shell o in locale (versione 2.0.55 o in un secondo momento consigliati). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

## <a name="scenarios"></a>Scenari

Per impostazione predefinita, è un'immagine in Registro contenitori di Azure con tag *modificabile*, in modo che disponga delle autorizzazioni appropriate è possibile aggiornare più volte e push di un'immagine con lo stesso tag a un registro di sistema. Le immagini del contenitore possono rivelarsi [eliminato](container-registry-delete.md) in base alle esigenze. Questo comportamento è utile quando si sviluppano immagini ed è necessario mantenere una dimensione per il registro.

Tuttavia, quando si distribuisce un'immagine del contenitore nell'ambiente di produzione, potrebbe essere necessario un *non modificabile* immagine del contenitore. Un'immagine non modificabile è quello che è Impossibile eliminare o sovrascrivere accidentalmente. Usare la [aggiornamento di az acr repository] [ az-acr-repository-update] comando per impostare gli attributi di repository in modo da poter:

* Bloccare una versione di immagine o un intero repository

* Evitare un repository o la versione dell'immagine di eliminazione, ma consentire gli aggiornamenti

* Evitare operazioni di lettura (pull) su una versione di immagine o un intero repository

Vedere le sezioni seguenti per gli esempi.

## <a name="lock-an-image-or-repository"></a>Bloccare un'immagine o un repository 

### <a name="show-the-current-repository-attributes"></a>Mostrare gli attributi di repository corrente
Per visualizzare gli attributi correnti di un repository, eseguire il comando seguente [show di az acr repository] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrare gli attributi di immagine corrente
Per visualizzare gli attributi di un tag correnti, eseguire il comando seguente [show di az acr repository] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloccare un'immagine di base al tag

Per bloccare il *myrepo / myimage:tag* immagine *myregistry*, eseguire il comando seguente [update di az acr repository] [ az-acr-repository-update] comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloccare un'immagine dal manifesto del digest

Per bloccare un *myrepo/myimage* immagine identificato dal manifesto del digest (hash SHA-256, rappresentati come `sha256:...`), eseguire il comando seguente. (Per trovare il digest del manifesto associato a uno o più tag di immagine, eseguire la [az acr repository show-manifesti] [ az-acr-repository-show-manifests] comando.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloccare un repository

Per bloccare il *myrepo/myimage* repository e tutte le immagini in essa contenuti, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Impedire l'eliminazione di un'immagine o un repository

### <a name="protect-an-image-from-deletion"></a>Impedire l'eliminazione di un'immagine

Per consentire la *myrepo / myimage:tag* immagine deve essere aggiornato ma non è stato eliminato, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Impedire l'eliminazione di un repository

Il comando seguente imposta la *myrepo/myimage* repository in modo che non può essere eliminato. Singole immagini possono ancora essere aggiornate o eliminate.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Evitare operazioni di lettura su un'immagine o un repository

Per impedire operazioni di lettura (pull) nella *myrepo / myimage:tag* immagine, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Per impedire operazioni di lettura in tutte le immagini nel *myrepo/myimage* repository, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Sbloccare un'immagine o un repository

Per ripristinare il comportamento predefinito del *myrepo / myimage:tag* immagine in modo che possa essere eliminato o aggiornato, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Per ripristinare il comportamento predefinito del *myrepo/myimage* repository e tutte le immagini in modo che possano essere eliminate e aggiornate, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, sono stati descritti usando il [aggiornamento di az acr repository] [ az-acr-repository-update] comando per impedire l'eliminazione o l'aggiornamento delle versioni delle immagini in un repository. Per impostare gli attributi aggiuntivi, vedere la [aggiornamento di az acr repository] [ az-acr-repository-update] Guida comandi.

Per visualizzare gli attributi impostati per un repository o la versione dell'immagine, usare il [show di az acr repository] [ az-acr-repository-show] comando.

Per informazioni dettagliate sulle operazioni di eliminazione, vedere [eliminare le immagini del contenitore in Registro contenitori di Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

