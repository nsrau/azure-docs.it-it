---
title: Bloccare immagini
description: Impostare gli attributi per un'immagine del contenitore o un repository in modo che non possa essere eliminato o sovrascritto in un registro contenitori di Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 9e55a6688be9f51f1c1b237ae86bd57692a86592
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456321"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloccare un'immagine del contenitore in un registro contenitori di Azure

In un registro contenitori di Azure è possibile bloccare una versione dell'immagine o un repository in modo che non possa essere eliminato o aggiornato. Per bloccare un'immagine o un repository, aggiornarne gli attributi usando il comando dell'interfaccia della riga di comando di Azure [AZ ACR repository update][az-acr-repository-update]. 

Per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure in Azure Cloud Shell o in locale (versione 2.0.55 o successiva consigliata). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!IMPORTANT]
> Questo articolo non si applica al blocco di un intero registro, ad esempio usando **le impostazioni > i blocchi** nel portale di Azure o `az lock` comandi nell'interfaccia della riga di comando di Azure. Il blocco di una risorsa del registro di sistema non impedisce la creazione, l'aggiornamento o l'eliminazione dei dati nei repository. Il blocco di un registro influiscono solo sulle operazioni di gestione, ad esempio l'aggiunta o l'eliminazione di repliche o l'eliminazione del registro di sistema. Altre informazioni in [bloccare le risorse per impedire modifiche impreviste](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="scenarios"></a>Scenari

Per impostazione predefinita, un'immagine con tag in Azure Container Registry è *modificabile*, quindi, con le autorizzazioni appropriate, è possibile aggiornare ripetutamente ed effettuare il push di un'immagine con lo stesso tag a un registro. Le immagini del contenitore possono anche essere [eliminate](container-registry-delete.md) in base alle esigenze. Questo comportamento è utile quando si sviluppano immagini ed è necessario mantenere una dimensione per il registro.

Tuttavia, quando si distribuisce un'immagine del contenitore in produzione, potrebbe essere necessaria un'immagine del contenitore non *modificabile* . Un'immagine non modificabile non può essere eliminata o sovrascritta accidentalmente. Usare il comando [AZ ACR repository update][az-acr-repository-update] per impostare gli attributi del repository, in modo da poter:

* Bloccare una versione dell'immagine o un intero repository

* Proteggere una versione dell'immagine o un repository dall'eliminazione, ma consentire gli aggiornamenti

* Impedisci le operazioni di lettura (pull) su una versione dell'immagine o un intero repository

Per esempi, vedere le sezioni seguenti.

## <a name="lock-an-image-or-repository"></a>Bloccare un'immagine o un repository 

### <a name="show-the-current-repository-attributes"></a>Mostra gli attributi del repository corrente
Per visualizzare gli attributi correnti di un repository, eseguire il comando [AZ ACR repository Show][az-acr-repository-show] seguente:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostra gli attributi dell'immagine corrente
Per visualizzare gli attributi correnti di un tag, eseguire il comando [AZ ACR repository Show][az-acr-repository-show] seguente:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Blocca un'immagine in base al tag

Per bloccare l'immagine repository */Image: Tag* nel registro di *sistema*, eseguire il comando [AZ ACR repository update][az-acr-repository-update] seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Blocca un'immagine in base al digest del manifesto

Eseguire il comando seguente per bloccare un'immagine di *repository/immagine* identificata dal digest del manifesto (hash SHA-256, rappresentato come `sha256:...`). (Per trovare il digest del manifesto associato a uno o più tag di immagine, eseguire il comando [AZ ACR repository Show-manifests][az-acr-repository-show-manifests] ).

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloccare un repository

Per bloccare il repository repository */immagine* e tutte le immagini al suo interno, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteggi un'immagine o un repository dall'eliminazione

### <a name="protect-an-image-from-deletion"></a>Proteggi un'immagine dall'eliminazione

Per consentire l'aggiornamento dell'immagine *repository/Image: Tag* ma non eliminata, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteggi un repository dall'eliminazione

Il comando che segue imposta il repository repository */immagine* in modo che non possa essere eliminato. Le singole immagini possono comunque essere aggiornate o eliminate.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedisci le operazioni di lettura su un'immagine o un repository

Per impedire l'esecuzione di operazioni di lettura (pull) nell'immagine dei *tag repository/Image:* , eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Per impedire l'esecuzione di operazioni di lettura su tutte le immagini del repository repository */immagine* , eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Sbloccare un'immagine o un repository

Per ripristinare il comportamento predefinito dell'immagine del *tag repository/immagine:* in modo che possa essere eliminata e aggiornata, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Per ripristinare il comportamento predefinito del repository repository */immagine* e tutte le immagini in modo che possano essere eliminate e aggiornate, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare il comando [AZ ACR repository update][az-acr-repository-update] per impedire l'eliminazione o l'aggiornamento delle versioni delle immagini in un repository. Per impostare attributi aggiuntivi, vedere la Guida di riferimento al comando [AZ ACR repository update][az-acr-repository-update] .

Per visualizzare gli attributi impostati per una versione o un repository di immagini, usare il comando [AZ ACR repository Show][az-acr-repository-show] .

Per informazioni dettagliate sulle operazioni di eliminazione, vedere [eliminare immagini del contenitore in Azure container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

