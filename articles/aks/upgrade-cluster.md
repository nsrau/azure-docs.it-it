---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Aggiornare un cluster del servizio Azure Kubernetes
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4ff2b56afc4496b6344735b4e3c813b06cee17e3
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141996"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Il servizio Azure Kubernetes semplifica l'esecuzione di attività di gestione comuni, ad esempio l'aggiornamento dei cluster Kubernetes.

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

Sono disponibili tre versioni per l'aggiornamento: 1.9.1, 1.9.2 e 1.9.6. È possibile usare il comando `az aks upgrade` per eseguire l'aggiornamento all'ultima versione disponibile.  Durante il processo di aggiornamento, il servizio contenitore di Azure aggiungerà un nuovo nodo al cluster, quindi [bloccherà e svuoterà][kubernetes-drain] attentamente un nodo alla volta per ridurre al minimo le interruzioni delle applicazioni in esecuzione.

> [!NOTE]
> Quando si aggiorna un cluster AKS, le versioni secondarie di Kubernetes non possono essere ignorate. Sono consentiti, ad esempio, aggiornamenti da 1.8.x a 1.9.x o da 1.9.x a 1.10.x, ma non da 1.8 a 1.10. Per eseguire l'aggiornamento da 1.8 a 1.10, eseguirlo prima da 1.8 a 1.9, quindi eseguire un altro aggiornamento da 1.9 a 1.10

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

Verificare che l'aggiornamento sia stato completato correttamente con il comando `az aks show`.

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
