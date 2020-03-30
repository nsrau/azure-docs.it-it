---
title: Bloccare immagini
description: Impostare gli attributi per un'immagine contenitore o un repository in modo che non possa essere eliminato o sovrascritto in un Registro di sistema del contenitore di Azure.Set attributes for a container image or repository so it can't be deleted or overwritten in an Azure container registry.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659697"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloccare un'immagine del contenitore in un registro del contenitore di AzureLock a container image in an Azure container registry

In un Registro di sistema del contenitore di Azure è possibile bloccare una versione dell'immagine o un repository in modo che non possa essere eliminato o aggiornato. Per bloccare un'immagine o un repository, aggiornarne gli attributi usando il comando [az acr repository update][az-acr-repository-update]dell'interfaccia della riga di comando di Azure. 

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure in Azure Cloud Shell o in locale (scelta della versione 2.0.55 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

> [!IMPORTANT]
> Questo articolo non si applica al blocco di un intero Registro di sistema, ad esempio l'uso di impostazioni > blocchi nel portale di Azure o comandi nell'interfaccia della riga di comando di Azure.This article doesn't apply to locking an entire registry, for example, using **Settings > Locks** in the Azure portal, or `az lock` commands in the Azure CLI. Il blocco di una risorsa del Registro di sistema non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nei repository. Il blocco di un Registro di sistema influisce solo sulle operazioni di gestione, ad esempio l'aggiunta o l'eliminazione di repliche o l'eliminazione del Registro di sistema stesso. Per ulteriori informazioni, fare clic su [Bloccare le risorse per impedire modifiche impreviste.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Scenari

Per impostazione predefinita, un'immagine con tag nel Registro di sistema del contenitore di Azure è *modificabile,* pertanto con le autorizzazioni appropriate è possibile aggiornare e inserire ripetutamente un'immagine con lo stesso tag in un Registro di sistema. Le immagini contenitore possono anche essere [eliminate](container-registry-delete.md) in base alle esigenze. Questo comportamento è utile quando si sviluppano immagini ed è necessario mantenere una dimensione per il Registro di sistema.

Tuttavia, quando si distribuisce un'immagine del contenitore nell'ambiente di produzione, potrebbe essere necessaria un'immagine del contenitore *non modificabile.* Un'immagine non modificabile è un'immagine che non è possibile eliminare o sovrascrivere accidentalmente.

Vedere [Consigli per l'assegnazione di tag e il controllo](container-registry-image-tag-version.md) delle versioni delle immagini del contenitore per le strategie per contrassegnare e utilizzare le immagini di versione nel Registro di sistema.

Utilizzare il comando [az acr repository update][az-acr-repository-update] per impostare gli attributi del repository in modo da poter:

* Bloccare una versione dell'immagine o un intero repository

* Proteggere una versione dell'immagine o un repository dall'eliminazione, ma consentire gli aggiornamentiProtect an image version or repository from deletion, but allow updates

* Impedire operazioni di lettura (pull) su una versione dell'immagine o su un intero repository

Per alcuni esempi, vedere le sezioni seguenti. 

## <a name="lock-an-image-or-repository"></a>Bloccare un'immagine o un repository 

### <a name="show-the-current-repository-attributes"></a>Mostrare gli attributi del repository corrente
Per visualizzare gli attributi correnti di un repository, eseguire il seguente comando [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrare gli attributi dell'immagine corrente
Per visualizzare gli attributi correnti di un tag, eseguire il seguente comando [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloccare un'immagine per tag

Per bloccare l'immagine *myrepo/myimage:tag* in *myregistry*, eseguire il seguente comando [az acr repository update:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloccare un'immagine in base al digest

Per bloccare un'immagine *myrepo/myimage* identificata dal digest del `sha256:...`manifesto (hash SHA-256, rappresentato come ), eseguire il comando seguente. Per trovare il digest del manifesto associato a uno o più tag immagine, eseguire il comando [az acr repository show-manifests.][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloccare un repository

Per bloccare il repository *myrepo/myimage* e tutte le immagini in esso contenute, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteggere un'immagine o un repository dall'eliminazioneProtect an image or repository from deletion

### <a name="protect-an-image-from-deletion"></a>Proteggere un'immagine dall'eliminazioneProtect an image from deletion

Per consentire l'aggiornamento ma non l'eliminazione dell'immagine *myrepo/myimage:tag,* eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteggere un repository dall'eliminazioneProtect a repository from deletion

Il comando seguente imposta il repository *myrepo/myimage* in modo che non possa essere eliminato. Le singole immagini possono comunque essere aggiornate o eliminate.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedire le operazioni di lettura in un'immagine o in un repository

Per impedire operazioni di lettura (pull) sull'immagine *myrepo/myimage:tag,* eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Per impedire le operazioni di lettura su tutte le immagini nel repository *myrepo/myimage,* eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Sbloccare un'immagine o un repository

Per ripristinare il comportamento predefinito dell'immagine *myrepo/myimage:tag* in modo che possa essere eliminata e aggiornata, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Per ripristinare il comportamento predefinito del repository *myrepo/myimage* e di tutte le immagini in modo che possano essere eliminate e aggiornate, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come utilizzare il comando [az acr repository update][az-acr-repository-update] per impedire l'eliminazione o l'aggiornamento delle versioni delle immagini in un repository. Per impostare attributi aggiuntivi, vedere az [acr repository update][az-acr-repository-update] command reference(a.

Per visualizzare gli attributi impostati per una versione dell'immagine o un repository, utilizzare il comando [az acr repository show.][az-acr-repository-show]

Per informazioni dettagliate sulle operazioni di eliminazione, vedere [Eliminare immagini di contenitori nel Registro][container-registry-delete]di sistema del contenitore di Azure.For details about delete operations, see Delete container images in Azure Container Registry .

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

