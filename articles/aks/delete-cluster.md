---
title: Eliminare un cluster del servizio contenitore di Azure (AKS)
description: Eliminare un cluster AKS con l'interfaccia della riga di comando o il portale di Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Eliminare un cluster del servizio contenitore di Azure (AKS)

Quando si elimina un cluster del servizio contenitore di Azure, il gruppo di risorse in cui è stato distribuito rimane, ma tutte le risorse correlate a AKS vengono eliminate. Questo documento mostra come eliminare un cluster AKS usando l'interfaccia della riga di comando e il portale di Azure. 

Oltre al cluster, può essere eliminato anche il gruppo di risorse in cui è stato distribuito e di conseguenza il cluster AKS.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az aks delete][az-aks-delete] per eliminare il cluster AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Con il comando `az aks delete` sono disponibili le opzioni seguenti.

| Argomento | DESCRIZIONE | Obbligatoria |
|---|---|:---:|
| `--name``-n` | Il nome della risorsa per il cluster gestito. | Sì |
| `--resource-group``-g` | Il nome del gruppo di risorse del servizio contenitore di Azure. | Sì |
| `--no-wait` | Indica che non è necessario attendere il termine dell'operazione a esecuzione prolungata. | no |
| `--yes``-y` | Indica che non è richiesta la conferma. | no |

## <a name="azure-portal"></a>Portale di Azure

All'interno del portale di Azure passare al gruppo di risorse contenente la risorsa del servizio contenitore di Azure (AKS), selezionare la risorsa e fare clic su **Elimina**. Viene chiesto di confermare l'operazione di eliminazione.

![Eliminare il portale del cluster AKS](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete