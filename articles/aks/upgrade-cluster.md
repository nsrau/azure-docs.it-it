---
title: Aggiornare un cluster del servizio contenitore di Azure | Microsoft Docs
description: Aggiornare un cluster del servizio contenitore di Azure
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Kubernetes, Docker, contenitori, microservizi, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: bff0a69d3dac076333de569b2c29af2887e4e1de
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Il servizio contenitore di Azure (AKS) semplifica l'esecuzione di attività di gestione comuni, incluso l'aggiornamento dei cluster Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio contenitore di Azure

Prima di aggiornare un cluster, usare il comando `az aks get-versions` per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Output:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

Sono disponibili tre versioni per l'aggiornamento: 1.7.9, 1.8.1 e 1.8.2. È possibile usare il comando `az aks upgrade` per eseguire l'aggiornamento all'ultima versione disponibile.  Durante il processo di aggiornamento, i nodi vengono accuratamente [contrassegnati come non pianificabili e svuotati](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.  Prima di avviare l'aggiornamento di un cluster, verificare di avere sufficiente capacità di calcolo aggiuntiva per gestire il carico di lavoro perché i nodi del cluster vengono aggiunti e rimossi.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione del servizio contenitore di Azure sono disponibili nelle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio contenitore di Azure](./tutorial-kubernetes-prepare-app.md)