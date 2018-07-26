---
title: Creare volumi persistenti con Azure Kubernetes Service
description: Informazioni su come usare i dischi di Azure per creare volumi permanenti per i POD in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 14617b57f59c068aa015c9bfea9b4d18520b4152
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38473683"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Creare volumi persistenti con dischi di Azure per Azure Kubernetes Service (AKS)

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso con pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Per altre informazioni sui volumi Kubernetes permanenti, vedere [Kubernetes persistent volumes][kubernetes-volumes] (Volumi Kubernetes permanenti). Questo articolo mostra come usare volumi pesistenti con i dischi di Azure in un cluster di Azure Kubernetes Service (AKS).

> [!NOTE]
> Un disco di Azure può essere montato solo con la *modalità di accesso* *ReadWriteOnce*, che lo rende disponibile solo a un singolo nodo del servizio contenitore di Azure. Se è necessario condividere un volume persistente tra più nodi, provare a usare [File di Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

Ogni cluster AKS include due classi di archiviazione predefinite, entrambe configurate per essere usate con i dischi di Azure. La classe di archiviazione *predefinita* esegue il provisioning di un disco di Azure standard. La classe di archiviazione *gestita Premium* esegue il provisioning di un disco di Azure premium. Se i nodi del servizio contenitore di Azure nel cluster usano l'archiviazione premium, selezionare la classe *gestita Premium*.

Usare il comando [kubectl get sc][kubectl-get] per visualizzare le classi di archiviazione create in precedenza. L'esempio seguente illustra la creazione preliminare di classi di archiviazione disponibile all'interno di un cluster AKS:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Le attestazioni di volumi permanenti sono specificate in GiB, ma i dischi gestiti di Azure vengono fatturati in base al codice SKU per una dimensione specifica. Questi SKU spaziano da 32 GiB per i dischi S4 o P4 a 4 TiB per i dischi S50 o P50. Anche la velocità effettiva e le prestazioni di operazioni di I/O al secondo di un disco gestito Premium dipendono sia dal codice SKU sia dalla dimensione dell'istanza dei nodi nel cluster AKS. Per altre informazioni, vedere [Prezzi e prestazioni dei dischi gestiti][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. In tal caso un'attestazione di volume permanente può usare una delle classi di archiviazione create in precedenza per creare un disco gestito di Azure standard o premium.

Creare un file denominato `azure-premium.yaml` e copiarlo nel manifesto seguente.

Notare che la classe di archiviazione *managed-premium* viene specificata nell'annotazione e che l'attestazione richiede un disco di *5 GB* di dimensione con accesso*ReadWriteOnce*.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Creare l'attestazione di volume permanente con il comando [kubectl apply][kubectl-apply] e specificare il file *azure-premium.yaml*:

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Dopo aver creato l'attestazione di volume permanente e aver eseguito il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod che usa l'attestazione di volume permanente *azure-managed-disk* per montare il disco di Azure nel percorso `/mnt/azure`.

Creare un file denominato `azure-pvc-disk.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Creare il pod con il comando [kubectl apply][kubectl-apply].

```console
kubectl apply -f azure-pvc-disk.yaml
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. Questa configurazione può essere visualizzata durante il controllo del pod tramite `kubectl describe pod mypod`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi permanenti Kubernetes che usano i dischi di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per i dischi di Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
