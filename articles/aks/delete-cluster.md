---
title: Eliminare un cluster Azure Kubernetes Service (AKS)
description: Eliminare un cluster AKS con l'interfaccia della riga di comando o il portale di Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dcebb702695a6601f6ed17b85a04d5bb4e01f6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100044"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Eliminare un cluster Azure Kubernetes Service (AKS)

Quando si elimina un cluster Azure Kubernetes Service, il gruppo di risorse in cui è stato distribuito rimane, ma tutte le risorse correlate ad AKS vengono eliminate. Questo documento mostra come eliminare un cluster AKS usando l'interfaccia della riga di comando e il portale di Azure.

Oltre al cluster, può essere eliminato anche il gruppo di risorse in cui è stato distribuito e di conseguenza il cluster AKS.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az aks delete][az-aks-delete] per eliminare il cluster AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Con il comando `az aks delete` sono disponibili le opzioni seguenti.

| Argomento | DESCRIZIONE | Obbligatoria |
|---|---|:---:|
| `--name``-n` | Nome della risorsa per il cluster gestito. | Sì |
| `--resource-group``-g` | Nome del gruppo di risorse di Azure Kubernetes Service. | Sì |
| `--no-wait` | Indica che non è necessario attendere il termine dell'operazione a esecuzione prolungata. | no |
| `--yes``-y` | Indica che non è richiesta la conferma. | no |

## <a name="azure-portal"></a>Portale di Azure

All'interno del portale di Azure passare al gruppo di risorse contenente la risorsa di Azure Kubernetes Service (AKS), selezionare la risorsa e fare clic su **Elimina**. Viene chiesto di confermare l'operazione di eliminazione.

![Eliminare il portale del cluster AKS](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete