---
title: Usare un disco di Azure con AKS
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 36e25d7e5f1e5c6e1cf72442b73ac081810d216a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumi permanenti con i dischi di Azure

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso con pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Per altre informazioni sui volumi Kubernetes permanenti, vedere [Kubernetes persistent volumes][kubernetes-volumes] (Volumi Kubernetes permanenti).

Questo documento descrive in modo dettagliato l'uso di volumi permanenti con i dischi di Azure in un cluster del servizio contenitore di Azure.

## <a name="built-in-storage-classes"></a>Classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

Ogni cluster AKS include due classi di archiviazione predefinite, entrambe configurate per essere usate con i dischi di Azure. La classe di archiviazione `default` esegue il provisioning di un disco di Azure standard. La classe di archiviazione `managed-premium` esegue il provisioning di un disco di Azure premium. Se i nodi del servizio contenitore di Azure nel cluster usano l'archiviazione premium, selezionare la classe `managed-premium`.

Usare il comando [kubectl get sc][kubectl-get] per visualizzare le classi di archiviazione create in precedenza.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. In tal caso un'attestazione di volume permanente può usare una delle classi di archiviazione create in precedenza per creare un disco gestito di Azure standard o premium.

Creare un file denominato `azure-premimum.yaml` e copiarlo nel manifesto seguente.

Notare che la classe di archiviazione `managed-premium` viene specificata nell'annotazione e l'attestazione richiede un disco di `5GB` con accesso `ReadWriteOnce`. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Creare l'attestazione di volume permanente con il comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

> [!NOTE]
> Un disco di Azure può essere montato solo con la modalità di accesso ReadWriteOnce, che lo rende disponibile solo a un singolo nodo del servizio contenitore di Azure. Se è necessario condividere un volume persistente tra più nodi, provare a usare [File di Azure][azure-files-pvc].

## <a name="using-the-persistent-volume"></a>Uso del volume permanente

Dopo aver creato l'attestazione di volume permanente e aver eseguito il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod che usa l'attestazione di volume permanente `azure-managed-disk` per montare il disco di Azure nel percorso `/mnt/azure`. 

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

Creare il pod con il comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. È possibile vedere il montaggio del volume quando si controlla il pod tramite `kubectl describe pod mypod`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi permanenti Kubernetes che usano i dischi di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per i dischi di Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md