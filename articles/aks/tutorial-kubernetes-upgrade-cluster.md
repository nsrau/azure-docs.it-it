---
title: 'Esercitazione su Kubernetes in Azure: aggiornare un cluster | Microsoft Docs'
description: 'Esercitazione su Kubernetes in Azure: aggiornare un cluster'
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d0193e891c6c41687f1aaa3a8033bc71e85b10c3
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Aggiornare Kubernetes nel servizio contenitore di Azure

È possibile aggiornare un cluster del servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure. Durante il processo di aggiornamento, i nodi Kubernetes verranno accuratamente [contrassegnati come non pianificabili e svuotati](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.

In questa esercitazione, l'ottava parte di otto, viene aggiornato un cluster Kubernetes. Le attività da completare comprendono:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore).


## <a name="get-cluster-versions"></a>Ottenere le versioni del cluster

Prima di aggiornare un cluster, usare il comando `az aks get-versions` per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Come si può vedere, la versione corrente del noto è `1.7.7` e sono disponibili la versione `1.7.9`, `1.8.1`, e `1.8.2`.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Aggiornare il cluster

Usare il comando `az aks upgrade` per aggiornare i nodi del cluster. Negli esempi seguenti il cluster viene aggiornato alla versione `1.8.2`.

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

## <a name="validate-upgrade"></a>Convalidare l'aggiornamento

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

In questa esercitazione è stato aggiornato Kubernetes in un cluster del servizio contenitore di Azure. Sono state completate le attività seguenti:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

Seguire questo collegamento per altre informazioni sul servizio contenitore di Azure.

> [!div class="nextstepaction"]
> [Panoramica del servizio contenitore di Azure](./intro-kubernetes.md)