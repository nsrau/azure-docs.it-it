---
title: Usare Dischi di Azure con il servizio contenitore di Azure
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e6b2d6e31a843259001a36ffb8c588c879a3f2b9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="volumes-with-azure-disks"></a>Volumi con dischi di Azure

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. È possibile usare dischi di Azure come archivio dati esterno. Questo articolo descrive in modo dettagliato l'uso di un disco di Azure come volume Kubernetes nel cluster del servizio contenitore di Azure.

Per altre informazioni sui volumi Kubernetes, vedere [Kubernetes volumes][kubernetes-volumes] (Volumi Kubernetes).

## <a name="create-an-azure-disk"></a>Creare un disco di Azure

Prima di montare un disco gestito di Azure come volume Kubernetes, il disco deve essere presente nello stesso gruppo di risorse delle risorse del cluster del servizio contenitore di Azure. Per trovare questo gruppo di risorse, usare il comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Il gruppo di risorse da cercare ha un nome simile a `MC_clustername_clustername_locaton`, dove clustername è il nome del cluster del servizio contenitore di Azure e location è l'area di Azure in cui è stato distribuito il cluster.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Usare il comando [az disk create][az-disk-create] per creare il disco di Azure. 

Usando questo esempio, aggiornare `--resource-group` con il nome del gruppo di risorse e `--name` con un nome desiderato.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Al termine della creazione del disco, l'output visualizzato sarà simile al seguente. Questo valore è l'ID disco, usato per montare il disco in un pod Kubernetes.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-file-share-as-volume"></a>Montare la condivisione file come volume

Montare il disco di Azure nel pod configurando il volume nella specifica del contenitore. 

Creare un nuovo file denominato `azure-disk-pod.yaml` con il contenuto seguente. Aggiornare `diskName` con il nome del nuovo disco creato e `diskURI` con l'ID disco. Inoltre, annotare il valore di `mountPath`, che è il percorso in cui il disco di Azure viene montato nel pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Usare kubectl per creare il pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

A questo punto è disponibile un pod in esecuzione con un disco di Azure montato in `/mnt/azure`.

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui volumi Kubernetes usando dischi di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per i dischi di Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
