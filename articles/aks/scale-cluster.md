---
title: Ridimensionare un cluster del servizio contenitore di Azure
description: Ridimensionare un cluster del servizio contenitore di Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 299eb74686f00dc6d5eb9a1c6127aa134dcd9b77
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Ridimensionare un cluster del servizio contenitore di Azure

Un cluster del servizio contenitore di Azure può essere facilmente ridimensionato in modo da passare a un diverso numero di nodi.  Selezionare il numero di nodi desiderato ed eseguire il comando `az aks scale`.  In caso di riduzione, i nodi verranno accuratamente [contrassegnati come non pianificabili e svuotati](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.  In caso di aumento, il comando `az` attende finché i nodi non vengono contrassegnati come `Ready` dal cluster Kubernetes.

## <a name="scale-the-cluster-nodes"></a>Ridimensionare i nodi del cluster

Per ridimensionare i nodi del cluster, usare il comando `az aks scale`. L'esempio seguente ridimensiona un cluster denominato *myK8SCluster* passando a un singolo nodo.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
```

Output:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nelle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure](./tutorial-kubernetes-prepare-app.md)
