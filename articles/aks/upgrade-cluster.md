---
title: Aggiornare un cluster del servizio contenitore di Azure
description: Aggiornare un cluster del servizio contenitore di Azure
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: gamonroy
ms.custom: mvc
<<<<<<< HEAD
ms.openlocfilehash: 9b94f858aa896eaa93430a12cd74e12d9bf02008
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
=======
ms.openlocfilehash: 6eaa0128c37d74fd2fd4c4bdb377ca76d7c37669
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
>>>>>>> ef264d3823deace652e9de26708b3ff46548277e
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Il servizio contenitore di Azure (AKS) semplifica l'esecuzione di attività di gestione comuni, incluso l'aggiornamento dei cluster Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Prima di aggiornare un cluster, usare il comando `az aks get-upgrades` per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento.

```azurecli-interactive
<<<<<<< HEAD
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
=======
az aks get-versions --name myAKSCluster --resource-group myResourceGroup --output table
>>>>>>> ef264d3823deace652e9de26708b3ff46548277e
```

Output:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

Sono disponibili tre versioni per l'aggiornamento: 1.7.9, 1.8.1 e 1.8.2. È possibile usare il comando `az aks upgrade` per eseguire l'aggiornamento all'ultima versione disponibile.  Durante il processo di aggiornamento, i nodi vengono accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.  Prima di avviare l'aggiornamento di un cluster, verificare di avere sufficiente capacità di calcolo aggiuntiva per gestire il carico di lavoro perché i nodi del cluster vengono aggiunti e rimossi.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Output:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

È ora possibile verificare che l'aggiornamento sia stato completato correttamente con il comando `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nelle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md