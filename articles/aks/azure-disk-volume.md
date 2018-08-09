---
title: Usare Dischi di Azure con il servizio contenitore di Azure
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: aa9b92df84a48ef4cb706e9e89e0f6c0a25cd42a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420493"
---
# <a name="volumes-with-azure-disks"></a>Volumi con dischi di Azure

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. È possibile usare dischi di Azure come archivio dati esterno. Questo articolo descrive in modo dettagliato l'uso di un disco di Azure come volume Kubernetes nel cluster di Azure Kubernetes Service (AKS).

Per altre informazioni sui volumi Kubernetes, vedere [Kubernetes volumes][kubernetes-volumes] (Volumi Kubernetes).

## <a name="create-an-azure-disk"></a>Creare un disco di Azure

Prima di montare un disco gestito di Azure come volume Kubernetes, il disco deve essere presente nel gruppo di risorse del **nodo** del servizio contenitore di Azure. Ottenere il nome del gruppo di risorse con il comando [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Usare il comando [az disk create][az-disk-create] per creare il disco di Azure.

Aggiornare `--resource-group` con il nome del gruppo di risorse ottenuto nell'ultimo passaggio e `--name` con il nome desiderato.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Al termine della creazione del disco, l'output visualizzato sarà simile al seguente. Questo valore è l'ID disco, usato per montare il disco.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> I dischi gestiti di Azure vengono fatturati per SKU in base alle dimensioni specifiche. Questi SKU spaziano da 32 GiB per i dischi S4 o P4 a 4 TiB per i dischi S50 o P50. Anche la velocità effettiva e le prestazioni delle operazioni di I/O al secondo di un disco gestito Premium dipendono sia dal codice SKU, sia dalle dimensioni dell'istanza dei nodi nel cluster AKS. Vedere [Prezzi per Managed Disks][managed-disk-pricing-performance].

> [!NOTE]
> Per creare il disco in un gruppo di risorse distinto è necessario aggiungere anche l'entità servizio di Azure Kubernetes Service (AKS) per il cluster al gruppo di risorse che contiene il disco con il ruolo `Contributor`. 
>

## <a name="mount-disk-as-volume"></a>Montare il disco come volume

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
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
