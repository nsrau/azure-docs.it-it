---
title: Aggiornare un cluster del servizio contenitore di Azure
description: Aggiornare un cluster del servizio contenitore di Azure
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 338a153ac4e00c329bf6854306a466657de1d70f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Il servizio contenitore di Azure (AKS) semplifica l'esecuzione di attività di gestione comuni, incluso l'aggiornamento dei cluster Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Prima di aggiornare un cluster, usare il comando `az aks get-upgrades` per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Sono disponibili tre versioni per l'aggiornamento: 1.9.1, 1.9.2 e 1.9.6. È possibile usare il comando `az aks upgrade` per eseguire l'aggiornamento all'ultima versione disponibile.  Durante il processo di aggiornamento, i nodi vengono accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.  Prima di avviare l'aggiornamento di un cluster, verificare di avere sufficiente capacità di calcolo aggiuntiva per gestire il carico di lavoro perché i nodi del cluster vengono aggiunti e rimossi.

> [!NOTE]
> Quando si aggiorna un cluster AKS, le versioni secondarie di Kubernetes non possono essere ignorate. Sono consentiti, ad esempio, aggiornamenti da 1.7.x a 1.8.x o da 1.8.x a 1.9.x, ma non da 1.7 a 1.9.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
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
    "kubernetesVersion": "1.9.6",
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
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nelle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md