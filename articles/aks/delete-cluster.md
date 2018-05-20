---
title: Eliminare un cluster Azure Kubernetes Service (AKS)
description: Eliminare un cluster AKS con l'interfaccia della riga di comando o il portale di Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e006466d1450471900a8635c49d3bc6c3a73d476
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
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